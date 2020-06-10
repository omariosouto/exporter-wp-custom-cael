---
title: "Polling Consumer no SQS com Apache Camel"
date: "2015-03-03"
author: "Natanael Pantoja"
authorEmail: "natanael.pantoja@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando falamos de integração de sistemas, não podemos deixar passar o conceito de  mensageria, extremamente útil quando tratamos de integração de sistemas.  No curso de [SOA](https://www.caelum.com.br/curso-java-ee-soa-web-services-mensageria/) apresentamos o Apache Camel, um excelente framework de integração que implementa diversos [padrões](http://www.enterpriseintegrationpatterns.com/toc.html) que nos ajudam bastante no dia a dia de um grande projeto. Sempre comento com os alunos que entender desses padrões é essencial para quem se aprofundar no mundo da integração de sistemas. Uma pergunta frequente é se consigo fazer o [polling de mensagens](http://www.enterpriseintegrationpatterns.com/PollingConsumer.html) no [SQS](http://aws.amazon.com/pt/sqs/?nc2=h_ls), que inclusive já falei dele [anteriormente](https://blog.caelum.com.br/mensageria-com-amazon-sqs/).

Com o apache Camel é muito simples, pois ele já disponibiliza diversos [componentes](http://camel.apache.org/components.html), Entre eles, há uma [lista](http://camel.apache.org/aws.html) específica para os serviços da Amazon.

Vamos começar criando nossas credenciais na Amazon AWS e nosso cliente do SQS.

\[code language="java"\] AWSCredentials awsCredentials = new BasicAWSCredentials("myAccessKey", "mySecretKey"); AmazonSQS client = new AmazonSQSClient(awsCredentials); \[/code\]

Este cliente tem que ser registrado via JNDI para que fique visível ao componente do Camel.

\[code language="java"\] JndiRegistry registry = new JndiRegistry(); registry.bind("sqsClient", client); \[/code\]

Logo após podemos criar nosso Contexto para que nossas rotas possam ser executadas.

\[code language="java"\] DefaultCamelContext ctx = new DefaultCamelContext(registry); \[/code\]

Iremos criar nossas rotas usando a DSL que o Camel disponibiliza para não ter que usar XML.

\[code language="java"\] public class SQSRouteBuilder extends RouteBuilder { public void configure() { from("aws-sqs://caelumQueue?amazonSQSClient=#sqsClient&deleteIfFiltered=false") //.filter(simple("${header.identity} == 'login'")) .log("We have a message! ${body}") .process(new Processor() { @Override public void process(Exchange exchange) throws Exception { String messagestring = exchange.getIn().toString(); System.out.println("messagestring : " + messagestring); } }) .to("file:target?fileName=sqs-msg.xml"); }

} \[/code\]

Explicando um pouco mais a DSL, basicamente, estou passando o padrão de URI do Camel (aws-sqs://queue-name\[?options\])

amazonSQSClient: Parêmetro que foi registrado via JNDI.  Com isso o componente vai conseguir se conectar ao SQS e fazer o consumo da fila.

Vale lembrar que temos que passar o # antes do nome registrado.

\[code language="java"\] registry.bind("sqsClient", client); \[/code\]

deleteIfFiltered: Esse parâmetro é importante, pois, diferentemente de outros MOM, o SQS não faz FIFO (First In First Out) e a responsabilidade de remover a mensagem é totalmente sua.

Agora é adicionar essa rota ao nosso contexto e testar. Não esqueça que para esse exemplo funcionar você precisa configurar seu ambiente na Amazon.

\[code language="java"\] ctx.addRoutes(new SQSRouteBuilder()); ctx.start(); Thread.sleep(100000); \[/code\]

Você pode entender melhor a DSL do Camel com outro artigo publicado [aqui](https://blog.caelum.com.br/padroes-de-integracao-com-apache-camel/). E ler todas as opções que você tem para usar na rota do SQS [aqui](http://camel.apache.org/aws-sqs.html).

Se está usando Maven, precisará das dependências do SQS e do SDK da Amazon.

\[code lamguage="xml"\]

<dependency> <groupId>org.apache.camel</groupId> <artifactId>camel-aws</artifactId> <version>2.14.1</version> </dependency>

<dependency> <groupId>com.amazonaws</groupId> <artifactId>aws-java-sdk</artifactId> <version>1.9.13</version> </dependency> \[/code\]

Você também vai precisar configurar seu contexto JNDI. Crie um arquivo chamado. jndi.properties com o seguinte conteudo.

\[code\] java.naming.factory.initial = org.apache.camel.util.jndi.CamelInitialContextFactory \[/code\]

O projeto completo você pode encontrar no meu [Github.](https://github.com/natanaelpantoja/camel-sqs)
