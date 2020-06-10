---
title: "Conheça o Tyrus - sua implementação para WebSocket com backend em Node e Java"
date: "2014-02-10"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Aqui na Caelum, adoramos destrinchar as [tecnologias novas](https://blog.caelum.com.br/java-ee6-comecando-com-as-servlets-3-0/) que entram na especificação da plataforma Java. No JavaEE 6, lançado em 10/12/2009, [falamos muito](https://blog.caelum.com.br/4-recursos-novos-do-cdi-1-1/) sobre [CDI](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/), e talvez tenha sido a [especificação](https://blog.caelum.com.br/diminua-suas-dependencias-com-os-eventos-do-cdi/) mais proeminente da tecnologia. Ano passado (12/06/2013), foi lançado o [JavaEE 7](https://blog.caelum.com.br/novidades-javaee7-2/), e já começamos a discorrer sobre algumas melhorias que entraram na especificação, como o [JMS 2.0](https://blog.caelum.com.br/a-nova-api-do-jms-2-0-no-java-ee-7/) por exemplo.

O objetivo deste artigo é estudar um pouco mais [a API de WebSocket](https://developer.mozilla.org/en-US/docs/WebSockets/Writing_WebSocket_client_applications). Já discutimos a [especificação anteriormente](https://blog.caelum.com.br/websockets-html5-em-java-com-jetty-web-em-tempo-real/), então a ideia aqui é tentar aprofundar um pouco mais. Talvez essa seja uma API tão discutida quanto foi o CDI,e ela nos faz rever alguns [conceitos sobre REST](http://www.infoq.com/news/2012/02/websockets-rest) também. Além disso, vários [frameworks](http://www.ignite.ee/websockets-in-spring-4-on-tomcat-8/) já estão suportando e [sugerindo melhorias](http://blog.gopivotal.com/products/websocket-architecture-in-spring-4-0) à especificação.

Para implementar o servidor vamos usar duas tecnologias: NodeJS e JavaEE 7 (GlassFish 4.0).

**_Server side - NODE_**

Node é uma implementação que permite executar JavaScript no lado do servidor. Ele utiliza um modelo orientado a eventos assíncrono. Possui [vantagens](http://blog.shinetech.com/2013/10/22/performance-comparison-between-node-js-and-java-ee/) em relação a um [servidor Java](http://blog.shinetech.com/2011/06/10/nodejs-from-the-enterprise-java-perspective/) e foram discutidas na revista [MundoJ](http://www.mundoj.com.br/62conteudo.shtml). Para trabalhar com WebSocket em NodeJS, existem várias implementações como [Socket.IO](socket.io) e [Socket.JS](https://github.com/sockjs/sockjs-node/). Neste artigo vamos utilizar a segunda.

\[sourcecode language="javascript"\] var http = require('http'); var sockjs = require('sockjs'); var clients = {};

var sockjs\_echo = sockjs.createServer();

function broadcast (message, exclude) { console.log("mensagem a ser enviada: "+ message); for ( var i in clients ) { if ( i != exclude ) { console.log("Cliente selecionado para enviar mensagem"+ clients\[i\]); clients\[i\].write( message ); }

} }

sockjs\_echo.on('connection', function(conn) { //guarda cliente conectado clients\[conn.id\] = conn;

//Quando receber uma mensagem, faz o broadcast conn.on('data', function(data) { broadcast(data); }); });

//criando um servidor http para ouvir o serviço var server = http.createServer();

//criando o canal de comunição websocket caelum sockjs\_echo.installHandlers(server, {prefix:'/caelum'});

server.listen(8082, '127.0.0.1'); \[/sourcecode\]

Para executar esse código você precisa estar com o [Node](nodejs.org) e o [SocketJS](https://github.com/sockjs/) instalado. Depois é só executar no terminal o comando: node <nomeArquivo>.js

**_Server side - JavaEE (Glassfish 4.0)_**

Em Java, você deve criar um projeto JavaEE e ter o GlassFish 4.0 instalado.

O código é bem simples e auto-explicativo, basta utilizar anotações com @ServerEndpoint e anotações de callback como @OnMessage, @OnOpen.

\[sourcecode language="java"\] @ServerEndpoint("/caelum") public class CaelumServer { @OnOpen public void onOpen(Session cliente) { System.out.println("Novo cliente" + cliente); }

@OnClose public void onClose(Session cliente) { System.out.println("Cliente desconectado" + cliente); }

@OnMessage public void message(String message, Session client) throws IOException {

Set<Session> openSessions = client.getOpenSessions(); for (Session session : openSessions) { session.getBasicRemote().sendText(message); } } } \[/sourcecode\]

O lado do servidor está pronto, temos o mesmo serviço WebSocket implementado por duas tecnologia diferentes e podendo ser acessado por um cliente JavaScript ou Java.

_**Conheça o Tyrus**_

O Projeto [Tyrus](https://tyrus.java.net/) é a implementação referência para a API de WebSocket. Assim como o [Weld](http://weld.cdi-spec.org), o projeto possui implementações que podem ser utilizadas para um servlet container ou standalone.

Vamos implementar um cliente Tyrus para acessar os dois serviços que criamos. Para isso, crie um projeto maven e coloque a seguinte dependência.

\[sourcecode language="xml"\] <dependency> <groupId>org.glassfish.tyrus.bundles</groupId> <artifactId>tyrus-standalone-client</artifactId> <version>1.4</version> </dependency> \[/sourcecode\]

Vamos criar duas classes de Cliente. A primeira apenas recebe a mensagem, já a segunda envia e recebe também. Para isso basta utilizarmos a anotação @ClientEndPoint.

\[sourcecode language="java"\] @ClientEndpoint public class ChatClientReceiver { @OnOpen public void onOpen(Session session) { System.out.println("Client Receiver conectado: " + session); }

@OnMessage public void processMessage(String message) { System.out.println("Recebendo no Client Receiver: " + message); } }

@ClientEndpoint public class ChatClientSender { @OnOpen public void onOpen(Session session) { try { System.out.println("Client Sender conectado: " + session); session.getBasicRemote().sendText("Mensagem Enviada Via WebSocket"); } catch (IOException e) { e.printStackTrace(); } }

@OnMessage public void processMessage(String message) { System.out.println("Recebendo no Client Sender: "+ message); } } \[/sourcecode\]

Agora vamos criar uma classe que usa a API do Tyrus para acionar os clientes

\[sourcecode language="java"\] public class TestClient { public static void main(String\[\] args) { WebSocketContainer container = ContainerProvider.getWebSocketContainer(); String uri = "ws://127.0.0.1:8082/caelum/websocket"; // String uri = "ws://127.0.0.1:8080/WebSocket/caelum"; try { container.connectToServer(ChatClientReceiver.class, URI.create(uri)); Thread.sleep(5000); container.connectToServer(ChatClientSender.class, URI.create(uri)); } catch (DeploymentException | IOException | InterruptedException e) { e.printStackTrace(); } } } \[/sourcecode\]

O código é bem simples, a única peculiaridade é que a primeira URI acessa o WebSocket Node.JS, já a segunda o Java. O sleep na Thread é só para dar um tempo e termos certeza que o primeiro CaelumClientReceiver conectou-se ao serviço.

Concluindo, um dos artigos mais legais que eu já li e que me motivou a fazer parte da equipe Caelum foi como montar um [serviço JAX-WS sem servidor de aplicação](https://blog.caelum.com.br/webservices-sem-servidor-de-aplicacao-no-java-6/), a ideia aqui é mostrar que com WebSocket também podemos fazer o mesmo. Também é importante salientar a integração entre tecnologias diferentes. No exemplo, trocamos uma simples mensagem, mas nada impediria de ser um JSON.
