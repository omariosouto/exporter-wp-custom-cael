---
title: "WebServices sem servidor de aplicação no java 6"
date: "2007-07-11"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Criando [WebServices](http://pt.wikipedia.org/wiki/Web_service) num servidor de aplicação como [glassfish](https://glassfish.dev.java.net/) ou [jboss](http://labs.jboss.com/) ficou muito fácil. Basta escrever um Session Bean Stateless e usar as anotações do pacote **javax.xml.ws** que JavaSE 6 já fornece. Veja o exemplo:

\[java\] @Stateless @Remote(Oi.class) @WebService public class OiMundo { @WebMethod public String oi(String nome) { return "Hallo " + nome; } }

public interface Oi { String oi(String nome); } \[/java\]

Pronto, fazendo deploy dessa classe no servidor do aplicação cria automaticamente o WebService e disponibiliza o [WSDL](http://pt.wikipedia.org/wiki/Web_service#WSDL). Aqui tem uma discussão mais ampla sobre [webservices em um servidor de aplicação](https://blog.caelum.com.br/criando-um-webservice-com-a-jsr-181/).

#### Sem servidor de aplicação

Com javaSE 6 nas mãos você nem precisa de um servidor de aplicação, nem mesmo um web server! Basta usar as anotações **@WebService** e **@WebMethod** na sua classe e a ferramenta **apt** (annotation processing tool – não o apt do Debian!, encontra-se na pasta `bin` do `jdk`) para gerar um WebService.

A conhecida classe: \[java\] @WebService public class OiMundo { @WebMethod public String oi(String nome) { return "Hallo " + nome; } } \[/java\] O comando (imagina a classe OiMundo está no pacote **br.com.caelum.ws**):

`**$apt br/com/caelum/ws/OiMundo.java**`

gera um pacote **br.com.caelum.ws.javaws** com duas classes, uma classe _Oi.java_ para os parâmetros do WebService (no nosso caso String nome do método oi) e outra _OiResponse.java_ que representa o retorno. Falta publicar o serviço/endpoint e iniciá-lo: \[java\] public class PublicaService { public static void main(String\[\] args){ OiMundo service = new OiMundo(); Endpoint endpoint = Endpoint.publish("http://localhost:8080/oi", service); } } \[/java\] Pronto, o WebService está rodando sem servidor de aplicação nem web container. Tudo isso com javaSE 6 jdk. Para acessar a wsdl dele, usa-se o URL:

`**http://localhost:8080/oi?wsdl**`

Claro que isso não substitui o servidor por questões de performance, configuração e manutenção, mas é útil na hora de desenvolver e testar um serviço rapidamente.

#### Cliente

Para criar o cliente do serviço, javaSE 6 já vem com as [ferramentas](http://java.sun.com/webservices/docs/2.0/jaxws/jaxws-tools.html#mozTocId497292) necessárias. Precisamos usar o **wsimport** (na pasta `bin` do jdk) para gerar as classes do cliente. Com o serviço rodando, fazemos na linha de comando:

`**$wsimport -keep -p br.com.caelum.cliente http://localhost:8080/oi?wsdl**`

A opção _keep_ não apaga os arquivos fontes e _p_ gera as classes dentre do pacote especificado.

Para chamar o serviço pelas classes geradas, escreva a seguinte classe dentro do pacote br.com.caelum.cliente:

\[java\] public class TesteServico { public static void main(String\[\] args) { br.com.caelum.cliente.OiMundo port = new OiMundoService().getOiMundoPort(); System.out.println(port.oi("Johann")); } } \[/java\] Repare que a classe `OiMundo` foi gerada pelo _wsimport_ e é uma **interface**, não confundir com o serviço `OiMundo` que é uma implementação.

#### WebServices no WebContainer

Num WebContainer, usando Tomcat ou Jetty, é preciso usar um framework para criar um WebService. Já foi mencionado o [Xfire](http://xfire.codehaus.org/) nesse [mesmo blog](https://blog.caelum.com.br/webservices-com-o-xfire/) (que agora faz parte do projeto [CXF](http://incubator.apache.org/cxf/)), mas existem outros como o [AXIS](http://ws.apache.org/axis/) da Apache ou [Metro](http://metro.java.net/) do java.net. **Metro** é a implementação usada pelo Glassfish.

Vamos usar o [Metro](http://metro.java.net/discover/) para criar um WebService dentro numa aplicação web usando Tomcat.

[Faça o download do metro](http://java.net/projects/metro/downloads/directory/releases), instale e copie os jars da pasta `metro/lib` para a pasta `tomcat/shared/lib`, ou então para o lib exclusivo do seu projeto. São os seguintes:

- webservices-api.jar
- webservices-extra.jar
- webservices-tools.jar
- webservices-extra-api.jar
- webservices-rt.jar
- wstx-services.war

Crie um aplicação web comum e adicione a classe _TchauMundo_ no seu projeto com as anotações:

\[java\] @WebService public class TchauMundo {

@WebMethod public String tchau(String nome) { return "Tschüss " + nome; } } \[/java\]

Gere novamente o serviço e execute o seguinte comando na pasta _src_ do seu projeto:

`**$apt br/com/caelum/ws/TchauMundo.java**`

As classes geradas devem ficar na pastas _WEB-INF/classes_.

Agora é preciso registrar um servlet e listener que recebe as requisições do WebService. O web.xml:

\[xml\] <web-app version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee"> <listener> <listener-class>com.sun.xml.ws.transport.http.servlet.WSServletContextListener</listener-class> </listener> <servlet> <servlet-name>webServiceServlet</servlet-name> <servlet-class>com.sun.xml.ws.transport.http.servlet.WSServlet</servlet-class> <load-on-startup>1</load-on-startup> </servlet> <servlet-mapping> <servlet-name>webServiceServlet</servlet-name> <url-pattern>/tchau</url-pattern> </servlet-mapping> </web-app> \[/xml\]

Além disso, falta um outro xml (mais um!) com a definição do serviço/endpoint. O arquivo **sun-jaxws.xml** deve estar na pasta `WEB-INF`. Veja o exemplo:

\[java\] <endpoints xmlns='http://java.sun.com/xml/ns/jax-ws/ri/runtime' version='2.0'> <endpoint name='tchauMundo' implementation='br.com.caelum.ws.TchauMundo' url-pattern='/tchau'/> </endpoints> \[/java\]

Fazendo o deploy e acessando a seguinte url:

**http://localhost:8080/ws/tchau**

A entrada de uma API para produzir e consumir webservices no Java SE 6 gerou muita polêmica, por inflar o tamanho da Java RE e ser pouco necessário na maioria dos projetos. Apesar disso, ter uma API unificada, além de ferramentas facilitadoras, possibilita uma mais fácil adoção de webservices por parte dos desenvolvedores Java.
