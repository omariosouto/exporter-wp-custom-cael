---
title: "Spring sem XML. É possível?"
date: "2014-08-05"
author: "paulo.alves"
authorEmail: "paulo.alves@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Sempre que falamos em trabalhar com Spring e colocá-lo em nossos projetos, sabemos que precisaremos configurar um bocado de XML para que o projeto possa ser usado e funcione corretamente. Desde integrações com os principais frameworks à beans do sistema, que podem receber configurações específicas no application-context.xml. Mas o que poucos desenvolvedores sabem é que desde a versão 3.0 do Spring já é possível trabalhar sem XML.

Os mais antigos com Spring, vão lembrar que a configuração era (quase que) completamente via XML e todas as classes do seu sistema era um bean configurado no arquivo xml.

Hoje em dia, esse tipo de configuração foi ficando cada vez mais "detestada". A questão seria então, como usar Spring e não ter que manter "quilômetros" de XML em nossas aplicações?

Para começarmos nossa configuração, devemos criar uma classe que será a classe de configuração da nossa aplicação. Vamos criar a classe AppConfig, e para que o Spring saiba que essa será a classe que irá configurar nossa aplicação, vamos usar a annotation `@Configuration`.

\[code language="java"\] @Configuration public class AppConfig {

} \[/code\]

Mesmo com a classe anotada, ainda precisaremos informar ao Spring que essa é nossa classe de configuração. Como queremos ter apenas o web.xml de arquivo de configuração da aplicação, precisaremos informar ao Spring na inicialização do seu contexto, que nossa configuração será por annotation.

Para isso usaremos o parâmetro `contextClass` no web.xml. Conforme o código a seguir:

\[code language="xml"\] <init-param> <param-name>contextClass</param-name> <param-value> org.springframework.web.context.support.AnnotationConfigWebApplicationContext </param-value> </init-param> \[/code\]

Além de informar isso precisamos também dizer qual é a classe anotada com `@Configuration`. O parâmetro é o mesmo que já usávamos para configurar o xml `contextConfigLocation` porém o `value` é a classe que usaremos como configuração padrão. No caso, o nome completo: `br.com.caelum.mvc.AppConfig`.

\[code language="xml"\] <init-param> <param-name>contextConfigLocation</param-name> <param-value>br.com.caelum.mvc.AppConfig</param-value> </init-param> \[/code\]

A configuração completa ficaria assim:

\[code language="xml"\] <servlet> <servlet-name>springmvc</servlet-name> <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class> <init-param> <param-name>contextClass</param-name> <param-value> org.springframework.web.context.support.AnnotationConfigWebApplicationContext </param-value> </init-param> <init-param> <param-name>contextConfigLocation</param-name> <param-value>br.com.caelum.mvc.AppConfig</param-value> </init-param> </servlet> <servlet-mapping> <servlet-name>springmvc</servlet-name> <url-pattern>/</url-pattern> </servlet-mapping> \[/code\]

Agora que temos TODA a configuração XML que precisaremos, podemos começar a fazer as configurações específicas para o projeto.

Vamos informar ao Spring que estamos usando Spring MVC. No xml, faríamos `<mvc:annotation-driver />`. Essa mesma configuração deve ser feita em nossa classe `AppConfig` com a annotation `@EnableWebMvc`.

\[code language="java"\] @EnableWebMvc @Configuration public class AppConfig { // outros métodos aqui } \[/code\]

Assim como no XML existem configurações que são específicas para o SpringMVC. Essas configurações podem ser feitas em nossa classe quando estendemos a classe `WebMvcConfigurerAdapter`.

Essa classe nos possibilitará configurações comuns como `<mvc:default-servlet-handler/>`. Nossa classe ficaria assim:

\[code language="java"\] @EnableWebMvc @Configuration public class AppConfig extends WebMvcConfigurerAdapter { @Override public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) { configurer.enable(); } } \[/code\]

Com essas configurações, já conseguimos rodar a aplicação com SpringMVC. Porém para cada **_bean_** é necessário criar um método que retorna uma instância de nossos beans. Por exemplo, se queremos um bean chamado `UsuarioDAO`, teríamos que criar um método chamado `usuarioDAO()` anotado com `@Bean` como a seguir:

\[code language="java"\] @EnableWebMvc @Configuration public class AppConfig {

@Bean public UsuarioDAO usuarioDAO() { return new UsuarioDAO(); // instanciando o bean para o Spring injetar em algum lugar }

} \[/code\]

Da forma acima, passamos a ter muita configuração. Imagine quantos beans nossa aplicação terá e de quantos métodos apenas para que o Spring possa injetá-los precisaríamos?

Quando usamos XML, o Spring consegue injetar automaticamente sem termos que criar tags `<bean />`. Para isso, adicionamos a configuração . Com annotation também podemos fazer o mesmo, deixando para o Spring o trabalho de criar nossos beans. Para isso, colocaremos mais uma annotation na classe `AppConfig` chamada `@ComponentScan` e que possui uma propriedade `basePackages` onde você pode passar um pacote ou vários que serão escaneados. Nosso código ficará assim:

\[code language="java"\] @Configuration @EnableWebMvc @ComponentScan(basePackages={"br.com.caelum"}) public class AppConfig extends WebMvcConfigurerAdapter {

// Métodos de configuração, nenhum bean da nossa app é necessário ...

} \[/code\]

Nossa aplicação com SpringMVC está quase pronta para rodar. Ainda precisamos informar como o Spring encontrará nossas páginas. Essa configuração é feita com um bean `InternalResourceViewResolver` informando as propriedades de _prefix_ e _suffix_.

Vamos fazer essa mesma configuração. Quando configuramos no XML usamos um **bean**. Como já comentei, beans no XML viram métodos na configuração baseada em Java. Assim, devemos criar um método que retorne uma instância de `InternalResourceViewResolver`.

O que precisamos fazer na implementação é simples, instanciar essa classe e invocar os métodos `setPrefix` e `setSuffix`. Agora ficou fácil fazer o método completo.

\[code language="java"\] @Bean public InternalResourceViewResolver internalResourceViewResolver() { InternalResourceViewResolver resolver = new InternalResourceViewResolver(); resolver.setPrefix("/WEB-INF/views/"); resolver.setSuffix(".jsp");

return resolver; } \[/code\]

Com essa configuração, fechamos o mínimo necessário para rodar o SpringMVC completamente configurado via classe Java. Quando olhamos a classe final, vemos que o resultado é bem simples e justo.

O **Spring** configurado por _annotation_ nos dá algumas vantagens:

- Configuração compilável;
- Auto-complete das IDE's;
- Instânciar um _Bean_ de acordo com uma regra de usuário ou _environment_

 

E você, o que achou da configuração do Spring via Java? Quais outras vantagens você consegue ver usando configuração por annotation?
