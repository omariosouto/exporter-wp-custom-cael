---
title: "Começando um projeto no Play! Framework usando Java"
date: "2014-05-23"
author: "fernando.stefanini"
authorEmail: "fernando.stefanini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Aqui na Caelum, sempre temos bastante liberdade para experimentar frameworks, bibliotecas e técnicas. Nos últimos meses trabalhamos em um projeto com o [Play! Framework](http://playframework.com/ "Play! Framework"), framework que tem ficado cada vez mais famoso devido à sua facilidade de uso, rapidez no desenvolvimento, otimizações de performance e várias outras vantagens.

Com apenas um comando já se tem um projeto pronto para rodar, sem precisar de nada mais que um terminal, um browser e o editor de texto ou IDE de sua preferência.

Antes de começar a trabalhar com o Play!, você vai precisar instalá-lo.

### Instalação

Para baixar o framework, escolha a _Classic Distribution_ mais recente [na página de download](http://playframework.com/download "download Play!"). Extraia os arquivos na pasta de sua preferência e configure seu `PATH` para poder executar `play` na linha de comando.

Que tal então se fizermos um projeto simples para ver como funciona?

### Criando um projeto

Começaremos criando um projeto simples para gerenciar códigos de desconto. Vamos chamá-lo de **descontos**.

A criação do projeto é feita com o comando `play new [nome-do-projeto]`, que gera uma estrutura básica pronta para uso.

\[code\] $ play new descontos \_ \_ \_\_ | | \_\_ \_ \_ \_ | '\_ \\| |/ \_' | || | | \_\_/|\_|\\\_\_\_\_|\\\_\_ / |\_| |\_\_/ The new application will be created in /diretorio/do/projeto/descontos \[/code\]

O assistente vai perguntar então o nome do seu projeto. Tecle `enter` para utilizar o nome padrão entre colchetes e responda à próxima pergunta com a opção **2** para que seu projeto seja criado em Java. Aperte `enter` novamente para realizar a escolha.

\[code\] What is the application name? \[descontos\] &gt;

Which template do you want to use for this new application?

1 - Create a simple Scala application 2 - Create a simple Java application

&gt; 2

OK, application descontos is created. Have fun! $ \[/code\]

Pronto! Seu projeto está criado. Entre na nova pasta e use o comando `play`, que abre um console interno, e nele o comando `run` para inicializar o servidor na porta padrão: **9000**.

\[code\] $ play \[info\] (...) \_ \_ \_\_ | | \_\_ \_ \_ \_ | '\_ \\| |/ \_' | || | | \_\_/|\_|\\\_\_\_\_|\\\_\_ / |\_| |\_\_/ &gt; Type "help play" or "license" for more information. &gt; Type "exit" or use Ctrl+D to leave this console. \[descontos\] $ run

\[info\] (...) --- (Running the application from SBT, auto-reloading is enabled) --- \[info\] play - Listening for HTTP on /0:0:0:0:0:0:0:0:9000 (Server started, use Ctrl+D to stop and go back to the console...) \[/code\]

Na primeira vez que o comando for executado o tempo de espera será um pouco maior, pois o Play! conferirá e baixará automaticamente as dependências e em seguida mostrará uma mensagem dizendo que o servidor está disponível para uso.

Acesse o endereço [localhost:9000](http://localhost:9000) no seu navegador para conferir! Para interromper a execução do servidor, aperte `ctrl+d` na linha de comando.

[![Your new application is ready.](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-04-welcome-300x165.png "Your new application is ready.")](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-04-welcome.png)

Agora que nosso projeto está "funcionando", precisamos modificá-lo, mas como utilizaremos o Eclipse para isso, temos que fazê-lo entender o projeto Play!

### Configurando o ambiente

Nativamente, o framework traz os comandos `eclipse` e `idea`, que configuram automaticamente o ambiente para as duas IDEs. Basta executar o comando adequado para suas necessidades.

\[code\] \[descontos\] $ eclipse \[info\] About to create Eclipse project files for your project(s). \[info\] Compiling (...) \[info\] Successfully created Eclipse project files for project(s): \[info\] descontos \[descontos\] $ \[/code\]

Depois de configurado, basta importar o projeto no Eclipse e partir para o trabalho, mas para isso é bom entender um pouco sobre os diretórios do projeto.

### A estrutura do projeto

Os diretórios mais importantes de um projeto Play! são os seguintes:

- `app/`, onde ficam os arquivos de código, como views (páginas html), modelos e controllers (ou quaisquer outras classes Java)
- `conf/`, onde ficam as configurações de projeto, incluindo rotas, banco de dados e afins
- `public/`, onde se armazenam os recursos como imagens e arquivos de estilização

Como vimos, a pasta `app` é onde boa parte do trabalho será feito. É lá que vamos escrever nosso primeiro código com o Play!

### Começando a aplicação

Como primeiro passo, definiremos uma classe Java que será nosso **modelo** para o cupom de desconto. Vamos, então, criar a classe `CupomDesconto` mas, para não misturar os nossos modelos com os controllers, vamos colocá-la no pacote `models`, ou seja, teremos a classe `app.models.CupomDesconto`. Vamos definir também alguns atributos importantes para o nosso cupom.

\[code language="java" gutter="true"\] package models;

public class CupomDesconto { private Long id; private String nomeDoCliente; private Double valor;

// getters e setters } \[/code\]

Um passo importante é não se esquecer de criar **getters** e **setters** para os seus atributos. A maior parte das funcionalidades do Play! que lidam com os seus modelos presumirão que você criou estes métodos.

Agora precisamos permitir que as instâncias desse modelo sejam persistidas no banco de dados.

### Configurando um banco de dados

Já temos um modelo, então está na hora de habilitar o banco no seu projeto. As principais configurações do Play! ficam no arquivo `conf/application.conf`. O framework te permite nativamente utilizar um banco de dados **in-memory** para que você possa desenvolver sem ter que se preocupar com criar um banco local imediatamente. Para ativá-lo, basta descomentar as seguintes linhas:

\[code title="-- language=conf/application.conf"\] db.default.driver=org.h2.Driver db.default.url="jdbc:h2:mem:play" db.default.user=sa db.default.password=""

ebean.default="models.\*" \[/code\]

Isso indica ao Play! qual o banco que ele deve usar e também que ele pode criar automaticamente os comandos de banco de dados que geram as tabelas do modelo. Configuramos também o Ebean, implementação ORM que vem por padrão com o Play!, para procurar os modelos no pacote `models`.

Agora, para dizer ao Play! que seu modelo pode ser persistido, precisamos adicionar a anotação `javax.persistence.Entity`, da JPA, à sua classe. Por fim, vamos utilizar as anotações `@Id` e `@GeneretadValue` para definir o `id` da tabela.

\[code language="java" highlight="3,4,6,7,8" gutter="true"\] package models;

import javax.persistence.\*; import play.db.ebean.Model;

@Entity public class CupomDesconto extends Model { @Id @GeneratedValue private Long id; //... } \[/code\]

Para facilitar ainda mais, fizemos a classe CupomDesconto herdar da classe Model. Dessa forma os objetos criados a partir dessa classe já vão possuir métodos básicos para acesso ao banco. Precisamos reiniciar o servidor para vermos as mudanças. Mas como não queremos ficar repetindo esse processo a cada modificação que fizermos, vamos usar um comando que automatiza isso:

\[descontos\] $ ~run

O `~run` vai recompilar  novamente seu projeto e  deixá-lo pronto para uso após cada modificação que você fizer. Agora acesse seu projeto no navegador e veja o que acontece! O sistema retorna uma página de alerta indicando que é necessário fazer uma modificação em seu banco.

Clicar no botão **Apply this script now!** modifica seu banco de dados para que reflita as mudanças de seus modelos! Isso ocorrerá toda vez que houver alguma mudança nos modelos que precise ser refletida nas tabelas.

[![Database needs evolution!](https://blog.caelum.com.br/wp-content/uploads/2014/05/evolution-300x136.png "Database needs evolution!")](https://blog.caelum.com.br/wp-content/uploads/2014/05/evolution.png)

Ao mesmo tempo em que aparece a mensagem no navegador, um erro é mostrado na linha de comando. Clique no botão para efetivar as mudanças.

Podemos agora criar as telas que utilizaremos para cadastrar e listar nossos cupoms.

### Views e Controllers — Cadastrando e listando modelos

No Play!, as `views` são escritas em Scala. Assim, podemos incluir código no HTML para gerar conteúdo dinamicamente. Se abrirmos o arquivo `index.scala.html` veremos o seguinte código:

\[code language="html"\] @(message: String)

@main("Welcome to Play") { @play20.welcome(message, style = "Java") } \[/code\]

Quando utilizar código `Scala` nas views, preceda com `@` os métodos ou variáveis.

A primeira linha indica os argumentos que passaremos à view antes de renderizá-la, enquanto na terceira é feita uma chamada ao template `main` (outro arquivo .scala.html no nosso projeto que contém o esqueleto HTML de todas as páginas do projeto), e passando para ele o parâmetro "Welcome do Play". Além disso, entre as chaves há o conteúdo que será renderizado no `<body>`.

Repare que se abrirmos o arquivo `main.scala.html` veremos que a primeira linha dele contém dois parâmetros: um título e o conteúdo HTML, como vimos na chamada acima.

\[code highlight="1,7,13" language="html" gutter="true"\] @(title: String)(content: Html)

<!DOCTYPE html>

<html> <head> <title>@title</title> <link rel="stylesheet" ...> <link rel="shortcut icon" ...> <script src="@routes....></script> </head> <body> @content </body> </html> \[/code\]

Os arquivos .scala.html também podem ser utilizados para criar **partials** ou **tags**.

Agora, vamos fazer o formulário para criar novos cupoms de desconto. Crie o arquivo `formularioNovoCupom.scala.html` no pacote `views`, receba nele o formulário de CupomDesconto `formCupom` (do tipo `Form[CupomDesconto]`), insira o template `main`, passando como parâmetro o título da página e crie o esqueleto do formulário HTML em seu corpo.

\[code language="html" gutter="true"\] @(formCupom: Form\[CupomDesconto\])

@main("Cadastrar Cupom") { <form action="/cupom/novo" method="POST"> <input type="submit" value="Criar"> </form> } \[/code\]

Vamos agora usar os `helpers` do Play! para criarmos os campos do formulário. Importe todos e faça uma chamada ao `inputText()`, que cria a estrutura HTML de um campo para entrada de texto. Este método recebe como parâmetro um `Field` do formulário, que acessamos com `formCupom("nome-do-campo")`, e pode receber o texto para uma label utilizando a sintaxe `'_label -> "texto-da-etiqueta"`.

\[code language="html" gutter="true" highlight="2,6,7"\] @(formCupom: Form\[CupomDesconto\]) @import helper.\_

@main("Cadastrar Cupom") { <form action="/cupom/novo" method="POST"> @inputText(formCupom("nomeDoCliente"), '\_label -> "Nome do cliente") @inputText(formCupom("valor"), '\_label -> "Valor") <input type="submit" value="Criar"> </form> } \[/code\]

As views em Scala são compiladas em um pacote Java para que você possa passar argumentos e identificar erros em **tempo de compilação** no seu código HTML, mostrados na linha de comando.

Pronto! Para ver seu formulário, precisamos utilizar um controller para renderizá-lo. Por hora, podemos fazê-lo no lugar da página principal. Na classe `app.controllers.Application`, utilize a classe `play.data.Form` para gerar um formulário de `CupomDesconto` e utilize-o para renderizar a página que acabou de criar.

A instrução `Form.form(CupomDesconto.class)` é um método de ajuda do Play! que cria uma estrutura de formulário pronta para ser utilizada nas páginas HTML.

\[code language="java" gutter="true" highlight="3,5,12,13"\] package controllers;

import models.CupomDesconto; import play.\*; import play.data.Form; import play.mvc.\*; import views.html.\*;

public class Application extends Controller {

public static Result index() { Form<CupomDesconto> form = Form.form(CupomDesconto.class); return ok(formularioNovoCupom.render(form)); }

} \[/code\]

Ao abrir a página no navegador, você já pode ver o formulário pronto

[![Formulário de cadastro](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-05-formulario-300x235.png "Formulário de cadastro")](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-05-formulario.png) mas ao clicar o botão de envio, recebemos uma tela de alerta, dizendo que a URL de envio ainda não existe e listando todas as URLs existentes.

[![Formulário de cadastro](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-06-actionnotfound-300x106.png "Action not found")](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-06-actionnotfound.png)

Repare que a `view` criada será usada para a criação de novos descontos, porém nós a chamamos no método `index()`. Vamos refazer essa parte do projeto para que fique melhor estruturado começando pelo controller: vamos mover o conteúdo do método `index()` para `formularioNovoCupom()` e criar um novo método que vai receber os dados enviados, `novoCupom()`. A variável `TODO` é uma página padrão amigável do Play! para te lembrar de terminar sua implementação.

\[code language="java" gutter="true" highlight="4,7,12,13,14"\] public class Application extends Controller {

public static Result index() { return TODO; }

public static Result formularioNovoCupom() { Form<CupomDesconto> form = Form.form(CupomDesconto.class); return ok(formularioNovoCupom.render(form)); }

public static Result novoCupom() { return TODO; } } \[/code\]

Agora falta criar as URLs. Isto é feito no arquivo `conf/routes`, dizendo, em ordem, o tipo de requisição (GET, POST, DELETE, etc), sua URL e o método executado na chamada. A rota que chama o método `app.controllers.Application.index()` abaixo é nossa página inicial. Criamos além dela URLs `/cupom/novo`, com tipos GET e POST, cada uma chamando um dos novos métodos do controller.

\[code highlight="3,4"\] # Home page GET / controllers.Application.index() GET /cupom/novo controllers.Application.formularioNovoCupom() POST /cupom/novo controllers.Application.novoCupom() ... \[/code\]

Agora podemos acessar tanto a página inicial quanto o formulário, e ao tentar criar um novo cupom de desconto não vemos mais o alerta e sim a mesma tela de nosso index:

[![TODO](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-07-todo-300x215.png "TODO")](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-07-todo.png)

Agora que temos rotas configuradas, podemos utilizar o `helper.form`, que recebe uma rota como argumento, para ajudar a gerar o formulário.

\[code language="html" gutter="true" highlight="2,6"\] @main("Cadastrar Cupom") { @helper.form(routes.Application.novoCupom) { @inputText(formCupom("nomeDoCliente"), '\_label -> "Nome do cliente") @inputText(formCupom("valor"), '\_label -> "Valor") <input type="submit" value="Criar"> } } \[/code\]

Vamos também modificar todo o nosso `index` para mostrar todos os cupons que tivermos cadastrado, recebendo do controller a lista completa. Ao final da lista, criamos também um link para criar novos cupons!

\[code language="html" gutter="true"\] @(cupons: List\[CupomDesconto\])

@main("Lista de Cupons"){ @if(cupons.isEmpty){ <h3>Não há cupons cadastrados</h3> } else { <h3>Cupons Cadastrados:</h3> <table> <tr> <th>id</th> <th>cliente</th> <th>valor</th> </tr> @for(cupom <- cupons){ <tr> <td>@cupom.getId</td> <td>@cupom.getNomeDoCliente</td> <td>R$ @cupom.getValor</td> </tr> } </table> } <hr> <a href="@routes.Application.formularioNovoCupom">Novo cupom</a> } \[/code\]

Note que agora a `index` recebe um parâmetro do tipo `List` para mostrar os cupons já cadastrados. Por enquanto, no controller, vamos adicionar uma lista vazia para não termos problemas de compilação, e nos preocuparemos em receber os cupons reais mais tarde.

\[code language="java" gutter="true" highlight="2"\] public static Result index() { return ok(index.render(new ArrayList())); } \[/code\]

Abrindo o navegador em [localhost:9000](http://localhost:9000) é possível ver a página agora renderizada corretamente.

[![Lista de cupons](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-08-listavazia-300x208.png "Lista de cupons")](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-08-listavazia.png)

Agora estamos prontos pra efetivamente cadastrar e listar os cupons de desconto.

### Lidando com formulários e cadastrando cupons

Repare que, até agora, o nosso formulário de cadastros não faz nada além de retornar uma página em construção. Vamos receber os dados enviados através do formulário. Para lidar com formulários enviados por requisição, o Play! nos oferece várias ferramentas. Vamos utilizar o método `bindFromRequest()` para receber o formulário enviado pela view, `hasErrors()` para rodar as validações automáticas no formulário, `get()` para receber o objeto `CupomDesconto` montado a partir do formulário e o método `save()` do nosso `CupomDesconto`, herdado de `Models` e responsável por salvar o objeto em banco. Após salvar o cupom no banco, o usuário é redirecionado direto pra lista de cupons já criados.

\[code language="java" gutter="true"\] public static Result novoCupom() { Form<CupomDesconto> form = Form.form(CupomDesconto.class).bindFromRequest(); if (form.hasErrors()) { return badRequest(formularioNovoCupom.render(form)); } CupomDesconto cupomDesconto = form.get(); cupomDesconto.save(); return redirect(routes.Application.index()); } \[/code\]

Neste caso, se as validações automáticas falharem, repare que renderizamos a mesma view passando para ela o formulário já com os dados preenchidos, para que o usuário re-entre os dados sem que perca tudo que digitou.

Só falta listar os cupons criados! Vamos para o último passo.

### Recuperando dados do banco

Agora que fomos capazes de salvar dados, queremos que o nosso index mostre os dados salvos. Para isso, vamos precisar passar para a view do index uma lista real de cupons toda vez que ela for renderizada (por enquanto estamos passando uma lista vazia). Para recuperar os dados utilizaremos a classe `com.avaje.ebean.Ebean`, configurado lá em cima, criando uma query de um modelo especifico e pedindo a lista de todas as entradas da tabela com `findList()`.

\[code language="java" gutter="true" highlight="2,3"\] public static Result index() { List<CupomDesconto> cupons = Ebean.createQuery(CupomDesconto.class).findList(); return ok(index.render(cupons)); } \[/code\]

[![Lista de cupons](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-09-lista-300x255.png "Lista de cupons")](https://blog.caelum.com.br/wp-content/uploads/2014/05/play-09-lista.png)

Obs: existe um padrão de projetos que estabelece que métodos que acessam o banco de dados devem ficar concentrados em uma classe separada (DAOs, ou Data Access Objects), dentro de um pacote com classes específicas para essa finalidade, para tornar o código mais legível. Neste exemplo fizemos a chamada no próprio `controller` para manter a simplicidade, mas vale lembrar que não há um certo ou errado. Se quiser saber mais sobre organização de pacotes e classes, leia esse post sobre [como organizar os pacotes da sua aplicação](https://blog.caelum.com.br/como-organizar-os-pacotes-da-sua-aplicacao/).

### Considerações Finais

O que vimos até agora foram as características básica de um projeto em Play!, parte da estrutura do projeto e algumas das operações de CRUD. Há ainda um grande número de funcionalidades a serem exploradas neste framework e que podem ser vistas no [site do Play!](http://www.playframework.com), além do [Workshop](https://www.caelum.com.br/workshop-play-na-pratica/ "Workshop Play! Framework") que será ministrado pelo Alberto Souza.
