---
title: "Entenda a injeção de dependência nos frameworks MVC"
date: "2015-02-19"
author: "alessandro.palmeira"
authorEmail: "alessandro.palmeira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Todo mundo que utiliza algum [framework MVC](http://pt.wikipedia.org/wiki/MVC) moderno já se deparou em algum momento com o conceito de [Inversão de Controle](http://pt.wikipedia.org/wiki/Invers%C3%A3o_de_controle) (IoC). Com esse conceito, a classe não mais se preocupa em como conseguir suas dependências, mas sim em apenas trabalhar com elas. O principal jeito de conseguir isso é através da [Injeção de Dependências](http://pt.wikipedia.org/wiki/Inje%C3%A7%C3%A3o_de_depend%C3%AAncia) (DI). Essa técnica, presente em diversos frameworks MVC, ajuda muito no desacoplamento e nos [testes do nosso sistema](https://blog.caelum.com.br/facilitando-a-manutencao-dos-testes-ao-diminuir-o-acoplamento-com-o-codigo/).

Dois dos frameworks onde encontramos isso são o [Spring MVC](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html) e o [VRaptor](http://www.vraptor.org/). No exemplo abaixo, temos um `Controller` em cada um desses Frameworks. Esse `Controller` depende de um `DAO`, que por sua vez tem como dependência uma `Connection`.

**Usando o Spring:**

Controlador:

\[code language="java"\] @Controller public class TarefasController { private final JdbcTarefaDao dao;

@Autowired public TarefasController(JdbcTarefaDao dao) { this.dao = dao; }

@RequestMapping(“adicionaTarefa”) public void adiciona(Tarefa tarefa) { dao.adiciona(tarefa); // ... } } \[/code\]

DAO:

\[code language="java"\] @Repository public class JdbcTarefaDao { private final Connection connection;

@Autowired public JdbcTarefaDao(DataSource dataSource) throws SQLException { this.connection = dataSource.getConnection(); } // Resto do DAO, usando a connection sem instanciá-la em cada método } \[/code\]

**E usando o VRaptor:**

Controlador:

\[code language="java"\] @Controller public class TarefasController {

@Inject private final JdbcTarefaDao dao;

@Post("/adicionaTarefa") public void adiciona(Tarefa tarefa) { dao.adiciona(tarefa); // ... } } \[/code\]

DAO:

\[code language="java"\] @RequestScoped public class JdbcTarefaDao {

@Inject private final Connection connection;

// Resto do DAO, usando a connection sem instanciá-la em cada método } \[/code\]

Repare que, quando a URL mapeada é chamada, o metódo `adiciona` é invocado pelo framework e, dentro dele, o `DAO` será usado. Mas onde ele está sendo instanciado, já que em nenhum momento nesses frameworks nós damos um `new` nessas dependências?

Para alguns desenvolvedores que olham esse código e não têm muito contato com essas técnicas, o processo de apenas receber as instâncias já criadas por alguém parece algo mágico e obscuro. A tendência é que eles acabem usando sem saber de onde vêm todas aquelas instâncias. Só que nada na computação é magia, tudo acontece por causa de um comando. Tudo é código. A ideia de IoC é justamente fazer com que não nos preocupemos com a criação das instâncias, mas é sempre bom saber o que está acontecendo por trás dos panos.

Quando estamos iniciando no mundo do Java, sentimos a necessidade de entender como as coisas acontecem e podemos aproveitar um framework MVC caseiro para isso. No [curso de Desenvolvimento Java para Web](https://www.caelum.com.br/curso-java-web/), desenvolvemos esse framework simples, que funciona da seguinte maneira: temos um servlet que recebe todas as requisições e, baseado em um parâmetro recebido, instancia uma lógica, que é definida por nós em uma classe que implementa a interface `Logica`. No caso, a interface contém apenas o método `executa`, que recebe um `request` e um `response` como parâmetros. O código desse framework é o seguinte:

\[code language="java"\] @WebServlet("/mvc") public class ControllerServlet extends HttpServlet { protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

String parametro = request.getParameter("logica"); String nomeDaClasse = "br.com.caelum.mvc.logica." + parametro;

Class classe = Class.forName(nomeDaClasse); Logica logica = (Logica) classe.newInstance(); String pagina = logica.executa(request, response);

request.getRequestDispatcher(pagina).forward(request, response); } } \[/code\]

E a lógica para fazer a remoção de um contato:

\[code language="java"\] public class RemoveContatoLogic implements Logica {

public String executa(HttpServletRequest req, HttpServletResponse res) throws Exception {

long id = Long.parseLong(req.getParameter("id"));

ContatoDao dao = new ContatoDao(); // O DAO é instanciado aqui. dao.exclui(id);

return "lista-contatos.jsp"; } } \[/code\]

Note que, dentro do método `executa`, fazemos a instanciação do `ContatoDAO` para invocar o método `exclui`. Como vimos, isso não é uma boa prática, pois deixa as classes mais acopladas e dificulta na hora de realizar os testes de unidade.

A solução, como já indicamos, é apenas receber os objetos já instanciados. Queremos ter a seguinte lógica:

\[code language="java"\] public class RemoveContatoLogic implements Logica {

private ContatoDAO dao;

public RemoveContatoLogic(ContatoDAO dao) { this.dao = dao; }

public String executa(HttpServletRequest req, HttpServletResponse res) throws Exception {

long id = Long.parseLong(req.getParameter("id"));

dao.exclui(id); // O DAO não é instanciado no método executa

return "lista-contatos.jsp"; } } \[/code\]

Para conseguir essas instâncias das dependências das lógicas, os frameworks MVC geralmente utilizam alguma biblioteca. Essas bibliotecas fazem a inversão do controle, através da Injeção de Dependências. Existem [vários frameworks](https://keyholesoftware.com/2014/02/17/dependency-injection-options-for-java/) que fazem isso. Vamos modificar o nosso framework MVC simples para utilizar o [Guice](https://github.com/google/guice) (pronuncia-se Juice).

Para tal, basta trocarmos o código da instanciação da lógica para parar de chamar o método `newInstance()` e passar a usar o Guice:

\[code language="java"\] @WebServlet("/mvc") public class ControllerServlet extends HttpServlet { protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

String parametro = request.getParameter("logica"); String nomeDaClasse = "br.com.caelum.mvc.logica." + parametro;

// Usando o Guice para instanciar a lógica Injector injector = Guice.createInjector(new GuiceModule()); Class classe = Class.forName(nomeDaClasse); Object logica = injector.getInstance(classe);

String pagina = logica.executa(request, response);

request.getRequestDispatcher(pagina).forward(request, response); } } \[/code\]

A instância da classe `GuiceModule` é necessária para configurar a criação de uma `Connection`. Nela, herdamos a classe `AbstractModule` do Guice e configuramos o nosso injetor com o seguinte código:

\[code language="java"\] public class GuiceModule extends AbstractModule { @Override protected void configure() { this.bind(Connection.class).to(ConnectionProvider.class); } } \[/code\]

Por fim, temos que indicar ao Guice qual é o construtor da nossa lógica que ele deverá usar para instanciá-la e injetar as dependências. Fazemos isso anotando o construtor com `@Inject`:

\[code language="java"\] public class RemoveContatoLogic implements Logica {

private ContatoDAO dao;

@Inject public RemoveContatoLogic(ContatoDAO dao) { this.dao = dao; } // metodo executa } \[/code\]

Agora, quando a nossa lógica é invocada, o Guice se encarrega de criar as instâncias necessárias para que ela funcione. Nesse caso, isso significa conseguir uma instância de `ContatoDAO` antes mesmo de chamar o construtor da lógica. Ou seja, com apenas uma chamada ao Guice, conseguimos criar a lógica e todas as suas dependências.

Vale lembrar que os frameworks MVC vão mais além do que fazer a instanciação das dependências do construtor. Eles também recebem parâmetros direto no método da lógica, fazem conversão de tipos e trazem diversas outras facilidades para o dia a dia. Cabe ao desenvolvedor decidir qual framework mais o agrada, de acordo com os diferentes recursos de cada um. E você, já usa injeção de dependências no seu projeto?
