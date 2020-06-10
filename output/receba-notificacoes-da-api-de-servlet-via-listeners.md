---
title: "Receba notificações da api de Servlet via Listeners"
date: "2012-12-18"
author: "dchohfi"
authorEmail: "dchohfi@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Em alguns sistemas web precisamos barrar que o usuário efetue o login em mais de um computador. Mas a pergunta é, onde colocaríamos essa regra?

Pode ser feito logo quando o usuário logar, para saber se ja há algum usuário com aquele login, com uma sessão aberta. Claro, você pode se organizar e ter apenas uma classe concentrando a responsabilidade por trabalhar com a sessão do usuário. Nem sempre temos sorte de mexer com um código mais organizado, ou você pode trabalhar com um framework que está escondendo bastante coisa por baixo dos panos. Além disso, você pode estar interessado em ver as modificações na sessão do usuário por diversos motivos, não apenas no caso de login.

A [especificação de Servlet](http://jcp.org/aboutJava/communityprocess/final/jsr315/index.html "especifiçacão") do Java nos provê uma funcionalidade interessante, os **listeners**, onde podemos cadastrar diversos **ouvintes** para determinados **eventos**. Sempre que esses eventos ocorrerem, em qualquer lugar do sistema, **seremos notificados**.

Para isso a api possuí diversas interfaces para cadastrar novos listeners e uma delas é a [HttpSessionAttributeListener](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionAttributeListener.html "HttpSessionAttributeListener"), onde podemos saber quando um atributo foi _adicionado, removido ou substituído_ da sessão do usuário.

Esses métodos recebem como paremetro um objeto do tipo [HttpSessionBindingEvent](http://docs.oracle.com/cd/E17802_01/webservices/webservices/docs/1.6/api/javax/servlet/http/HttpSessionBindingEvent.html "HttpSessionBindingEvent") e com ele podemos recuperar as informações do atributo da sessão, como o nome e o objeto passado no parametro ao [setAttribute](http://docs.oracle.com/cd/E17802_01/webservices/webservices/docs/1.6/api/javax/servlet/http/HttpSession.html#setAttribute(java.lang.String, java.lang.Object) "setAttribute") da já conhecida classe **HttpSession**. Eis o esqueleto da implementação:

\[sourcecode language="java"\] @WebListener public class UsuarioDuplicadoListener implements HttpSessionAttributeListener { public void attributeAdded( HttpSessionBindingEvent bind ) { }

public void attributeRemoved( HttpSessionBindingEvent bind ) { }

public void attributeReplaced( HttpSessionBindingEvent arg0 ) { } } \[/sourcecode\]

Com servlets 3.0 basta a anotação `@WebListener` para que seu listener seja carregado. Para as versões anteriores, há tags correspondentes para o `web.xml`.

Outros listeners também estão disponíveis para outros tipos de notificações, por exemplo, se fosse necessário descobrir quando o projeto subiu por completo, o servidor poderia nos notificar a partir da interface [ServletContextListener](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContextListener.html) e para registrá-la basta adicionar a mesma anotação que foi adicionada no listener anterior.

Mas, para implementar a funcionalidade que desejamos - barrar que o mesmo usuário se logue em mais de um lugar - precisamos saber se aquele objeto adicionado - para aquele atributo - não foi adicionado posteriormente por outra requisição. Para isso teríamos de acessar todas as sessões ativas e, por questões de segurança, a especificação não permite.

Uma alternativa é armazenar todos os usuários logados em uma lista e, quando outro usuário tentar efetuar o login novamente, validar se o mesmo objeto já não foi adicionado

Precisamos apenas tomar cuidado com o tipo de lista que iremos armazenar essas informações, ou melhor, como não queremos repetições podemos utilizar a interface _Set_, que não aceita itens repetidos. Outro cuidado que precisamos ter é que o ciclo de vida de um listener é igual de uma Servlet, ou seja, teremos apenas uma instância de `UsuarioDuplicadoListener` na memória, sendo compartilhada entre várias **threads**. Precisamos de uma implementação da interface _Set_ que seja **thread-safe** e a api do Java nos dispõe de algumas opções que já tomam cuidado com os possível problemas de concorrência, ou utilizar o `Collections.synchronizedSet`.

Vamos tomar por padrão que o nome do atributo que gerencia o usuário logado no sistema é _usuarioLogado_, basta então que a classe que represente essa informação implemente os métodos `equals` e `hashCode` para que a coleção trate a busca corretamente.

\[sourcecode language="java"\] @WebListener public class UsuarioDuplicadoListener implements HttpSessionAttributeListener { private static final String ATTRIBUTE\_NAME = "usuarioLogado"; private static final Set<Object> USUARIOS\_LOGADOS = Collections.synchronizedSet(new HashSet<Object>());

public void attributeAdded(HttpSessionBindingEvent bind) { String attributeName = bind.getName(); Object attributeValue = bind.getValue(); if(ATTRIBUTE\_NAME.equals(attributeName) ) { if(!USUARIOS\_LOGADOS.add(attributeValue)){ bind.getSession().removeAttribute(ATTRIBUTE\_NAME); } } }

public void attributeRemoved(HttpSessionBindingEvent bind) { String attributeName = bind.getName(); Object attributeValue = bind.getValue(); if(ATTRIBUTE\_NAME.equals(attributeName)) { USUARIOS\_LOGADOS.remove(attributeValue); } }

public void attributeReplaced(HttpSessionBindingEvent bind) { } } \[/sourcecode\]

Com essa abordagem temos um código centralizado, independente da implementação que representa o usuário logado no sistema e seria simples implementar alguma regra mais específica - como bloquear o usuário anterior ou até saber quanto usuários logados atualmente temos no sistema. Diversos outros problemas poderiam ser resolvidos com **listeners** e a api do Java provê outras seis interfaces para escutar diversos eventos que podem ocorrer em uma aplicação web.

[HttpSessionAttributeListener](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionAttributeListener.html) [HttpSessionListener](http://docs.oracle.com/javaee/6/api/javax/servlet/http/HttpSessionListener.html) [ServletRequestAttributeListener](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletRequestAttributeListener.html) [ServletRequestListener](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletRequestListener.html) [ServletContextAttributeListener](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContextAttributeListener.html)

Essa abordagem, de registrar um listener para validar se existe mais de um usuário logado no sistema é usado pelo **Spring Security** e aqui na Caelum temos um [curso que aborda este e diversos outros assuntos do framework Spring](http://www.caelum.com.br/curso/fj-27-spring-framework/ "FJ-27").
