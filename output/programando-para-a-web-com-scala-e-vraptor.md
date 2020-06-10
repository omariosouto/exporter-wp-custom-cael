---
title: "Programando para a web com Scala e VRaptor"
date: "2011-01-18"
author: "pmatiello"
authorEmail: "pedro.matiello@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Na Caelum temos experimentado a combinação de Java com Scala para projetos em produção, onde seu poder funcional e a desburocratização da sintaxe nos permite escrever um código mais elegante e reduzido. Exemplos disso são encontrados no post escrito pelo Rafael Ferreira em [Scala realmente pode ser a sua próxima linguagem](https://blog.caelum.com.br/scala-sua-proxima-linguagem/) e, no mais recente escrito pelo Thadeu Russo, [Modelando as classes do FJ-16 em Scala](https://blog.caelum.com.br/modelando-as-classes-do-fj-16-em-scala/).

![](https://blog.caelum.com.br/wp-content/uploads/2011/01/scala.png "scala")

Além das vantagens citadas acima, um outro ponto bastante interessante das linguagens escritas sobre a JVM, é a possibilidade de usar tudo que já está pronto no Java de dentro da nova linguagem. Por exemplo, podemos usar qualquer classe previamente criada no Java dentro de um projeto usando Scala. Para exemplificar a mistura das linguagens, escolhemos migrar uma parte do projeto exemplo do VRaptor, o [Mydvds](http://vraptor.caelum.com.br/download.jsp).

Em primeiro lugar, alteramos um pouco os controllers responsáveis por administrar os usuários, nesse caso aproveitando apenas a sintaxe mais enxuta do Scala para conseguirmos ter as mesmas funcionalidades, porém escrevendo menos código. Segue um trecho da classe:

\[scala\] @Resource class UsersController(dao:UserDao, userInfo:UserInfo, result:Result, validator:Validator) { @Path(Array("/users/{user.login}")) def view(user:User) = dao.find(user.getLogin)

@Path(Array("/users")) def list = { result include("users",dao.listAll) } } \[/scala\]

Já fazendo uso da sintaxe mais reduzida, a declaração do construtor ficou bem mais enxuta, com os parâmetros automaticamente sendo disponibilizados como atributos do objeto. A mesma redução acontece na declaração do método _view_, onde o tipo de retorno é inferido automaticamente e, como o método só tem uma linha, podemos omitir a abertura das chaves. Sempre é válido lembrar que Scala é estaticamente tipado da mesma maneira que Java. Programando em alguma IDE com suporte a linguagem, podemos ver em tempo de compilação que esse método retornará um objeto do tipo _User_.

Depois que a lógica é executada, o VRaptor, seguindo a convenção, vai direcionar para o jsp responsável por exibir a  listagem. Poderiamos até usar os JSPs como camada de visualização para nossa aplicação, mas o problema seria ficar preso às restrições impostas pela Expression Language (EL) e ao uso das _taglibs_ padrões do Java. Por exemplo, para exibir a listagem seria necessário uma java.util.Collection ou de um Array para que a tag _foreach_ da JSTL funcione. Aqui há uma incompatibilidade pois as coleções do Scala foram implementadas do zero, sem nenhum vínculo com as coleções do Java. Outra incompatibilidade é em relação as classes de dominio, se estas forem criadas em Scala, por default não são criados getters no estilo Java e com isso não conseguimos chamar os métodos a partir dos JSPs.

Para explorar o poder do Scala na view, foi usado o [Scalate](http://scalate.fusesource.org/), um projeto para camada de visualização focado em tirar proveito do melhor que a linguagem tem para oferecer. Abaixo segue a página de listagem dos usuarios implementada com Scalate:

\[scala\] <%@val users:Buffer\[User\]%> <% render("/header.ssp") %>

<h1>${"list\_users".i18n}</h1> <table> #for(user <- users) <tr> <td><a href="${"users".url}/${user.getLogin}">${"view".i18n}</a></td> <td>${user.getName}</td> </tr> #end

</table> <% render("/footer.ssp") %> \[/scala\]

O primeiro detalhe diferente para quem está acostumado com os JSPs é a necessidade de declarar as variáveis que estão disponíveis no request para serem usadas dentro da página. O Scalate transforma isso numa classe em Scala para que depois possa compilar o código para ser otimizado pela JVM, evitando o uso de _reflection_.

Outro ponto interessante é a sintaxe utilizada para executar os trechos dinâmicos. O Scalate vem com suporte a diversas engines de renderização. No exemplo, estamos utilizando a chamada SSP (Scala Server Pages), que tem sintaxe similar ao Velocity. Um exemplo foi o _for_ realizado no código acima. Outro ponto interessante de ser notado é o seguinte trecho de código:

\[scala\] ${"users".url} \[/scala\]

Para conseguir pegar a url relativa ao contexto nas páginas dentro de uma JSP, faríamos uso da tag _<c:url>_. Mas no SSP estamos chamando o método _url_ a partir de uma `String`. O problema é que na `String` não existe o método _url,_ e mesmo assim o código funciona: a idéia foi usar o poder de [implicit methods](http://www.artima.com/weblogs/viewpost.jsp?thread=179766) para adicionar comportamentos em classes que já existem sem que necessariamente seja preciso alterá-las, como é o caso da classe `String`.

Para que o método _url_ possa ser invocado através de uma `String`, seria necessário a importação da classe que contém essa extensão de comportamento. Mas, como declarar váriaveis não é algo comumente feito por programadores que utilizam JSP, o Scalate disponibiliza uma maneira de importar classes e disponibilizar helpers para as views, sem que seja preciso de fato declarar a importação na SSP. Para isso, o programador simplesmente cria uma classe chamada **Boot** em um pacote chamado **scalate** e a engine a carrega automaticamente. Abaixo o código:

\[scala\] package scalate

// imports omitidos

class Boot(engine: TemplateEngine,context:ServletContext){ def run: Unit = { engine.importStatements  ::= "import scalate.VraptorHelper.\_" engine.importStatements  ::= "import scalate.PimpedJSTLCore" engine.importStatements  ::= "import br.com.caelum.vraptor.mydvds.model.\_" engine.importStatements  ::= "import scala.collection.mutable.Buffer" } }

object VraptorHelper{ implicit def string2PimpedJSTLCore(str:String) = new PimpedJSTLCore(str) }

class PimpedJSTLCore(str:String){ def url = request.getServletContext.getContextPath +"/"+ str } \[/scala\]

Repare que, através do método _importStatements_, disponibilizamos classes para serem utilizadas em qualquer página que for ser escrita, sem a necessidade dos imports. No exemplo acima, disponibilizamos as classes do pacote **model** para todas SSPs, já que é bem comum fazermos uso dessas classes nas views.

Além disso, o  método _string2PimpedJSTLCore_ responsável pela mágica do método _url_ na classe `String`.  Aqui é definido um método que consegue instanciar um objeto do tipo **PimpedJSTLCore** para qualquer `String` que chame o método _url_. Dessa maneira é possível simular uma adição de comportamento numa classe previamente escrita sem que precisemos promover alguma alteração na original.

Para quem quer aventurar-se na mistura das linguagens, como pregado pelos [programadores poliglotas](http://www.programadorpoliglota.com.br), o VRaptor já conta com um plugin que contorna alguns detalhes dessa integração. Para quem pensa em fazer uma migração, todos os SSPs devem ser colocados dentro da pasta _ssp_, seguindo a mesma estrutura da pasta _jsp_ convencionada pelo VRaptor.
