---
title: "Usando o padrão observer com CDI para lidar com o Kafka"
date: "2016-06-14"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Há pouco tempo passei por um problema que é rotina na vida de todo desenvolvedor: [acoplamento](https://blog.caelum.com.br/orientacao-a-objetos-uma-outra-perspectiva-sobre-o-acoplamento/). Identificamos que um sistema está acoplado quando é difícil mudar a implementação da solução que foi escolhida para determinado problema, quando uma classe depende de várias outras para funcionar, quando há muito o uso do [design patterns RCP](http://desciclopedia.org/wiki/Gambi_Design_Patterns), dentre outros.

No meu caso, o problema era retirar mensagens de um servidor de mensageria. Teria que fazer da forma menos acoplada possível, pois a probabilidade de trocar esse servidor é grande. Não deveria nem ter relacionamento com a [especificação padrão JMS](https://blog.caelum.com.br/a-nova-api-do-jms-2-0-no-java-ee-7/), haja vista que escolhemos o [Kafka](https://www.infoq.com/articles/apache-kafka) como servidor e ele, por uma [série de motivos](http://stackoverflow.com/questions/30453882/apache-kafka-is-jms-api), incluindo desempenho, não segue a especificação.

Talvez haja mais [servidores de mensageria do que de aplicação JavaEE](https://www.quora.com/How-is-Kafka-different-from-typical-JMS-message-brokers-like-IBM-MQ-Active-MQ-etc), então o analista tem muitos [trade-offs para avaliar](http://stackoverflow.com/questions/27666943/activemq-vs-apollo-vs-kafka). De qualquer forma, mesmo que futuramente haja uma troca para um servidor JMS, as partes dos sistema que usam esse serviço não devem ser alteradas, [aí que está o desafio do acoplamento.](https://www.infoq.com/news/2016/06/programmers-write-better-code)

> _The more coupling we have between objects, components, modules, or systems, the more we experience many consequences. These consequences include but are not limited to difficulty of modification, propagation of failure, inability to scale because of contention, and performance issues due to dependent actions._ 

## Encapsulando a API.

A [API do Kafka](https://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html) é bem tranquila de usar, baseada em Producer e Consumer, portanto o primeiro desafio era [encapsular](https://dzone.com/articles/encapsulation-i-dont-think-it-means-what-you-think) o uso em uma interface mais simples ainda. Outro problema aparece, o [encapsulamento](https://blog.caelum.com.br/revisitando-a-orientacao-a-objetos-encapsulamento-no-java/). Nenhuma das partes do sistema devem saber sobre os detalhes de como foi implementado o serviço de mensageria, devem apenas saber o mínimo possível para utilizá-lo.

\[java\] public interface ServicoMensageria { public void assinar(String topico);

public void enviarMensagem(String topico, String mensagem); } \[/java\]

Da interface acima, poderíamos diminuir ainda mais, como omitir método _assinar,_ [identificando que o contexto da aplicação foi inicializado](http://docs.oracle.com/javaee/6/api/javax/servlet/ServletContextListener.html) e fazendo a assinatura automática.

Agora basta usar um framework de [injeção de dependências](https://blog.caelum.com.br/ioc-e-di-para-frameworks-mvc/) para usar o serviço de mensageria, sem saber quem é o [responsável por prover a implementação.](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/)

\[java\] @Inject private ServicoMensageria servicoMensageria;

public void envia(){ servicoMensageria.enviarMensagem("meutopico","mensagem") } \[/java\]

## O padrão Observer

Agora passamos para o próximo problema, o cliente da API já fez a assinatura de um fila de mensagens e precisa ser notificado quando houver alguma mensagem na fila. Para isso, nada mais adequado do que implementar o padrão [Observer](http://www.journaldev.com/1739/observer-design-pattern-in-java-example-tutorial). Ao invés de implementar o padrão, usei uma [api que já implementa, inclusive discutida pelo Sérgio Lopes anteriormente, o CDI](https://blog.caelum.com.br/diminua-suas-dependencias-com-os-eventos-do-cdi/).

 

\[java\] public class OuvidorMensageriaNFe {

/\*\* \* \* SerieMensagens é uma wrapper que encapsula uma List <Mensagem>; \*\*/ public void ouvindoMensagens(@Observes SerieMensagens serieMensagens) {

System.out.println("mensagem recebida "+ serieMensagens); } } \[/java\]

## Lidando com o sincronismo.

Agora precisamos retirar mensagens da fila e produzir eventos do CDI. Só que aqui temos um sério problema: a [API de Consumer do Kafka](https://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html) fica em um loop infinito _while true_ e caso haja mensagem um evento deve ser disparado.

\[java\] try (KafkaConsumer<String, String> consumer = new KafkaConsumer<> (consumerProperties);) {

consumer.subscribe("meutopico");

while (true) {

List < Mensagem > mensagens = new ArrayList <> (); ConsumerRecords < String, String > records = consumer.poll(pollPadrao);

for (ConsumerRecord < String, String > record : records) {

mensagens.add(new Mensagem(record.topic(), record.value())); } if (!mensagens.isEmpty()) {

//cuidado redobrado aqui evento.fire(new SerieMensagens(mensagens));

mensagens = new ArrayList<>(); }

} } \[/java\]

O problema **aqui é que essa solução não escala e será muito lenta**, devido a basicamente um limitador: o **CDI só trabalha com eventos [síncronos](https://wmarkito.wordpress.com/2012/07/20/cdi-events-synchronous-x-asynchronous/)**, ou seja, quando a Thread chega na linha _evento.fire()_, ela só passa para o próximo passo **quando todos os responsáveis** por ouvir o evento @Observer SerieMensagens terminam o seu processamento. Há como contornar esse "problema", tanto no [produtor do evento como no observador do evento](http://piotrnowicki.com/2013/05/asynchronous-cdi-events/). Neste, poderíamos utilizar o [Asynchonous](https://docs.oracle.com/javaee/6/tutorial/doc/gkkqg.html) e naquele bastaria lançar o evento em uma thread separada.

 

\[java\]

if (!mensagens.isEmpty()) {

final List < Mensagem > mensagensParaEnviar = new ArrayList <> (mensagens); new Thread(() -> { evento.fire(new SerieMensagens(mensagensParaEnviar));}).start();

mensagens = new ArrayList <> (); }

\[/java\]

## Concluindo.

Pensar em [baixo acoplamento, alta coesão e encapsulamento](https://blog.caelum.com.br/tdd-e-sua-influencia-no-acoplamento-e-coesao/) é problema diário de qualquer desenvolvedor. Você pode até não perceber de imediato, mas o preço da [manutenibilidade chega logo](http://martinfowler.com/bliki/DesignPayoffLine.html) (_Design payoff_). Por fim, há várias [outras features do CDI](http://www.next-presso.com/2014/06/you-think-you-know-everything-about-cdi-events-think-again/) que você deveria ter no seu leque de opções e talvez no futuro, a invocação de [métodos assíncronos](https://blogs.oracle.com/theaquarium/entry/asynchronous_cdi_events) faça parte da [especificação](http://www.next-presso.com/2014/03/forward-cdi-2-0/). Se bem que o [JavaEE 8](http://blog.arungupta.me/javaee8/) não está tão perto assim de sair, no momento passa por [uma crise interna](https://dzone.com/articles/java-ee-8-in-crisis).
