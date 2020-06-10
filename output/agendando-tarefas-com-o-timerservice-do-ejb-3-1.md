---
title: "Agendando tarefas com o TimerService do EJB 3.1"
date: "2011-10-31"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

A [versão 3](http://jcp.org/en/jsr/detail?id=220 "Especificação EJB3") dos [Enterprise Java Beans](http://en.wikipedia.org/wiki/Enterprise_JavaBean "Wikipedia EJB") trouxe [grandes mudanças e muitas simplificações](http://www.caelum.com.br/curso/fj-31-java-ee-web-services/ "Treinamento FJ-31 na Caelum") para o desenvolvedor. O forte uso de anotações e [convenções](http://en.wikipedia.org/wiki/Convention_over_configuration "Wikipedia Convention over configuration"), que deixaram os XMLs complexos opcionais, entrou no [JPA](https://blog.caelum.com.br/screencast-primeiros-passos-para-a-jpa/ "Screencast JPA da Caelum") como forma [padrão de persistência](http://jcp.org/aboutJava/communityprocess/final/jsr317/index.html "Especificação JPA ") para substituir os burocráticos entity beans e a [injeção de dependências](https://blog.caelum.com.br/customizando-a-producao-de-dependencias-no-cdi/ "Link CDI no blog da Caelum") melhora o [design para não depender de lookups acoplados](https://blog.caelum.com.br/tdd-e-sua-influencia-no-acoplamento-e-coesao/ "Artigo sobre TDD no blog da Caelum").

[![](https://blog.caelum.com.br/wp-content/uploads/2011/10/timer-300x300.png "timer")](https://blog.caelum.com.br/wp-content/uploads/2011/10/timer.png) O [EJB 3.1](http://jcp.org/aboutJava/communityprocess/pr/jsr318/index.html "Especificação EJB 3.1") foi um passo na mesma direção. Nessa versão as interfaces locais ficaram opcionais, os EJBs podem ser utilizados [dentro de um WAR](http://stackoverflow.com/questions/4441237/packaging-ejb-in-javaee-6-war-vs-ear "Comparação dos formatos EAR e WAR"), o que simplifica o empacotamento (EAR não é mais preciso), e [várias outras novidades](https://blog.caelum.com.br/novidades-do-ejb-31-do-futuro-java-ee-6/ "Artigo sobre JavaEE 6 no blog da Caelum").

Uma das melhorias do EJB 3.1 está relacionada com o agendamento de tarefas dentro do servidor de aplicação, o que é o foco desse post. Mas, para ser exato, o agendamento já era possível nas versões anteriores do EJB (entrou na versão 2.1 da especificação), mas foi muito aperfeiçoado no EJB 3.1.

**Como funcionava com EJB 3.0/2.1**

Primeiro era preciso definir o método que era chamado quando o [@Timeout](http://java.sun.com/javaee/5/docs/api/javax/ejb/Timeout.html "Explicação breve onde usar @Timeout") de um agendamento ocorria. Para isso podemos usar um Session Bean Stateless:

\[java\] @Stateless @Remote(Agendador.class) //pode ser @Local também public class AgendadorBean implements Agendador {

@Timeout // no EJB 2.1 implementava a interface javax.ejb.TimedObject public void timeout(Timer timer) { System.out.println("Timeout: " + timer.getInfo()); } \[/java\]

Para agendar a execução desse método, usamos o [`TimerService`](http://download.oracle.com/javaee/5/tutorial/doc/bnboy.html "Link para o tutorial do JavaEE 6 sobre o TimerService"). Com ele podemos definir o agendamento e executar um método anotado com `@Timeout` apenas uma vez, ou em intervalos ([single-action ou interval-action](http://www.javabeat.net/articles/3-ejb-30-timer-services-an-overview-1.html)). O `TimerService` pode ser injetado, com EJB3, da seguinte maneira:

\[java\] @Stateless @Remote(Agendador.class) public class AgendadorBean implements Agendador {

@Resource //no EJB 2.1 era preciso usar ejbContext.getTimerService() private TimerService timerService;

public void agenda() { //definir o agendamento - daqui 10s, cada 20s this.timerService.createTimer(10\*1000L, 20\*1000L, "alguma info"); } } \[/java\]

O método `createTimer(..)` é [sobrecarregado](http://java.sun.com/javaee/5/docs/api/javax/ejb/TimerService.html "Javadoc TimerService") e possui variações, mas não tem como fazer mais do que definir _single-action-timer_ ou _interval-action_.

**O que melhorou com EJB 3.1**

No EJB 3.1 o [TimerService](http://download.oracle.com/javaee/6/api/javax/ejb/TimerService.html "Tutorial do TimerService no JavaEE 6") ganhou métodos para deixar o agendamento mais preciso, baseado em expressões de calendar. Isso é bem parecido com [o que o framework Quartz permite](https://blog.caelum.com.br/agendamento-de-tarefas-em-aplicacoes-web-um-truque-com-quartz/ "link para o artigo sobre Quartz no blog da Caelum") no Java, e [as expressões cron](http://en.wikipedia.org/wiki/CRON_expression "Link wikipedia sobre cron"):

\[java\] public void agenda() { // cada segunda e quarta as 8:30 ScheduleExpression expression = new ScheduleExpression(); expression.dayOfWeek("Mon,Wed"); expression.hour("8"); expression.minute("30"); this.timerService.createCalendarTimer(expression); System.out.println("Agendado: " + expression); } \[/java\]

Com [essas expressões](http://download.oracle.com/javaee/6/api/javax/ejb/ScheduleExpression.html "Javadoc da class ScheduleExpression") podemos definir intervalos bem precisos e a mesma definição também pode ser feita de forma declarativa, através da anotação `@Schedule`. Com ela nem é necessário usar `@Timeout`. O método anotado com `@Schedule` é invocado quando o timeout ocorre:

\[java\] //dentro do session bean @Schedule(dayOfWeek="Mon,Wed", hour="8", minute="30") void agendado() { System.out.println("agendado pela anotacao @Schedule"); } \[/java\]

O servidor de aplicação chama então o `agendado()` periodicamente. Importante saber que qualquer timer que definir um intervalo (_interval-action-timer_) é persistido e será recuperado quando o servidor reiniciar. Mas podemos deixar o agendamento não persistente também:

\[java\] //dentro do session bean @Schedule(dayOfWeek="Mon,Wed", hour="8", minute="30", persistent=false) void agendado() { System.out.println("agendado pela anotacao @Schedule"); } \[/java\]

ou ser for agendado programaticamente:

\[java\] ScheduleExpression expression = new ScheduleExpression(); //.. TimerConfig config = new TimerConfig(); config.setPersistent(false); this.timerService.createCalendarTimer(expression, config); \[/java\]

Poderíamos ainda melhorar [o exemplo usando `@Singleton`](http://openejb.apache.org/singleton-ejb.html "Descrição sobre @Singleton no site do projeto OpenEJB") e [`@Startup`](http://openejb.apache.org/3.0/singleton-example.html "Exemplo @Startup com @Singleton no site do projeto OpenEJB"), outras novidades do EJB 3.1. Isso pode ser útil se for utilizado o agendamento através do `@Schedule` não persistente. E claro, isso é apenas mais uma das facilidades que o container EJB e o [servidor de aplicação](https://blog.caelum.com.br/jboss-as-7-inovacao-nos-servidores-java-ee/ "Artigo sobre JBoss AS 7 no blog da Caelum") podem oferecer, todos vistos no treinamento [Java EE avançado e Web Services](http://www.caelum.com.br/curso/fj-31-java-ee-web-services/ "Link para o treinamento FJ-31 da Caelum").
