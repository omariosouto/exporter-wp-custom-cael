---
title: "WebSockets HTML5 em Java com Jetty: Web em tempo real"
date: "2012-08-23"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Navegadores são bons em fazer requisições para o servidor. Mas e o contrário? Fazer o servidor enviar dados pro navegador em momentos arbitrários sempre foi um trabalho. Ajax reverso, comet, long polling são algumas das gambiarrastécnicas usadas. Mas o HTML5 trouxe uma grande novidade: a API de **[WebSockets](https://developer.mozilla.org/en-US/docs/WebSockets)**.

[WebSockets](https://developer.mozilla.org/en-US/docs/WebSockets) permitem abrir uma conexão com o servidor remoto e trafegar dados arbitrariamente do servidor para o cliente e vice-versa. Dá pra fazer muita coisa com isso. Um chat em tempo real, um mecanismo de sincronização, e até streaming de dados binários.

No último [QCon SP 2012](https://blog.caelum.com.br/qcon-sp-2012-como-foi-a-terceira-edicao-do-evento/), usei WebSockets [na minha palestra de Web Mobile](http://sergiolopes.org/palestra-mobile-web/) para sincronizar os slides de apresentação com os celulares, tablets e notebooks da platéia. Todo mundo abriu uma página com os slides e, conforme a palestra andava, eu mudava o slide no telão e imediatamente a platéia via o novo slide em seus dispositivos, junto com notas e exemplos adicionais.

Os slides foram feitos em HTML, CSS e JS, e o mecanismo de sincronizar meu slide com os mais de 200 dispositivos conectados ao mesmo tempo foi WebSockets. Minha máquina no telão enviava para o servidor qual era o slide atual e este distribuía a informação pra todo mundo em tempo real.

## JavaScript no cliente

O código na página é muito simples. Você abre a conexão com o servidor e pode receber ou enviar mensagens. O envio é uma simples chamada de método e o recebimento, um callback JavaScript assíncrono. Há ainda um callback pra você saber quando a conexão for aberta.

No caso do sincronizador de slides, a versão mobile aberta nos dispositivos dos usuários recebia o ID do slide a ser mostrado:

\[javascript\] var ws = new WebSocket('ws://meuservico.com/websockets');

ws.onopen = function() { console.log('Conexão aberta com sucesso'); };

ws.onmessage = function(message) { var slide = document.getElementById(message); mostrarSlide(slide); }; \[/javascript\]

Já a máquina principal, que faz a sincronização, envia qual é o slide atual pra todo mundo:

\[javascript\] function mostrarSlide(slide) { // lógica de exibir slide...

// sincroniza dispositivos ws.send(slide.id); } \[/javascript\]

Repare como usei um protocolo bem simples, trocando apenas os IDs dos slides. Isso facilitou em ocupar menos banda e evitar deixar a rede pesada no momento da palestra.

## Servidor WebSockets com Jetty 8

O cliente JavaScript é bastante simples, mas a complexidade maior acaba ficando no servidor. WebSockets são um novo protocolo de comunicação em cima do HTTP e porta 80, e portanto exigem um servidor compatível. Há uma implementação de WebSockets muito boa no Jetty 8 para usarmos em Java, mas há outras para diversas linguagens - como o [socket.io](http://socket.io/) para Node.JS.

[Com Jetty 8, é possível usar WebSockets](http://wiki.eclipse.org/Jetty/Feature/WebSockets) através de uma Servlet especial, a `WebSocketServlet`. Herdamos dessa classe e sobrescrevemos o método `doGet` como numa Servlet comum. Mas, além disso, devemos sobrescrever o método `doWebSocketConnect` que faz a conexão no protocolo de WebSockets em si.

Sua implementação desse método deve devolver um objeto do tipo `WebSocket` que você vai criar. Para trabalhar com mensagens texto - como no nosso caso - implementamos a interface `OnTextMessage`. Essa interface nos dá três métodos: `onOpen`, `onMessage` e `onClose`.

## Servidor de sincronização com Java

A ideia do sincronizador de slides é simples: guardar todos os usuários conectados numa lista e, quando chegar uma nova mensagem, reenviamos pra todo mundo. Para saber qual usuário é o principal, que comanda a sincronização, vou usar um parâmetro na URL:

\[java\] @WebServlet(urlPatterns="/sincronizar") public class SincronizadorServlet extends WebSocketServlet { // lista de todos os usuários conectados. // cuidado com acesso concorrente, por isso uso aqui a CopyOnWriteArraySet private final Set<SyncWebSocket> usuarios = new CopyOnWriteArraySet<SyncWebSocket>(); @Override protected void doGet(HttpServletRequest req, HttpServletResponse res) throws ServletException, IOException {

// implementação do doGet aqui. // pode até deixar em branco, sem resposta.

} @Override public WebSocket doWebSocketConnect(HttpServletRequest request, String arg1) {

// faz a conexão via WebSockets e devolve um novo cliente. // devolvemos um objeto da nossa classe SyncWebSocket que implementa a lógica. // repare que ela terá acesso à lista de usuários e // ao parâmetro para saber se é o usuário principal ou não. return new SyncWebSocket(usuarios, request.getParameter("principal") != null); } } \[/java\]

Cada cliente é representado pela minha classe `SyncWebSocket` que recebe as mensagens do usuário principal e dispara para todos os outros.

\[java\] public class SyncWebSocket implements OnTextMessage { private final Set<SyncWebSocket> usuarios; private final boolean principal; private Connection connection; public SyncWebSocket(Set<SyncWebSocket> usuarios, boolean principal) { this.usuarios = usuarios; this.principal = principal; }

public void onOpen(Connection connection) { // novo usuário conectado. adicionar na lista compartilhada usuarios.add(this);

// guarda a Connection pra enviar mensagens depois this.connection = connection; } public void onClose(int arg0, String arg1) { // remove usuário da lista quando sai usuarios.remove(this); }

public void onMessage(String message) { // só recebe mensagens se esse for o usuário principal. if (principal) {

// envia a mensagem pra todo mundo, sincronizando os clientes for (SyncWebSocket usuario: usuarios) { try { usuario.connection.sendMessage(message); } catch (IOException e) { usuarios.remove(usuario); usuario.connection.close(); } } } else { throw new RuntimeException("Você não pode mandar mensagens!"); } } } \[/java\]

Para subir esse código, você vai precisar do Jetty 8. No meu caso, precisei também copiar os JARs de websockets e outras libs do Jetty pra dentro do **WEB-INF/lib** do projeto.

É um exemplo simples de sincronização, mas muito mais é possível com WebSockets. Dá pra [mandar mensagens binárias](http://www.html5rocks.com/en/tutorials/websockets/basics/), e enviar e receber mensagens ao mesmo tempo.

## Mais WebSockets e o futuro

WebSockets são suportadas nas últimas versões de todos os navegadores, incluindo os mobile. Há um detalhe apenas com relação à versão do protocolo que é suportada. Tivemos várias revisões do protocolo que foi sendo refinado com o tempo fechando bugs importantes de segurança. A versão final do RFC ainda não é suportada por todo mundo e pode gerar alguns problemas de compatibilidade para coisas mais complicadas.

A transmissão de dados binários foi adicionada recentemente ao protocolo e também não é [suportada em todos os navegadores](http://caniuse.com/websocket).
