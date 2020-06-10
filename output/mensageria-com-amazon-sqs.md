---
title: "Mensageria com Amazon SQS"
date: "2013-10-15"
author: "Natanael Pantoja"
authorEmail: "natanael.pantoja@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

São muitos os sistemas de mensageria disponíveis. Ou Message Oriented Middleware, MOM, se preferir.

Alguns exemplos que temos de MOM são: [IBM MQSeries](http://www-03.ibm.com/software/products/us/en/wmq/ "IBM MQSeries"), [RabbitMQ](http://www.rabbitmq.com/ "RabbitMQ"), [HornetQ](http://www.jboss.org/hornetq "HornetQ"), [ActiveMQ](http://activemq.apache.org/ "ActiveMQ"). Alguns servidores de aplicação tem internamente um MOM configurado, como o JBoss, que usa o hornetQ.

A amazon tem um MOM disponibilizado como serviço, dispensando a configuração e manutenção da infraestrutura, o [SQS (Simple Queue Service)](http://aws.amazon.com/pt/sqs/ "SQS").

Como funciona o SQS? Muito simples. Os desenvolvedores podem criar número ilimitado de filas e número ilimitado de mensagens e só vai ser cobrado pelo que for transferido para fora, porque os dados transferidos entre o SQS e uma máquina no EC2 dentro de uma mesma região são gratuitos.

Vamos usar o SDK da amazon para entender o funcionamento do SQS. Primeiro temos que configurar uma fila, como mostra a figura a seguir:

[![Nova Fila](https://blog.caelum.com.br/wp-content/uploads/2013/10/nova-fila-300x175.png)](https://blog.caelum.com.br/wp-content/uploads/2013/10/nova-fila.png)

A parametrização é tranquila, basta você dar um nome para a fila e definir algumas variáveis, como o período de visibilidade da mensagem e período de retenção da mensagem.

Então vamos ao código. Para publicar uma mensagem basta você instanciar o cliente e enviar a mensagem desejada. Lembrando que as credenciais que voçê tem que passar são as credenciais criadas na amazon para que uma aplicação a acesse de fora.

\[java\] String url = "https://sqs.us-east-1.amazonaws.com/403828569551/caelum-queue-jms"; AmazonSQSClient sqsClient = new AmazonSQSClient(awsCredentials); sqsClient.sendMessage(new SendMessageRequest() .withQueueUrl(url).withMessageBody("Texto da MSG.")); \[/java\]

Você pode visualizar a mensagem publicada utilizando o [plugin da amazon](https://blog.caelum.com.br/comecando-com-aws-eclipse-plugin-com-aws-sdk/ "Plugin Amazon") pro eclipse. Veja a imagem a seguir:

[![Mensagem Enviada](https://blog.caelum.com.br/wp-content/uploads/2013/10/Screen-Shot-2013-10-11-at-09.56.50-300x115.png)](https://blog.caelum.com.br/wp-content/uploads/2013/10/Screen-Shot-2013-10-11-at-09.56.50.png)

Para receber a mensagem publicada basta criar um objeto do tipo `ReceiveMessageRequest` que aponta para a nossa fila na amazon instanciar nosso cliente e usar o método `receiveMessage`.

\[java\] ReceiveMessageRequest receiveMessageRequest = new ReceiveMessageRequest("https://sqs.us-east-1.amazonaws.com/403828569551/caelum-queue-jms"); AmazonSQSClient sqsClient = new AmazonSQSClient(awsCredentials); List<Message> messages = sqsClient.receiveMessage(receiveMessageRequest).getMessages(); for (Message message : messages) { System.out.println("MSG"); System.out.println("MsgID: " + message.getMessageId()); System.out.println("Identificador Único MSG: " + message.getReceiptHandle()); System.out.println("MD5 do Corpo da MSG: " + message.getMD5OfBody()); System.out.println("Corpo: " + message.getBody()); for (Entry<String, String> entry : message.getAttributes().entrySet()) { System.out.println("Atributos"); System.out.println("Nome: " + entry.getKey()); System.out.println("Valor: " + entry.getValue()); } } \[/java\]

Para cada mensagem enviada o SQS gera um ID único para e um hash para você garantir que a mensagem não foi alterada e para poder ler a mensagem quantas vezes for necessário. Outro ponto interessante é que o SQS não faz `FIFO (Fist in Fist out)`, ou seja, diferente dos outros sistemas de mensageria ele não remove automaticamente a mensagem já lida. Ela permanece lá até o tempo de validade dela expirar ou você executar a remoção de forma programática. Mas enquanto a mensagem está sendo lida ela fica bloqueada para que outros consumidores não leiam a mensagem igual. Se você precisa de `FIFO`, você tem que projetar sua aplicação para gerenciar isso.

É simples fazer essa remoção:

\[java\] String url = "https://sqs.us-east-1.amazonaws.com/403828569551/caelum-queue-jms"; AmazonSQSClient sqsClient = new AmazonSQSClient(awsCredentials); ReceiveMessageRequest receiveMessageRequest = new ReceiveMessageRequest(url); List<Message> messages = sqsClient.receiveMessage(receiveMessageRequest).getMessages(); System.out.println("Deletando a MSG.\\n" + messages.get(0).getBody()); String messageReceiptHandle = messages.get(0).getReceiptHandle(); sqsClient.deleteMessage(new DeleteMessageRequest().withQueueUrl(url).withReceiptHandle(messageReceiptHandle)); \[/java\]

Um outro serviço de mensageria que está se popularizando muito é o [IronMQ](http://dev.iron.io/mq/ "IronMQ") ,que tem uma [API REST](http://dev.iron.io/mq/reference/api/ "REST API"), facilitando bastante o uso do serviço por aplicações web. O [IronMQ](http://dev.iron.io/mq/ "IronMQ") também disponibiliza SDKs para diferentes linguagens. Vale a pena dar uma conferida.

Para aprender mais sobre o SDK da amazon e sobre o SQS, não deixe de ver o [FAQ](http://aws.amazon.com/pt/sqs/faqs/) e a [documentação](http://aws.amazon.com/pt/documentation/), além desse nosso [código de exemplo](https://github.com/natanaelpantoja/amazonsqs-example).
