---
title: "Padrões de Integração com Apache Camel"
date: "2014-01-28"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Hoje em dia é difícil encontrar uma aplicação que funciona de maneira isolada, sem depender de nenhuma outra funcionalidade externa. Queremos reaproveitar funcionalidades já existentes que é a ideia principal do SOA. Ou seja, em tempos de Cloud e SOA a [integração entre aplicações](http://www.eaipatterns.com/Chapter1.html) se tornou algo muito comum e faz parte do dia-a-dia do desenvolvedor.

O problema é que integração é uma tarefa árdua! Fazer que aplicações se comuniquem de [maneira robusta e desacoplada](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-de-webservices-altamente-eficazes/) não é fácil, pois não criamos as [aplicações pensando na integração](https://blog.caelum.com.br/diminuindo-acoplamento-de-sistemas-com-rest-e-video/). Além disso, temos que trabalhar com plataformas, linguagens, formatos e protocolos diferentes, manter versões diversas, lidar com problemas da rede e falhas em geral, entre outras preocupações.

[![Logo Apache Camel](https://blog.caelum.com.br/wp-content/uploads/2014/01/camel-logo.png)](https://blog.caelum.com.br/wp-content/uploads/2014/01/camel-logo.png)

Um **framework de integração** ajuda a diminuir a complexidade e o impacto dessas integrações. Ao invés de escrever código de integração na mão usamos componentes para isso. Com um framework de integração seguimos boas práticas que foram identificadas e amadurecidas ao longo do tempo.

Os **[Enterprise Integration Patterns](http://www.eaipatterns.com/)** definem uma [série de boas práticas](http://www.eaipatterns.com/Chapter1.html) na integração que foram documentadas no [livro com o mesmo nome](http://www.amazon.com.br/Enterprise-Integration-Patterns-Designing-Addison-Wesley-ebook/dp/B007MQLL4E/ref=sr_1_1?ie=UTF8&qid=1390178112&sr=8-1&keywords=enterprise+integration+patterns). O livro descreve as vantagens e desvantagens de cada padrão e define um vocabulário comum a ser seguido. O [Apache Camel](http://camel.apache.org/), como [framework de integração](http://java.dzone.com/articles/open-source-integration-apache), implementa a maioria dos [padrões de integração](camel.apache.org/enterprise-integration-patterns.html).

Essencialmente, Camel é um roteador (_routing engine_) e a tarefa do desenvolvedor é configurar, através de um _Builder_, as regras de roteamento. O desenvolvedor decide de onde vem as mensagens (`from()`), para onde enviar (`to()`) e o que fazer com a mensagem no meio desse processo (_mediation engine_).

Veja um exemplo de uma rota com Apache Camel configurada pela [Camel DSL](http://camel.apache.org/dsl.html) em Java: \[code language="java"\] from("jpa://br.com.caelum.livraria.modelo.Pedido?consumer.delay=5s") .marshal().xstream() .log(LoggingLevel.INFO, "XML do pedido: ${body}") .to("jms:queue:pedido") .marshal().xmljson() .log(LoggingLevel.INFO, "JSON do pedido: ${body}") .to("http4://localhost:8080/pedidos") .end(); \[/code\]

Nesse exemplo verificamos a cada 5 segundos se há um novo pedido no [banco de dados](http://www.eaipatterns.com/SharedDataBaseIntegration.html). Caso exista, serializamos para XML e [encaminhamos para uma fila JMS](http://camel.apache.org/jms.html). Depois transformamos o [XML para JSON](http://camel.apache.org/data-format.html) para [enviar uma requisição HTTP](http://camel.apache.org/http4.html). A rota deve ser configurada pelo `RouteBuilder` que, por sua vez, é adicionado ao `CamelContext`:

\[code language="java"\] CamelContext context = new DefaultCamelContext(); context.addRoutes(new RouteBuilder() { //configuração da rota from(..).to(..) });

context.start(); \[/code\]

Pelos métodos `from(..)` e `to(..)` definimos os _endpoint_, ou seja de onde vem e para onde vai a mensagem (_routing_). No meio dos _endpoints_ podemos aplicar regras (_mediation_) de [transformação](http://camel.apache.org/message-translator.html) ou [enriquecer](http://camel.apache.org/content-enricher.html), serializar para [vários formatos](http://camel.apache.org/data-format.html), [validar](http://camel.apache.org/bean-validation.html) e [processar](http://camel.apache.org/processor.html) os dados e muito mais, sempre seguindo os Enterprise Application Patterns. A [lista de componentes](http://camel.apache.org/components.html) é impressionante (+120), mas não estamos limitados aos componentes oficiais, pois é relativamente fácil [criar o seu próprio componente](http://java.dzone.com/articles/creating-custom-camel).

Nesse momento poderia parecer que o [Camel é um ESB](http://camel.apache.org/is-camel-an-esb.html) ([Enterprise Service Bus](http://www.mulesoft.org/what-esb)) e, realmente, algumas das funções de um ESB tradicional o Apache Camel também possui. O próprio site do Camel define o framework como "_a small, lightweight embeddable ESB_".

A explicação do site também deixa clara a diferença: O Apache Camel roda embutido dentro da aplicação. Um ESB é um container/servidor separado, um produto [mais complexo com várias funcionalidades](http://www.mulesoft.com/de/resources/esb/mule-vs-camel-comparison). Além do roteamento e mediação já vistos, um ESB adiciona outros recursos como monitoramento e gerenciamento de processos, balanceamento de carga e alta disponibilidade, oferece um registro de serviços, força segurança e disponibiliza ferramentas (_tooling_), entre outras funções. Isso também faz com que um ESB seja um produto mais complexo, enquanto o Apache Camel foca no roteamento e mediação.

Mais sobre a integração com Apache Camel é visto no nosso treinamento **[SOA na prática: Integração com Web Services e Mensageria](http://www.caelum.com.br/curso-java-ee-soa-web-services-mensageria/)**.

E você, já usou Apache Camel?
