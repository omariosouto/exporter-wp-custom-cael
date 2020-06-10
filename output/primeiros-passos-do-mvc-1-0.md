---
title: "Primeiros passos com a especificação do MVC 1.0"
date: "2015-04-07"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Temos participado da especificação do [MVC 1.0](https://java.net/projects/mvc-spec/pages/Home), uma das mais esperadas para o Java EE 8. Já há uma versão inicial para download. Você pode baixar sua especificação e API diretamente pela [página do JCP](http://download.oracle.com/otndocs/jcp/mvc-1-edr-spec/index.html), ou via maven, para o caso da API:

\[code language="xml"\] <dependency> <groupId>javax.mvc</groupId> <artifactId>javax.mvc-api</artifactId> <version>1.0-edr1</version> </dependency> \[/code\]

Vale ressaltar que a ideia dessa especificação não é substituir o JSF, que é um modelo de MVC baseado em componentes (`component-based`). [Diferente disso](https://blog.caelum.com.br/entenda-os-mvcs-e-os-frameworks-action-e-component-based/), o MVC 1.0 propoe suporte para um framework MVC baseado em ações (`action-based`), assim como o [VRaptor](http://www.vraptor.org/), Spring MVC e outros.

### Integração com outras specs

A API do MVC se integra com diversas especificações do Java EE, como [CDI](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/), Bean Validation, e JAX-RS (The Java API for RESTful Web Services), sendo a última utilizada como base dessa nova spec. Na prática, isso significa que todo controller do MVC é um resource do JAX-RS e, por esse motivo, curiosamente podemos definir a anotação `@Controller` no escopo do método:

\[code language="java"\] @Path("usuarios") public class UsuarioController {

@Controller @GET public String lista() { // ... } } \[/code\]

Interessante, não é? Com isso podemos ter classes hibridas, onde alguns métodos são controllers do MVC e outros serviços do JAX-RS. Mas claro, você também pode adicionar a anotação no escopo da classe, definindo assim que todos os seus métodos serão controllers.

\[code language="java"\] @Path("usuarios") @Controller public class UsuarioController { // ... } \[/code\]

### Ozark, a implementação de referência

A implementação de referência dessa nova especificação é o [Ozark](https://ozark.java.net/), que também já possui uma versão para download disponível via maven e pode ser testado com [um nightly build do Glassfish](http://dlc.sun.com.edgesuite.net/glassfish/4.1/nightly/glassfish-4.1-b13-03_16_2015.zip).

\[code language="xml"\] <dependency> <groupId>com.oracle.ozark</groupId> <artifactId>ozark</artifactId> <version>1.0.0-m01</version> <scope>compile</scope> </dependency> \[/code\]

Para facilitar esse setup inicial, também foi disponibilizada [uma imagem de Docker](https://ozark.java.net/download.html) que já inclui o Ozark, Glassfish e todos os seus exemplos.

### Conhecendo um pouco do código

É assim que se parece um primeiro exemplo do MVC com Ozark:

\[code language="java"\] @Controller @Path("olaMundoMVC") public class PrimeiroController {

@GET public String olaMundo() { return "olaMundo.jsp"; } } \[/code\]

Neste caso, o método olaMundo poderá ser acessado pela URL [http://localhost:8080/projeto/olaMundoMVC](#). Após executá-lo, o Ozark despachará a requisição para a jsp de seu retorno, `olaMundo.jsp`, que deve estar presente em `/WEB-INF/views/`.

Além do retorno do método, existem diversas variações para definição da view que deve ser renderizada. Retornar um `Viewable` ou anotar o método com `@View` são algumas delas.

\[code language="java"\] @GET public String olaMundo() { return "olaMundo.jsp"; }

@GET public Viewable olaMundo() { return new Viewable("olaMundo.jsp"); }

@GET @View("olaMundo.jsp") public void olaMundo() { } \[/code\]

Você encontra esse e diversos outros exemplos de uso do MVC em um mirror de seu repositório no Github, criado e atualizado para facilitar a navegação do código.

[https://github.com/spericas/ozark/tree/master/test](https://github.com/spericas/ozark/tree/master/test)

### Trabalhando com diferentes tipos de view

Além do JSP e Facelets, que são as definições de `view` do Java EE, o Ozark também oferece uma serie [extensões](https://github.com/spericas/ozark/tree/master/ext) que possibilitam integração com os mais diferentes tipos de mecanismos de template existentes no mercado. FreeMarker, Velocity, Mustache, Handlebars e Thymeleaf são algumas das possibilidades atuais.

Para utilizar o `Mustache`, por exemplo, você só precisa adicionar a dependência do `ozark-mustache` em seu classpath e pronto!

\[code language="xml"\] <dependency> <groupId>com.oracle.ozark.ext</groupId> <artifactId>ozark-mustache</artifactId> <version>1.0.0-m01</version> <scope>compile</scope> </dependency> \[/code\]

Conveniente, não é? Nenhuma configuração adicional será necessária. Com isso você já poderá retornar views com a extensão `.mustache` nos métodos de seu controller:

\[code language="java"\] @Path("mustache") @Controller public class MustacheController {

@GET public String hello() { return "hello.mustache"; } } \[/code\]

Claro, muito além das que já estão implementadas, você pode usar qualquer tecnologia de view desenvolvendo sua própria implementação de [ViewEngine](https://github.com/spericas/mvc-spec/blob/master/api/src/main/java/javax/mvc/engine/ViewEngine.java). Veja aqui como [a MustacheViewEngine foi desenvolvida.](https://github.com/spericas/ozark/blob/master/ext/mustache/src/main/java/com/oracle/ozark/ext/mustache/MustacheViewEngine.java)

### Como contribuir

É importante lembrar que esse é um trabalho em progresso e que está em estágio inicial, mas desde já você pode contribuir enviando ideias, sugestões, dúvidas e críticas. Todos serão importantíssimos para definirmos o futuro do MVC do Java EE 8. Seu feedback pode ser postado aqui, ou enviado diretamente na lista de usuários users@mvc-spec.java.net.
