---
title: "A nova API do JMS 2.0 no Java EE 7"
date: "2013-11-26"
author: "mario.amaral"
authorEmail: "mario.amaral@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Junto com o [Java EE 7](https://blog.caelum.com.br/novidades-javaee7-2/ "Novidades no JavaEE 7") saiu uma nova versão da especificação de mensageria, o [JMS](https://jms-spec.java.net/ "JMS page"). Agora na versão 2.0, a integração com o `CDI` e uma reformulação geral nas APIs simplificaram muito a vida do desenvolvedor.

A principal mudança foi a inclusão das interfaces [JMSContext](http://docs.oracle.com/javaee/7/api/javax/jms/JMSContext.html "JMSContext javadoc"), [JMSProducer](http://docs.oracle.com/javaee/7/api/javax/jms/JMSProducer.html "JMSProducer javadoc") e [JMSConsumer](http://docs.oracle.com/javaee/7/api/javax/jms/JMSConsumer.html "JMSConsumer javadoc"), reduzindo muito o código e a complexidade  para enviar ou receber mensagens. Vamos comparar algumas operações comuns na api do JMS 1.1 e na nova versão, para se ter uma idéia das mudanças:

Antes para enviar uma mensagem de texto pra uma fila, era necessário o seguinte código:

\[code language="java"\] @Resource(lookup = "jms/connectionFactory") ConnectionFactory connectionFactory;

@Resource(lookup = "jms/fila") Destination fila;

public void sendMessage(String mensagem) throws JMSException { try (Connection connection = connectionFactory.createConnection()) { Session session = connection.createSession(); MessageProducer producer = session.createProducer(fila); TextMessage textMessage = session.createTextMessage(mensagem); producer.send(textMessage); } } \[/code\]

Agora, com a nova api:

\[code language="java"\] @Resource(lookup = "jms/connectionFactory") ConnectionFactory connectionFactory;

@Resource(lookup="jms/fila") Destination fila; public void sendMessageNew (String body) { try (JMSContext context = connectionFactory.createContext();){ context.createProducer().send(fila,body); } } \[/code\]

O `JMSContext` encapsula toda a complexidade para se enviar uma mensagem, deixando o código bem mais limpo. É possível deixar o código ainda melhor, injetando o `JMSContext` usando o `CDI`:

\[code language="java"\] @Inject @JMSConnectionFactory("jms/connectionFactory") JMSContext context;

@Resource(mappedName = "jms/fila") Destination fila;

public void sendMessageNew(String mensagem) { context.createProducer().send(fila, mensagem); } \[/code\]

A anotação `@JMSConnectionFactory` indica ao `CDI` qual a `ConnectionFactory` que deve ser usada para criar o `JMSContext`.

Para receber uma mensagem sem o uso de um [Message-Driven Bean](http://docs.oracle.com/javaee/6/tutorial/doc/gipko.html "Message-Driven Bean Oracle docs") , o código era o seguinte:

\[code language="java"\] @Resource(lookup = "jms/connectionFactory") ConnectionFactory connectionFactory;

@Resource(lookup = "jms/fila") Destination fila;

public String receiveMessage() throws JMSException { try (Connection connection = connectionFactory.createConnection()) { connection.start(); Session session = connection.createSession(); MessageConsumer consumer = session.createConsumer(fila); TextMessage textMessage = (TextMessage) consumer.receive(); String body = textMessage.getText(); return body; } } \[/code\]

Com a nova api, não são necessárias mais que duas linhas:

\[code language="java"\] @Inject @JMSConnectionFactory("jms/connectionFactory") private JMSContext context;

@Resource(lookup = "jms/fila") Destination fila;

public String receiveMessageNew() { JMSConsumer consumer = context.createConsumer(fila); return consumer.receiveBody(String.class); } \[/code\]

Para ver mais exemplos de código, visite a página do [JCP](http://jcp.org/en/home/index "jcp") e [baixe a ultima versão da especificação](http://jcp.org/en/jsr/detail?id=343 "Especificação do JMS 2.0"). Existe um capítulo inteiro dedicado a comparar a versão antiga e nova da api, além de todas as novidades e melhorias da nova versão.

E ai, o que achou das mudanças? Já está utilizando em algum projeto?
