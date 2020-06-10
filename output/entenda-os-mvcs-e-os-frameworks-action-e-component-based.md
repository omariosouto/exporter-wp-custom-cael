---
title: "Entenda os MVCs e os frameworks Action e Component Based"
date: "2012-07-03"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

_É muito comum encontrar desenvolvedores que aprendem componentes do JSF como o Primefaces e o Richfaces e gostariam de usá-las em seus projetos que utilizam frameworks web como o Spring MVC, Struts e VRaptor, porém, isso não é possível. Por que? **Entenda os diferentes funcionamentos do MVC, aprenda como funcionam os frameworks e tire para sempre essa e outras dúvidas!**_

**Um @Controller do SpringMVC**

Quando trabalhamos com o [SpringMVC](http://static.springsource.org/spring/docs/3.0.x/spring-framework-reference/html/mvc.html), tratamos as requisições enviadas pelos usuários dentro de classes conhecidas como `Controller`. É uma classe simples, anotada com `@Controller` onde implementamos métodos que tratam a requisição para uma determinada URL, indicada através da anotação `@RequestMapping.`

\[code language="java"\] @Controller public class ProdutosController { @RequestMapping("adicionaProduto") public void adiciona(Produto p, HttpServletRequest req) { // pega o produto recebido no request e grava no banco de dados } } \[/code\]

Quando a requisição é submetida para o endereço `adicionaProduto` e envia os parâmetros adequados para preencher o objeto `Produto`, ele é gravado no banco. Porém, precisamos devolver uma resposta para o usuário, que deverá conter o nome do produto adicionado, então podemos disponibilizar um atributo ao JSP através do `request` que recebemos no nosso método.

\[code language="java"\] @RequestMapping("adicionaProduto") public void adiciona(Produto p, HttpServletRequest req) { // pega o produto recebido no request e grava no banco de dados req.setAttribute("nome", p.getNome()); } \[/code\]

Então logo após a execução da ação, indicamos para o SpringMVC que o `produto-adicionado.jsp` deve ser mostrado. Para isso, basta fazermos nosso método retornar uma `String` com o nome do JSP.

\[code language="java"\] @RequestMapping("adicionaProduto") public String adiciona(Produto p) { // pega o produto recebido no request e grava no banco de dados req.setAttribute("nome", p.getNome()); return "produto-adicionado"; } \[/code\]

Dessa forma, após a execução, o JSP será exibido para o usuário.

\[code language="html"\] <h2>Produto ${nome} adicionado com sucesso</h2> \[/code\]

**Onde está o MVC aí?**

O mais importante nisso tudo, é notar que existe uma separação clara entre as responsabilidades no tratamento da requisição.

Quando um usuário submete uma requisição para o endereço `http://localhost:8080/projeto/adicionaProduto`, o SpringMVC a recebe e descobre, de alguma maneira, qual é o `@Controller` e qual método deve ser responsável por tratá-la. Nesse caso, o SpringMVC está fazendo o papel do Front Controller, que delega a responsabilidade para um `Controller` secundário, no caso a classe `ProdutosController` e o método `adiciona`. Chamamos o responsável por esse trabalho de **Controlador**.

\[caption id="attachment\_5130" align="aligncenter" width="480"\][![](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_push_1.png "mvc_push_1")](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_push_1.png) Os passos do controlador\[/caption\]

O próximo passo é a execução das regras de negócio, que podem envolver a execução da persistência das informações, validações e outras tarefas que dizem respeito aos requisitos funcionais da aplicação. Chamamos as classes que fazem esse trabalho de **Modelo**.

\[caption id="attachment\_5135" align="aligncenter" width="480"\][![](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_push_2.png "mvc_push_2")](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_push_2.png) @Controller invoca o Modelo para realizar as regras de negócio\[/caption\]

Após a execução do Modelo e de todas as as regras de negócio, o JSP contendo a resposta adequada deve ser exibido para o usuário. Nesse ponto, temos as regras de visualização implementadas, como por exemplo, exibir as informações em vermelho, dada alguma característica. Essas regras ficam todas centralizadas no JSP, que nesse caso, faz um papel que chamamos de **Visão**, ou seja, é o responsável pelo que o usuário irá visualizar como resultado.

\[caption id="attachment\_5134" align="aligncenter" width="480"\][![](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_push_3.png "mvc_push_3")](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_push_3.png) @Controller indica qual visão será devolvida para o usuário\[/caption\]

Juntando tudo o que vimos, temos o MVC - Modelo, Visão e Controlador, cujo objetivo é separar as responsabilidades da aplicação, fazendo com que tenhamos um código mais fácil de ser mantido.

**Os frameworks Action Based e o MVC Push**

No exemplo anterior, os dados que precisam ser disponbilizados para a Visão fazer a exibição são "empurrados" do `@Controller`. Quando isso acontece, dizemos que estamos usando um estilo de MVC chamado _MVC Push_, ou seja, que empurra os dados para a Visão utilizar. Atualmente, frameworks que seguem essa estratégia são conhecidos como _Action Based_ (Baseado em Ações).

Esse estilo de MVC é utilizado na grande maioria dos frameworks web, como o SpringMVC (visto aqui), [Struts](http://struts.apache.org/), [VRaptor](http://www.vraptor.com.br), [ASP.NET MVC](http://www.asp.net/mvc) e inclusive o [Rails](http://rubyonrails.org/).

**E quando eu trabalho com JSF não acontece a mesma coisa?**

Considere uma listagem simples de produtos em uma aplicação [JSF](http://www.oracle.com/technetwork/java/javaee/javaserverfaces-139869.html), na qual precisaríamos ter o XHTML, chamado `produtos.xhtml`, para mostrar os dados através de um simples `h:dataTable`.

\[code language="html"\] <h:dataTable value="#{produtosBean.produtos}" var="p"> <h:column>#{p.nome}</h:column> </h:dataTable> \[/code\]

Quando acessamos em nosso navegador `http://localhost:8080/projeto/produtos.xhtml`, novamente o framework, no caso o JSF, é quem recebe a requisição e desencadeia todo um processo para tratá-la de maneira adequada.

**O fluxo no JSF e como se encaixa no MVC**

No momento em que recebe a requisição ele inicia o processamento do xhtml, o que no nosso caso, logo incidirá na avaliação do `h:dataTable`, que depende de uma lista de produtos, indicada pela Expression Language `#{produtosBean.produtos}`. Nesse momento, o processamento está na Visão.

\[caption id="attachment\_5131" align="aligncenter" width="264"\][![](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_pull_1.png "mvc_pull_1")](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_pull_1.png) Fluxo no JSF começa pela Visão\[/caption\]

A lista de produtos se encontra num Managed Bean, que possui o seu método `getProdutos` invocado pelo JSF, indicado através do parâmetro `value` do `h:dataTable`, para que consiga recuperá-la do banco de dados ou do lugar adequado:

\[code language="java"\] @ManagedBean public class ProdutosBean { // atributos e demais métodos public List getProdutos() { return repositorio.produtos(); } } \[/code\]

Nesse caso, o `Managed Bean` quando solicitado pela Visão como foi no caso do `value` do `h:dataTable`, busca junto às suas classes os objetos solicitados, novamente, invocando regras de persistência, de validação e outros. Estamos diante do Modelo da nossa aplicação.

\[caption id="attachment\_5132" align="aligncenter" width="480"\][![](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_pull_2.png "mvc_pull_2")](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_pull_2.png) Visão pede pro Managed Bean que invoca o Modelo\[/caption\]

Os objetos recuperados pelo `Managed Bean` são enfim dados à Visão que o solicitou, para que o processamento dela possa continuar e ao final de tudo devolver a resposta para o usuário.

\[caption id="attachment\_5133" align="aligncenter" width="480"\][![](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_pull_3.png "mvc_pull_3")](https://blog.caelum.com.br/wp-content/uploads/2030/12/mvc_pull_3.png) Managed Bean devolve os dados para a Visão, que continua seu processamento. Ao final, o resultado é mostrado para o usuário.\[/caption\]

**Os frameworks Component Based e o MVC Pull**

Quando usamos o JSF, a requisição cai primeiro no Controlador, o JSF, que inicia o processamento pela Visão, que quando necessário, **pega** os dados através do `Managed Bean` e invoca as classes necessárias do Modelo e por fim, disponibiliza os dados necessários para a Visão poder continuar o processamento.

Repare que nesse caso, é a Visão quem solicita os objetos necessários para a renderização, ela "puxa" os dados através do `Managed Bean`. Esse estilo de MVC ficou conhecido como _MVC Pull_ e hoje em dia é mais conhecido por _Component Based_ (Baseado em Componentes), uma categoria que possui frameworks como o JSF e os menos conhecidos [Wicket](http://wicket.apache.org/) e [Tapestry](http://tapestry.apache.org/).

**Diferenças, vantagens e desvantagens**

Há uma clara diferença entre o ciclo de execução de ambos os estilos de MVC, o que inviabiliza, por exemplo, o uso de qualquer biblioteca de componentes do JSF com um framework como o Spring MVC, afinal seu controlador não está preparado e nem foi feito para que a Visão lhe solicite informações.

Quando usamos o MVC Pull, temos um maior acoplamento entre a Visão e o framework MVC, o que pode ser um ponto negativo, porém abre portas para a criação de componentes ricos da tela controlados via código Java.

Já no MVC Push temos o inverso. Conseguimos um grande desacoplamento entre a Visão, mas perdemos a possibilidade de termos componentes ricos controlados por código Java.

Na Caelum, durante o curso [FJ-21](http://www.caelum.com.br/curso/fj-21-java-web/) estudamos o MVC Push, através do SpringMVC e no [FJ-26](http://www.caelum.com.br/curso/fj-26-java-web-jsf2-cdi/), estudamos o JSF e o MVC Pull. No [curso de arquitetura](http://www.caelum.com.br/curso/fj-91-arquitetura-design-projetos-java/) (assim como [neste livro](http://www.arquiteturajava.com.br/)) temos boas discussões sobre o uso ou não de cada um dos estilos.

A [Editora Casa do Código](http://www.casadocodigo.com.br) publicou o [livro de JSF e JPA](http://www.casadocodigo.com.br/products/livro-jsf-jpa "Livro de JSF e JPA"), onde diversos assuntos são discutidos, inclusive detalhes sobre o JSF e como trabalhar com o MVC Pull.

E você, tem alguma preferência?
