---
title: "Novidades do EJB 3.1 do futuro Java EE 6"
date: "2008-03-25"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

A [especificação EJB 3.0](http://jcp.org/en/jsr/detail?id=220) já está no mercado há quase 2 anos e simplificou bastante o desenvolvimento. O uso de anotações (XML opcional) e POJOs/POJIs são as características principais. EJB 3.0 faz parte da [Java EE 5](http://java.sun.com/javaee/technologies/javaee5.jsp).

Ainda esse ano deve sair o [Java EE 6](http://jcp.org/en/jsr/detail?id=313) e, com ele, também uma atualização da especificação do EJB. Vamos ver quais são as novidades/melhorias propostas atualmente no **rascunho** do [EJB 3.1](http://jcp.org/aboutJava/communityprocess/edr/jsr318/):

**Facilidades no desenvolvimento**

1) Interfaces locais são opcionais.

Um session bean local pode ficar simples assim:

\[java\] @Stateless public class PedidoDAO {

public void cadastra(Pedido pedido) { //código para cadastrar um pedido } } \[/java\]

Não precisa mais da interface e da anotação _@Local_.

2) EJB's dentro do war

Será possivel colocar um EJB3 num war, facilitando o packaging. Não é preciso criar um jar e ear separado, tudo pode ser dentro de um unico war. As classes ficam na pasta _WEB-INF/classes_, e se for usado um _ejb-jar.xml_, se encontra na pasta _WEB-INF_.

**Novidades**

1) Singleton Beans

A ideia é que você pode criar session beans que só existem uma única vez na sua aplicação. Assim é possível criar objetos como o _ServletContext_ na aplicação web. Por exemplo:

\[java\] @Singleton @ReadOnly public class ApplicationContextBean implements ApplicationContext {

@PostConstruct public void init() { //initializa o contexto da aplicação }

//métodos, por exemplo getAttribute(String chave) } \[/java\]

Repare a anotação _@ReadOnly_, declarando o EJB imutável. Também vai existir uma anotação _@ReadWrite_ para singletons beans que podem ser alterados.

O método _init()_ será executado quando o container inicializa a aplicação. Singleton components também serão EJBs e podem ser injetados, o que é uma grande vantagem em relação a ter de ficar preso ao método estático de lookup de um singleton, e uma boa prática de inversão de controle.

2) EJB Timer

Terá uma nova anotação _@Schedule_ referente ao EJB timer para facilitar o agendamento. Por exemplo:

\[java\] //... @Schedule(hour=”13”, dayOfMonth=”1”) public void geraRelatorio() {/\* .... \*/} //... \[/java\]

executa cada primeiro dia no mes as 13 horas. É um modelo bem superior aos atuais timers do EJB, porém ainda não tão completo quanto aos frameworks open source existentes, como o [Quartz](http://www.opensymphony.com/quartz/).

3) Chamadas assíncronas

Parecidos com Web Services, métodos num session beans também podem ser chamados assincronamente. Para isto serve a anotação _@Asynchronous_:

\[java\] @Stateless @Remote(PedidoFacade.class) public class PedidoFacadeBean implements PedidoFacade{

@Asynchronous public void cadastra(Pedido pedido) { //código para cadastrar um pedido }

@Asynchronous public Future autoriza(Pedido pedido) { //autoriza demora ..... } } \[/java\]

O segundo método devolve um object do tipo `java.util.concurrent.Future` que fornece métodos para verificar se o resultado já chegou.

O lançamento da versão 3.1, que teoricamente ocorrerá até o fim deste ano, deve acelerar ainda mais a adoção da plataforma Java EE 5/6 em relação ao uso das versões antigas J2EE.
