---
title: "Arquitetura REST com Java: JAX-RS"
date: "2009-12-15"
author: "sergiojr"
authorEmail: "sergior.jr@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

A necessidade de trocar informações entre aplicações motivou diferentes abordagens para "integração de dados". Desde soluções simples e questionáveis como utilizar um [banco de dados compartilhado](http://www.eaipatterns.com/SharedDataBaseIntegration.html), ou realizar [troca de arquivos](http://www.eaipatterns.com/FileTransferIntegration.html) até soluções mais elaboradas que utilizam objetos distribuidos ([COM](http://www.microsoft.com/com/default.mspx) e [Corba](http://www.cs.wustl.edu/~schmidt/corba-overview.html)). Em diversos momentos não temos somente a integração de sistemas diferentes mas a distribuição de um único sistema em diversas partes também pode ser integrada da mesma maneira.

A solução de integração denominada [Webservices](http://en.wikipedia.org/wiki/Web_service), que já é relativamente [simples de implementar](https://blog.caelum.com.br/webservices-sem-servidor-de-aplicacao-no-java-6/), é a mais utilizada hoje em dia, que vemos em profundidade no curso [FJ-31](http://www.caelum.com.br/curso/fj-31-java-ee-web-services/).

A Web é amplamente utilizada e reconhecida principalmente por sua arquitetura robusta, tolerante a falhas e escalável. Quem sustenta a Web nesses fatores e lhe dá todo este poder é o protocolo HTTP. Este protocolo inocente que utilizamos "meio que sem saber" em nossos navegadores de internet está presente na Web inteira, e inclusive em nossos Webservices. Não seria ótimo se eles tirassem proveito das caracteristicas do protocolo HTTP, sem que isso nos desse muito trabalho?

A especifição [JSR-311 JAX-RS](http://jcp.org/aboutJava/communityprocess/final/jsr311/index.html) de Restful webservices (que faz parte agora do [Java EE 6](http://java.sun.com/developer/technicalArticles/JavaEE/JavaEE6Overview.html)) tornou isso simples e possível. Diferentemente do tradicional SOAP - em sua versão amplamente utilizada - e WSDL, o JAX-RS foca um pouco mais em URIs e nos detalhes do protocolo HTTP para se beneficiar de seus recursos.

Como utilizamos o JAX-RS para buscar dados de um `Pedido`?

\[java\] @Path("/pedido/{id}") public class PedidoResource { @GET @Produces( { MediaType.APPLICATION\_XML }) public Pedido getPedidoById(@PathParam("id") Long id) { PedidoDAO pedidoDAO = new PedidoDAO(); Pedido pedido = pedidoDAO.getPedidoById(id); return pedido; } } \[/java\]

Através da classe `PedidoResource`, disponibilizaremos os dados de nossos pedidos no formato XML. Vamos supor que configuramos que este serviço esteja disponível no endereço: _http://caelum.com.br/rest/pedido_. Para conseguirmos informações sobre o pedido 10, podemos acessar a url _http://caelum.com.br/rest/pedido/10_, através de nosso browser de internet favorito. E assim receberiamos um resultado parecido com este:

\[xml\] <pedido> <dataPedido>2009-12-10T18:50:57.173-02:00</dataPedido> <descricao>Pedido 10</descricao> <id>10</id> <total>3000.25</total> </pedido> \[/xml\]

A api JAX-RS nos permite trabalhar com o que foi denominado Restful WebServices. E segundo a arquitetura REST nós devemos expor as informações importantes de nossa aplicação como _recursos_. Para isso precisamos criar uma classe que é definida pela especificação como `RootResource`. Em nosso exemplo a classe `PedidoResource` é a nossa , e para ser acessivel aos clientes fornecemos a ela um url através da anotaçao @Path("/pedido/{id}"), e o próprio JAX-RS reconhece {id} como sendo um parametro que é enviado através do url.

Um recurso pode responder a operações do protocolo HTTP, dentre as quais destacam-se: POST, GET, PUT e DELETE. Nós escolhemos que nosso serviço responderá apenas a solitações do tipo GET, a anotação @GET acima do método `getPedidoById` é quem define isso. Essas anotações de caminho e de qual método HTTP podem acessar determinado método estão presentes em frameworks como o [Spring MVC](http://static.springsource.org/docs/Spring-MVC-step-by-step/) e o [VRaptor](http://www.vraptor.com.br/).

Depois dizemos que uma solitação GET para nosso recurso irá produzir um resposta do tipo XML, através da anotação @Produces( { MediaType.APPLICATION\_XML }). É impotante destacar que esta anotação não é a responsável por serializar o objeto no formato XML. O JAX-RS usa o [JAX-B](https://blog.caelum.com.br/jaxb-xml-e-java-de-maos-dadas/) como serializador padrão, basta para isso colocar a anotação @XmlRootElement na classe desejada, assim como nós fizemos em nossa classe pedido.

\[java\] @XmlRootElement public class Pedido {

private Long id; private String descricao; private double total; private Calendar dataPedido; //Getters e Setters...

} \[/java\]

Agora que entendemos melhor o nosso serviço, ou seja, como acessar nosso _recurso_, podemos olhar melhor para nosso cliente. Como nosso cliente do serviço web pode ser um simples browser de internet, conseguimos usar o browser para consumir nosso serviço web porque eles já estão bem acostumados a realizar operações HTTP do tipo GET.

Mas os browser's não são nossos únicos clientes. Podemos criar diferentes tipos, e inclusive ter aplicações desktop como clientes. Vejamos um exemplo de uma aplicação cliente, que o usa a API do [httpclient](http://hc.apache.org/httpcomponents-client-ga/) do grupo apache:

\[java\] public class CaelumRestClient {

public static void main(String\[\] args) throws Exception { HttpClient httpClient = new HttpClient();

GetMethod httpMethod = new GetMethod("http://caelum/rest/pedido/20");

httpMethod.addRequestHeader("Accept", "application/xml"); httpClient.executeMethod(httpMethod); Scanner scan = new Scanner(httpMethod.getResponseBodyAsStream()); PrintStream ps = System.out; while (scan.hasNext()) { ps.println(scan.nextLine()); } httpMethod.releaseConnection(); } } \[/java\]

Este cliente é bem simples, ele faz apenas o mesmo trabalho que o browser já havia feito. Mas nada nos impede de implementar coisas bem mais interessantes. Poderiamos fazer com que este cliente desserializasse nosso objeto pedido. Para isso precisarimos apenas de um parser XML para extrair os valores do xml e depois popular um objeto Pedido com estes dados. A partir daí poderiamos utilizar este objeto em nossa aplicação.

A única [limitação](http://guilhermesilveira.wordpress.com/2009/12/03/resteasy-where-did-the-hypermedia-go-to/) é que esta representação XML não nos diz nada sobre quais ações estão disponiveis para o nosso objeto Pedido, nem mesmo quais as relações desse recurso com o mundo afora. Uma alternativa seria utilizar o [restfulie](http://github.com/caelum/restfulie-java) framework [muito discutido hoje em dia](http://www.infoq.com/news/2009/12/restfulie) e idealizado por [Guilherme Silveira](http://guilhermesilveira.wordpress.com/) e desenvolvido em conjunto com o pessoal da [Caelum](http://www.caelum.com.br), que usa o conceito de hypermedia para expor além dos dados as ações que um determinado objeto pode realizar.

Em breve teremos um post do próprio Guilherme sobre a importância do conteúdo hypermedia na arquitetura REST.
