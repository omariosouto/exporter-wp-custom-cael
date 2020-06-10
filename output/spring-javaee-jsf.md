---
title: "Trabalhe com Java EE e Spring juntos"
date: "2012-05-31"
author: "mario.amaral"
authorEmail: "mario.amaral@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Existem várias [discussões recentes sobre se devemos utilizar o Spring ou o JavaEE em nossos novos projetos](https://blog.caelum.com.br/java-ee-versus-spring-retomando-a-discussao/ "Java EE versus Spring: retomando a discussão"), já que muitas das facilidades fornecidas pelo Spring estão agora disponíveis por padrão a partir do JavaEE 6, talvez indo além. A maioria desses posts realiza comparacões entre o Spring e o JavaEE, mostrando pontos fracos e fortes de cada plataforma e determinando um vencedor na disputa. O que esses autores esquecem é que não é necessário escolher entre um ou outro, sendo plenamente possível utilizar tanto bibliotecas do JavaEE e do Spring em conjunto, conseguindo o melhor dos dois mundos, se houver a necessidade.

[![Logo Spring](https://blog.caelum.com.br/wp-content/uploads/2012/04/Logo_Spring_258x151.png "Spring")](https://blog.caelum.com.br/wp-content/uploads/2012/04/Logo_Spring_258x151.png)

Neste post usaremos como exemplo uma aplicação JSF 2.0 rodando em um servlet container simples (como o Tomcat ou o Jetty, por exemplo), usando o Spring para fornecer injeção de dependências e controlar o ciclo de vida dos ManagedBeans do JSF.

O primeiro passo para realizar a integração é registrar o _listener_ do Spring e informar o local do arquivo de configurações no `web.xml`. Com isso, todos os serviços do Spring já estarão disponíveis no _startup_ da aplicação.

\[xml\] <!-- Listener do Spring --> <listener> <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class> </listener> <!-- Local do arquivo de configuração --> <context-param> <param-name>contextConfigLocation</param-name> <param-value>WEB-INF/applicationContext.xml</param-value> </context-param> \[/xml\]

Como iremos delegar para o Spring o controle sobre o ciclo de vida de nossos ManagedBeans, também temos que registrar o listener que nos permitirá utilizar os escopos padrões de uma aplicação web, como request ou session:

\[xml\] <listener> <listener-class>org.springframework.web.context.request.RequestContextListener</listener-class> </listener> \[/xml\]

Agora, é só indicar ao JSF para delegar a criação dos ManagedBeans para o Spring, registrando um `el-resolver` no `faces-config.xml`:

\[xml\] <faces-config> <application> <el-resolver>org.springframework.web.jsf.el.SpringBeanFacesELResolver</el-resolver> <application> ... </faces-config> \[/xml\]

A partir desse ponto, podemos usar as anotações do Spring para criar os ManagedBeans. Um bean simples com JSF seria escrito normalmente da seguinte maneira:

\[java\] @ManagedBean @RequestScoped public class ProdutoBean{ private List produtos; public List getProdutos(){ if(produtos == null){ ProdutoDAO dao = new ProdutoDAO(); produtos = dao.listaTodos(); } return produtos; } } \[/java\]

Escrever esse mesmo exemplo usando o spring é muito simples, basta trocar as anotações do JSF pelas (quase) equivalentes do Spring:

\[java\] @Controller // Equivalente à @ManagedBean @Scope("request") // Equivalente à @RequestScoped public class ProdutoBean{

private List produtos;

public List getProdutos(){ if(produtos == null){ ProdutoDAO dao = new ProdutoDAO(); produtos = dao.listaTodos(); } return produtos; } } \[/java\]

Para acessar a lista de produtos de uma página, é só referenciar o bean usado a Expression Language:

\[xml\] <h:dataTable value="#{produtoBean.produtos}" var="produto"> \[/xml\]

A grande vantagem é que agora podemos receber injeções de dependências diretamente em nossos beans. Vamos injetar o `ProdutoDAO`. Precisamos indicar para o Spring que o `ProdutoDAO` é um objeto injetável, faremos isso com a anotação `@Repository`:

\[java\] @Repository public class ProdutoDAO{ ... } \[/java\]

E agora, é só fazer a injeção da dependência no ProdutoBean:

\[java\] @Controller @Scope("request") public class ProdutoBean{ @Autowired // Anotação do Spring para injeção private ProdutoDAO dao;

private List produtos;

public List getProdutos(){ if(produtos == null){ produtos = dao.listaTodos(); } return produtos; } } \[/java\]

O Spring não é a única solucão para injeção de dependências fora de um servidor de aplicação. Poderiamos utilizar o [Weld](http://seamframework.org/Weld "Weld") para nos fornecer injeção padrão [JavaEE por meio do CDI](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/), [permitindo inclusive customizar a criação dos objetos](https://blog.caelum.com.br/customizando-a-producao-de-dependencias-no-cdi/ "Customizando a produção de dependências no CDI"). O uso do Spring, entretanto, torna mais fácil integrar nosso projeto com outras funcionalidades do framework, como Security, AOP, Transaction, etc., pois o ecossistemas Spring é enorme, bastando apenas configurar e habilitar os serviços no `applicationContext.xml`.

Sempre podemos considerar a integração com o Spring em nossos sistemas, seja para utilizar algum dos seus serviços que não tem especificação equivalente no JavaEE, seja para substituir alguma especificação cuja implementação do Spring forneça mais funcionalidades, ou simplesmente porque a implementação do Spring é a que a equipe mais domina.

E você, já integrou o Spring e o JavaEE em algum projeto?
