---
title: "O recurso de implicits no Scala"
date: "2012-01-13"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quem nunca reclamou de uma API não tão bem amigável? Um bom caso é o uso do `Calendar` enquanto estamos programando em Java. Para fazer uma simples soma de dia num `Calendar` acabamos com o seguinte código:

\[java\] Calendar data = Calendar.getInstance(); data.add(Calendar.DAY\_OF\_MONTH,1); \[/java\]

O que seria mais interessante, pelo menos nesse caso, seria ter algum método mais propício que fizesse a lógica de adição. Lá dentro poderia até ser implementada de uma maneira complicada, mas é para isso que serve encapsulamento. Já que trabalhar com `Calendar` é muito comum, muitos programadores acabam criando classes utilitárias justamente para encapsular este tipo de código. Abaixo segue um exemplo:

\[java\] CalendarUtil calendarWrapper = new CalendarUtil(Calendar.getInstance()); calendarWrapper.plusDays(1); \[/java\]

Existe inclusive o bem conhecido [Joda Time](http://joda-time.sourceforge.net/ "joda-time"), exatamente com esse propósito. O problema aqui é que nem sempre temos uma lib como essa para tentar minimizar problema de APIs verborrágicas e, além disso, temos que trabalhar com objetos de classes diferentes, talvez modificar assinaturas de métodos que já existem, algumas vezes impossível. Pensando nisso, a Scala tem umas de suas mais conhecidas features, para simular novos métodos em classes que não temos controle. Imagine que queremos ter um novo método no `Calendar` chamado `plusDays`. O código seria como o abaixo:

\[java\] Calendar data = Calendar.getInstance(); data.plusDays(1); \[/java\]

Só que esse método não existe. É justamente aqui que criaríamosnosso próprio Wrapper, adicionando esses novos métodos e delegando os antigos, podendo implementar um decorator. No Scala, podemos indicar ao compilador para que procure métodos não existentes em determinadas classes em outro lugar. Preste atenção no código a seguir, que define um método `convertCalendarToImprovedCalendar` que recebe um `Calendar` e devolve uma instância de classe anônima (filha de `Calendar`) com o método adicional `plusDays`:

\[sourcecode language="scala"\] object Converters { implicit def convertCalendarToImprovedCalendar(calendar:Calendar) = new { def plusDays(number:Int) = calendar.add(Calendar.DAY\_OF\_MONTH,number) } } \[/sourcecode\]

O nosso `plusDays` poderia ser implementado inclusive com o _Joda Time_ por debaixo dos panos, e os outros desenvolvedores continuariam a trabalhar com o velho conhecido `Calendar`.

A mágica aqui acontece com o uso da palavra `implicit`. Podemos agora pedir para que esse conversor seja utilizado sempre quando um método não existente em `Calendar` for invocado. Basta importarmos o `Converters`, como no exemplo abaixo:

\[sourcecode language="scala"\] import Converters.\_ class TesteCalendarComMetodosNovos { def main(args:Array\[String\]) { val data = Calendar.getInstance(); data.plusDays(1); } } \[/sourcecode\]

Repare que os métodos da classe `Converters` são importados estaticamente para o escopo de execução, a linha `import Converters._` funciona muito parecido ao import estático do Java.

Essa técnica para adicionar novos comportamentos em classes que não estão disponíveis para serem alteradas foi chamado de _[Pimp my Library](http://www.artima.com/weblogs/viewpost.jsp?thread=179766 "pimp my library")_ por Martin Odersky, num post que ele escreveu há bastante tempo. Outras linguagens possuem features similares, como as _Open Classes_ do Ruby. Uma diferença bastante grande entre os _implicits_ e as _Open Classes_ é que como o Scala é uma linguagem estaticamente tipada, a resolução das chamadas a esses métodos são todas resolvidas em tempos de compilação. Nada é adicionado em tempo de execução.

_Implicit method_ é uma das features que mais utilizamos da linguagem (e precisamos tomar cuidado para não abusar). Adicionar esses novos comportamentos, criação de DSLs e outros usos que discutiremos em novos posts são alguns dos exemplos de uso.

Para quem quiser ver um pouco mais, existe um projeto chamado **[hibernate-dsl](https://github.com/caelum/hibernate-query-dsl)** que utilizamos aqui na Caelum para conseguirmos trabalhar com a session do Hibernate de maneira mais interessante.
