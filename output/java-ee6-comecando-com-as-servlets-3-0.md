---
title: "Java EE6: Começando com as Servlets 3.0"
date: "2010-01-08"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Se você nunca escreveu uma servlet antes por causa do medo de tantas configurações, agora é o momento para iniciar nessa tecnologia. Repare, com esse artigo, como ficou mais simples desenvolver para a web com Java.

Grande parte das aplicações atualmente desenvolvidas em Java são para a Web e geralmente são desenvolvidas através de frameworks como [Struts](http://struts.apache.org/), [VRaptor](http://www.vraptor.com.br/) e [JSF](http://java.sun.com/javaee/javaserverfaces/). No entanto, é muito importante que antes de aprendermos alguma dessas ferramentas, entendamos os conceitos que elas nos abstraem.

**Servlets**

O principal pilar do desenvolvimento Web em Java é a API de Servlets. Com ela é possível executarmos código de uma determinada classe Java a partir de requisições HTTP para uma URL. Esse acesso é feito através de configurações não triviais de um servidor e arquivos específicos, de maneira diferente às antigas linguagens de script, como Perl, o que pode tornar o aprendizado de Servlets complicado.

Até a versão 2 da API de Servlets, precisamos fazer a declaração dessas classes através de XML (o `web.xml`). Portanto, além de nos preocuparmos em escrever o código Java com o processamento e lógica que desejamos fazer, ainda temos que nos preocupar com as configurações em XML, numa estrutura de diretórios especifica (o famoso diretório `WEB-INF/classes`), além de ter de manusear um _servlet container_.

**Pronto pra escrever sua primeira servlet? Use a versão 3.0!**

No mês passado foi aprovada oficialmente a versão nova do JavaEE 6, trazendo entre várias especificações novas, uma versão para Servlets, contendo diversas atualizações, a [JSR 315](http://jcp.org/en/jsr/detail?id=315), também conhecida como Servlet 3.0.

Nessa nova versão criamos as Servlets da mesma forma que fazíamos antes, ou seja, através de uma classe que estenda `HttpServlet`. Dessa forma, podemos criar uma Servlet que recebe um parâmetro pela requisição e imprime o mesmo no console da seguinte forma:

\[java\] public class OiMundo extends HttpServlet { protected void service(HttpServletRequest request, HttpServetResponse response) throws ServletException, IOException { String nome = request.getParameter("nome");

PrintWriter out = response.getWriter(); out.println("Ola: " + nome); } } \[/java\]

A primeira grande diferença que pode ser percebida na Servlet 3.0 é o uso de anotações para a configuração das Servlets em vez de grandes declarações em XML. Na versão 3, existe a anotação `@WebServlet`, que indica que aquela classe é uma Servlet. Essa anotação recebe no seu parâmetro `value` em qual URL a Servlet estará disponível.

\[java\] @WebServlet(value="/oiMundo") public class OiMundo extends HttpServlet { PrintWriter out = response.getWriter(); \[/java\]

Você já poderia acessar nossa Servlet através de uma URL semelhante a `http://localhost:8080/projeto/OiMundo?nome=Caelum`, porém é mais interessante termos um formulário HTML que enviará a requisição e o parâmetro para a Servlet. Podemos criar um `oiMundo.html` com o seguinte conteúdo:

\[xml\] <html> <body> <form action="oiMundo">Informe o nome: <input name="nome" type="text" /> <input type="submit" value="Enviar" /> </form> </body> </html> \[/xml\]

Desenvolvedores que já trabalharam com a versão 2 de Servlets podem perceber que não declaramos nenhum nome para nossa Servlet em sua configuração. Isso anteriormente era feito através da Tag `<servlet-name>` no XML. Porém, por padrão, na versão 3.0 é utilizado o nome completo da classe que está anotada. Podemos sobrescrever essa padrão através do parâmetro name da anotação `@WebServlet`:

\[java\] @WebServlet(value="/oiMundo", name="ServletOiMundo") \[/java\]

**Mais novidades da especificação**

Além de não precisarmos mais fazer a declaração das nossas Servlets em XML, agora também podemos declarar Filtros via anotações com o uso de `@WebFilter`.

Outro ponto que também acaba sendo muito comum quando utilizamos frameworks e bibliotecas de terceiros é que se exige uma configuração mínima no `web.xml` dos nossos projetos. Mas agora nem mesmo precisamos fazer essa configuração. As bibliotecas podem disponibilizar no diretório `META-INF` de seus jars um arquivo chamado `web-fragment.xml` contendo as configurações mínimas necessárias para o funcionamento da biblioteca, dessa forma, nós que utilizamos as bibliotecas não precisaremos nem fazer as configurações mínimas.

**O que preciso para usar a nova versão?**

Para que possamos utilizar Servlet 3.0 em nossas aplicações precisamos de um servidor que implemente essa nova versão. Até o momento, o único servidor que possui um release compatível é o [Glassfish v3](http://glassfish.java.net/). Outros servidores famosos como [Jetty](http://jetty.codehaus.org/jetty/) (disponível em versão experimental na versão 8 ) e [Tomcat](http://tomcat.apache.org/) (planejado para a versão 7) também suportarão.

Agora bastaria disponibilizar um projeto no servidor contendo no seu diretório `WEB-INF/classes` o `.class` da nossa nova Servlet.

**Servlet 3.0 e a Caelum**

Nosso conhecido [curso de java para web](http://www.caelum.com.br/curso/fj-21-java-web/), que vai desde servlets e JSP até Struts 2 e um pouco de Hibernate e VRaptor, já incorpora essas novidades do Java EE 6, e agora possui um capítulo totalmente dedicado à API Servlet 3.0.

Além disso, a versão 3.1 do [VRaptor](http://www.vraptor.com.br/) possui suporte também através do uso dos _web fragments_, dessa forma, você utiliza o framework sem escrever absolutamente nenhuma linha de XML, nem mesmo a declaração do filtro do framework.
