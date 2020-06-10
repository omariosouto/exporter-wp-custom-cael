---
title: "JavaEE 6: contexto assíncrono das Servlets para o Ajax push"
date: "2010-09-27"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![](https://blog.caelum.com.br/wp-content/uploads/2010/09/ajax-150x150.jpg "ajax")Desenvolvedores de backend que precisam de muita escalabilidade já conhecem os truques de IO não-blocante há muito tempo: selector, poll e epoll no Linux, kqueue no MacOS e BSD e I/O Completion Ports no Windows. A [palestra do Renato Lucindo no QConSP](http://blog.lucindo.com.br/2010/09/27/qcon-sp-2010/) abordava questões e soluções que podiam ser implementadas com essas abordagens. Curiosamente são padrões e implementações já bastante antigos, e agora surgem novamente com força. Mas onde o IO não-blocante e estratégias de conexão podem nos ajudar numa aplicação web do dia a dia? No Ajax reverso.

Considerando o exemplo trivial de ter de atualizar o valor de um produto em leilão a cada alteração de preço, podemos fazer com que o browser dispare uma requisição Ajax a cada 5 segundos, e, caso haja alguma modificação no preço, o servidor vai responder o valor novo e via javascript atualizamos esse valor. Essa é a implementação mais básica de ajax reverso, o polling, dando a impressão para o usuário de que a aplicação está atualizada com o servidor. A desvantagem aqui é que num intervalo de cerca de 5 segundos, você pode estar vendo dados _stale_, o que é aceitável para grande maioria das aplicações.

Para algo mais sofisticado, precisamos realmente fazer um push do servidor: quando houver dados, mandamos para o cliente. Muitos se referem as técnicas de Ajax push como comet, e são duas as mais usadas: fazer um long polling, e o servidor segura a requisição aberta por alguns segundos ou até ter algum dado para enviar; ou fazer um verdade streaming, mantendo a conexão aberta por tempo indeterminado, e ir enviando os dados novos para o cliente na medida que houver. Em ambos os casos podemos facilmente implementar o cliente, mas como fazer no servidor?

No servidor, precisamos manter a socket aberta com o cliente. Em uma implementação ingênua, teremos, para cada `HttpServletResponse`, uma thread em `waiting`. Quando o evento que estamos esperando for disparado, fazemos o `notify`/`notifyAll` necessário (ou ainda usar `BlockingQueue`s). Essa implementação de _thread-per-request_ é bastante cara no Java, tanto pelo footprint de memória quanto por criar native threads. Quando o número de clientes aumentar, a grande quantidade de threads vai atrapalhar bastante o desempenho do sistema, mesmo se praticamente todas as conexões estiverem ociosas, que é um caso bastante frequente para o nosso cenário.

Em muitos casos o melhor será usar algumas poucas (muitas vezes apenas uma) threads que façam o event loop e verifiquem se algum `SocketChannel` daquele `Selector` está pronto para leitura/escrita, e realize a operação correspondente caso necessário. Implementar isso com `java.nio` não é difícil, e mais ainda, os servlet containers começaram a implementar essa estratégia e fornecer abstrações bem mais simples para não ter de trabalhar com event loops nem máquinas de estado. O [Tomcat 6 já trazia uma abstração para Comet](http://tomcat.apache.org/tomcat-6.0-doc/aio.html) com diversos callbacks para o estado de cada socket, enquanto [o Jetty 6 já fazia o mesmo através de uma implementação limitada de continuations](http://docs.codehaus.org/display/JETTY/Continuations), porém bastante simples. Em ambos a ideia era fornecer uma maneira de suspender e continuar o processamento daquela requisição, e que, enquanto o processamento estivesse suspenso, aquela thread não entrasse em estado blocked, podendo voltar a processar outras requisições que possuíssem sockets prontas para ler/escrever sem contenção.

Desde novembro de 2009 não precisamos mais adotar uma solução específica de servlet container: na [Servlet 3](http://jcp.org/aboutJava/communityprocess/final/jsr315/index.html) temos suporte ao que foi batizado de contexto assíncrono. Se precisamos fazer um push para todos os clientes conectados, basta avisarmos ao container que não queremos que a requisição termine ao fim do método `service` (e, portanto, do `doGet`, `doPost`, etc). Fazemos isso com um simples `req.startAsync()`. Depois adicionamos o contexto devolvido em uma coleção, para poder fazer o broadcast do evento que os clientes estão todos aguardando.

\[java\] private Queue<AsyncContext> clients = new ConcurrentLinkedQueue<AsyncContext>();

protected void doGet(HttpServletRequest req, HttpServletResponse arg1) throws ServletException, IOException { AsyncContext ctx = req.startAsync(); ctx.setTimeout(3000000); clients.add(ctx); System.out.println("novo cliente conectou." ); } \[/java\]

Você já pode rodar esse teste com seu servlet container ([Tomcat 7](http://tomcat.apache.org/download-70.cgi) e [Jetty 8](http://dist.codehaus.org/jetty/) possuem suporte). Para isso falta [configurar a Servlet através de anotações](https://blog.caelum.com.br/java-ee6-comecando-com-as-servlets-3-0/) para que ela tenha suporte a chamada assíncrona:

\[java\] @WebServlet(urlPatterns = { "/subscribe" }, asyncSupported = true) public class ChatServlet extends HttpServlet { \[/java\]

Se você fizer uma requisição para `/subscribe` do seu servidor, perceberá que o browser ficará em aguardo de uma resposta, já que o servidor não encerrou a requisição. Queremos disparar uma mensagem para todos os clientes que estiverem esperando nosso push (que tiver feito o "_subscribe_"). Para isso, vamos fazer com que o post em `/subscribe` envie uma mensagem. Por enquanto vamos adicionar essa mensagem no fim de nossa fila, com um id próprio:

\[java\] private BlockingQueue<String> messages = new LinkedBlockingQueue<String>(); private AtomicInteger contador = new AtomicInteger();

protected void doPost(HttpServletRequest req, HttpServletResponse arg1) throws ServletException, IOException { System.out.println("enviando mensagem para todos cliente"); messages.add(String.format("mensagem número %d %n", contador.incrementAndGet())); } \[/java\]

Precisamos que alguém seja responsável por verificar se há mensagens novas na fila e, caso positivo, enviar essa mensagem a todos os clientes da nossa lista de `AsyncContext`. Faremos isso numa thread que será iniciada durante a criação de nossa servlet, e ficará esperando por mensagens novas no método blocante `BlockingQueue.take`:

\[java\] public void run() { while (true) { final String message = messages.take(); for (final AsyncContext ctx : clients) { public void run() { PrintWriter writer = ctx.getResponse().getWriter(); writer.println(message); writer.flush(); } } } } \[/java\]

Removi o tratamento das checked exceptions por uma questão de legibilidade, e o [código completo desta ChatServlet pode ser encontrada aqui](http://github.com/peas/asyncservlets-test/blob/master/src/main/java/br/com/caelum/chat/ChatServlet.java).

E como realizar um teste com milhares de clientes? Isso pode ser um problema: caso você utilize alguma biblioteca que faça _thread-per-request_, não vai conseguir abrir muitos clientes simultaneamente, dado o número excessivo de threads criadas. Você vai precisar fazer um cliente usando `java.nio` puro, ou ainda em conjunto com alguma biblioteca http que utilize a API não-blocante. Esse é o caso das novas versões do [Apache Http Core](http://hc.apache.org/httpcomponents-core-ga/index.html), e [aqui há um pequeno teste de exemplo](http://github.com/peas/asyncservlets-test/blob/master/src/main/java/br/com/caelum/chat/NHttpClient.java).

Você ficará bastante impressionado ao ver que uma máquina caseira pode suportar mais de 5 mil clientes simultâneos com o Jetty 8, e que só vai parar nesse número provavelmente pelo [limite padrão de arquivos abertos](http://www.cyberciti.biz/faq/linux-increase-the-maximum-number-of-open-files/) no seu sistema operacional.

Economizar recursos caros, como threads, conexões e descritores de arquivos, é sempre uma preocupação. Na Servlet 3 conseguimos balancear esses recursos, minimizando o uso de memória e processamento, porém pagando um pouco mais alto no uso de descritores abertos, mas que ajudam muito nesse cenário de ajax reverso com push.

Expressivos agradecimentos ao [Renato Lucindo](http://blog.lucindo.com.br/) e [Gleicon Moraes](http://zenmachine.wordpress.com/) pelo aprendizado, diversas explicações, conversas e correções.
