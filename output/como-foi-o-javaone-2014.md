---
title: "Como foi o JavaOne 2014"
date: "2014-10-13"
author: "francisco.sokol"
authorEmail: "francisco.sokol@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Entre os dias 27 de setembro e 2 de outubro aconteceu a 18ª edição do [JavaOne](https://www.oracle.com/javaone/index.html), a maior conferência de Java do mundo organizada pela Oracle. Tivemos a honra de participar do evento, apresentando o [VRaptor 4](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=4097), nosso já conhecido framework MVC e tema de diversos posts aqui no blog.

O evento começou no sábado, com o tradicional _Geek Bike Ride_ em que alguns participantes atravessaram oito milhas de bicicleta entre San Francisco e Sausalito.

[![bike-ride](https://blog.caelum.com.br/wp-content/uploads/2014/10/bike-ride.jpg)](https://blog.caelum.com.br/wp-content/uploads/2014/10/bike-ride.jpg)

As sessões técnicas do evento foram inauguradas com os keynotes no domingo. De maneira geral, todos os keynotes destacaram o lançamento do Java 8, diversos livros sobre o tema foram listados na apresentação e entre eles estava o [Java 8 Prático](http://www.casadocodigo.com.br/products/livro-java8), da Casa do Código.

[![livro-java8-no-javaone](https://blog.caelum.com.br/wp-content/uploads/2014/10/livro-java8-no-javaone.png)](https://blog.caelum.com.br/wp-content/uploads/2014/10/livro-java8-no-javaone.png)

Destaque para o keynote técnico comandado por Mark Reinhold, que iniciou a palestra destacando a evolução da plataforma Java. Brian Goetz mostrou exemplos práticos das principais features do Java 8 como os lambdas, Stream e o Optional. Além disso, os dois arquitetos da Oracle destacaram as novidades que estão por vir no Java 9 e 10, como o projeto Jigsaw e os Value Types do projeto Valhalla. A palestra já está [disponível online](http://medianetwork.oracle.com/video/player/3811045975001).

O conteúdo do evento é muito extenso, contando com mais de 500 sessões técnicas que foram distribuídas 9 trilhas. Você pode ter uma ideia do conteúdo no [site oficial do evento](https://oracleus.activeevents.com/2014/connect/search.ww?eventRef=javaone#loadSearch-event=null&searchPhrase=&searchType=session&tc=0&sortBy=&p=&i(10009)=10111). Fique atento, pois algumas palestras foram gravadas e a organização confirmou que os vídeos estarão disponíveis no [Parlays](https://parleys.com) em breve.

Houve muitas sessões básicas sobre Java 8. Notamos também que boa parte dos participantes do evento ainda não tem conhecimento e/ou nunca usaram recursos da nova versão da linguagem. Então se você já está por dentro das novas features, tenha certeza que está a frente de diversos desenvolvedores do exterior.

Se você já conhece as novidades do Java 8, sem dúvida vai gostar da palestra [Lambda Under the Hood](https://oracleus.activeevents.com/2014/connect/search.ww?eventRef=javaone#loadSearch-event=null&searchPhrase=Brian+Goetz&searchType=speaker&tc=0&sortBy=&i(10009)=10111) do Brian Goetz. Ele mostrou detalhes de implementação do lambda, os problemas que encontraram desde o início do projeto e alguns dos passos que os levaram a trabalhar com uma nova instrução para invocação de métodos, o `InvokeDynamic`.

Boa parte do conteúdo do evento focou em temas relacionados a programação funcional, destaque para a palestra [Thinking in Functional Style](https://oracleus.activeevents.com/2014/connect/search.ww?eventRef=javaone#loadSearch-event=null&searchPhrase=functional+&searchType=session&tc=0&sortBy=&p=&i(10009)=10111) do Venkat Subramaniam, que mostrou com muita clareza os benefícios desse paradigma. Outras palestras que pudemos acompanhar e que não poderíamos deixar se destacar são:

- [Reactive Streams with Rx](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=5749), de Ben Christensen do Netflix.
- [Unlocking the Magic of Monads in Java 8](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=1738), de Oleg Šelajev da ZeroTurnaround.
- [GS Collections and Java 8](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=5423&tclass=popup), de Craig Motlin e Donald Raab do Goldman Sachs.

Além das sessões relacionadas a programação funcional, tivemos a oportunidade de acompanhar algumas ótimas apresentações sobre outros temas. Mark Reinhold e Alan Bateman [apresentaram](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=5435) com mais detalhes o projeto Jigsaw com direito a demo ao vivo das novas ferramentas que estão sendo desenvolvidas para possibilitar a criação de aplicações realmente modulares em Java. Engenheiros do Google [revelaram](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=5100) alguns detalhes da próxima geração do Google App Engine, que será totalmente baseada no Docker possibilitando o fim das odiadas restrições do ambiente Java do cloud da empresa. Linda DeMichiel, da Oracle, [apresentou](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=3015) os planos para o JavaEE 8 com novidades interessantes, como suporte ao HTTP/2 na especificação de Servlet 4.0 e melhor integração entre o CDI e JMS, além das novas especificações do MVC 1.0 e o Java EE Security 1.0.

Em [nossa apresentação](https://oracleus.activeevents.com/2014/connect/sessionDetail.ww?SESSION_ID=4097) mostramos algumas das principais features do VRaptor 4 e os benefícios da adoção do CDI 1.1, como por exemplo a facilidade de integração com as demais especificações do Java EE 7. O Framework cada vez mais ganha destaque no mercado nacional [e internacional](https://blogs.oracle.com/theaquarium/entry/vraptor_4_a_cdi_based). Os slides já estão disponíveis [via slideshare](http://www.slideshare.net/rdrgturini/vraptor-javaone), não deixe de comentar.
