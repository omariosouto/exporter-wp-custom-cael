---
title: "WebServices com o XFire"
date: "2007-03-25"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nessa segunda feira o Fábio Kung estava me contando de algumas vantagens que enxergava no [XFire](http://xfire.codehaus.org) para criar webservices, e eu logo comentei que preferia o JBossWS porque bastava eu colocar um jar no diretório de deploy, [conforme já blogamos anteriormente](https://blog.caelum.com.br/criando-um-webservice-com-a-jsr-181/).

Uma das vantagens que ele citou é do Jetty embutido que existe no XFire, que facilita em muito o desenvolvimento, sem a necessidade de fazer deploys ou reiniciar o JBoss. Considere o bean, o mesmo do post sobre o JBossWS, que quero expor como um webservice:

\[java\] @WebService public class AgenteDeReservaBean { @WebMethod public boolean reserva(@WebParam(name = "nome") String nome, @WebParam(name = "voo") String voo) {

// logica de negocios, ou delegacao para o BO return false } } \[/java\]

Agora, para verificar o WSDL gerado e fazer o deploy do serviço e até mesmo invocá-lo, basta seguir o seguinte código:

\[java\] public class LigaJettyDoXFire { public static void main(String\[\] args) throws Exception { XFire xfire = XFireFactory.newInstance().getXFire();

AnnotationServiceFactory factory = new AnnotationServiceFactory(xfire.getTransportManager()); Service service = factory.create(AgenteDeReserva.class); xfire.getServiceRegistry().register(service);

XFireHttpServer server = new XFireHttpServer(xfire); server.start(); } } \[/java\]

Através do `XFireHttpServer` o XFire inicia um jetty embutido rodando na porta 8081. Acessando `localhost` nessa porta você terá uma lista dos serviços registrados que foram expostos pelo XFire. A velocidade de inicialização do servidor é simplesmente impressionante. Essa dica do Fábio já aplicamos em um projeto nessa mesma semana, e diminuiu bastante o tempo gasto para desenvolvimento e testes.

O Fábio também me mostrou o recurso do XFire fazer uma invocação dinâmica, sem necessidade de stubs. Ainda não fiz nenhum teste, até porque não sei como ele vai serializar em XML uma array de objetos para que eles estejam conforme os complex types utilizados no WSDL. Agora é só torcer para que o [gsoap](http://www.cs.fsu.edu/~engelen/soap.html) não tenha problemas para gerar os stubs, pois quem vai consumir esse serviços são pequenos dispositivos que rodam código escrito em C.

É impressionante como diversas ferramentas e frameworks clássicos da Apache, como nesse caso o Axis, perdem cada vez mais espaço para os projetos concorrentes pertencentes aos outros grupos, tais como o opensymphony, codehaus e JBoss. Apache que se cuide...
