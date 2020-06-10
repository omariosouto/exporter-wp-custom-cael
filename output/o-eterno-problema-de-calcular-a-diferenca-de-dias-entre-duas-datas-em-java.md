---
title: "O eterno problema de calcular a diferença de dias entre duas datas em Java"
date: "2012-10-17"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Seu chefe te dá a seguinte missão: calcular a diferença de dias entre duas datas. Usando API do `java.util.Date` ou `java.util.Calendar` você até consegue dar um jeito. Um mês depois ele pede a você que calcule novamente essa diferença, mas desprezando sábados e domingos. E agora? O que fazer? E tem mais, um tempo depois ele pede que despreze também os feriados, dias abonados, e dias que não teve expediente (greve, recesso, comemorações), considerando assim apenas os dias úteis. É um problema relativamente corriqueiro, a questão é como solucioná-lo sem fazer malabarismos com as classes do `java.util`, que deixam bastante a desejar.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/10/Watch_Wallpaper_1024x768_wallpaperhere-300x225.jpg "Watch_Wallpaper_1024x768_wallpaperhere")](https://blog.caelum.com.br/wp-content/uploads/2012/10/Watch_Wallpaper_1024x768_wallpaperhere.jpg)

Uma solução prática para este problema é o uso do [JodaTime](http://joda-time.sourceforge.net/index.html), projeto criado justamente para facilitar a manipulação de datas em Java. A biblioteca voltou a figurar nas notícias pois foi [anunciado recentemente](http://www.infoq.com/news/2012/09/jsr310-java8) que a API de Data será reformulada para o Java 8, sob a especificação [JSR 310](http://jcp.org/en/jsr/detail?id=310), fortemente inspirada pelo JodaTime, já que um dos spec leaders é o próprio criador.

A especificação já existe há um bom tempo, inclusive conta com a participação do [Fábio Kung](http://twitter.com/fabiokung), que [já explicou em 2007 como seria a proposta](https://blog.caelum.com.br/jsr-310-date-and-time-api/). Porém, enquanto o Java 8 não chega, para resolvemos o problema, além do JodaTime.

Inicialmente, para obter uma data, basta instanciar um `[DateTime](http://joda-time.sourceforge.net/api-release/org/joda/time/DateTime.html)`, que possui uma API bem completa e tem a vantagem de ser imutável. Ela representa um [instante](http://joda-time.sourceforge.net/key_instant.html) no tempo.

\[sourcecode language="java"\] DateTime dataInicial = new DateTime(2012, 12, 1, 12, 0); DateTime dataFinal = new DateTime(2012, 12, 28, 12, 30); DateTime feriado = new DateTime(2012, 12, 25, 12, 0); \[/sourcecode\]

Essa classe funciona muito bem com a API do `java.util`, possuindo métodos `toDate` e `toCalendar`, além de um construtor que pode receber `Date` e `Calendar`.

Para calcular quantos dias existem entre uma data e outra, é muito simples:

\[sourcecode language="java"\] int dias = Days.daysBetween(dataInicial, dataFinal).getDays(); \[/sourcecode\]

[Days.daysBetween](http://joda-time.sourceforge.net/apidocs/org/joda/time/Days.html#daysBetween(org.joda.time.ReadableInstant, org.joda.time.ReadableInstant)) devolve um objeto do tipo `Days`, que implementa a importante interface [Period](http://joda-time.sourceforge.net/key_period.html).

Vamos começar a complicar. Podemos calcular, de maneira análoga, a diferência de horas usando a classe `Hours`.

\[sourcecode language="java"\] System.out.println( Hours.hoursBetween(dataInicial, dataFinal).getHours()); \[/sourcecode\]

Aqui desprezamos os minutos, retornando apenas a quantidade de horas como um valor inteiro. Podemos usar a classe [Minutes](http://joda-time.sourceforge.net/api-release/org/joda/time/Minutes.html) com [BigDecimal](https://blog.caelum.com.br/arredondamento-no-java-do-double-ao-bigdecimal/).

\[sourcecode language="java"\] BigDecimal minutos = new BigDecimal(Minutes.minutesBetween(dataInicial, dataFinal).getMinutes()); BigDecimal horas = minutos.divide(new BigDecimal("60"), 2, RoundingMode.HALF\_UP); System.out.println(horas); // 648,50h \[/sourcecode\]

Caso a gente vá calcular a diferença das datas em diversas unidades de tempo (horas, dias, minutos, etc), podemos utilizar a classe [Duration](http://joda-time.sourceforge.net/api-release/org/joda/time/Duration.html), que tem um construtor que recebe dois instants (como `DateTime`) e depois permite recuperar a diferença na unidade temporal que você desejar. Agora temos que descontar os feriados e finais de semana do período. Utilizaremos outras duas bibliotecas, [que são relacionadas ao projeto JodaTime](http://joda-time.sourceforge.net/related.html), [ObjectLab Holiday Calculation](http://objectlabkit.sourceforge.net/) e [Jollyday](http://jollyday.sourceforge.net/).

Primeiro passo é descobrir quais são os feriados no Brasil para o ano corrente. Vamos usar o Jollyday, que já vem com algum feriados e você pode [customizar outros em um arquivo separado](http://jollyday.sourceforge.net/data.html).

\[sourcecode language="java"\] HolidayManager gerenciadorDeFeriados = HolidayManager.getInstance(de.jollyday.HolidayCalendar.BRAZIL); Set<Holiday> feriados = gerenciadorDeFeriados.getHolidays(new DateTime().getYear()); Set<LocalDate> dataDosFeriados = new HashSet<LocalDate>(); for (Holiday h : feriados) { dataDosFeriados.add(new LocalDate(h.getDate(), ISOChronology.getInstance())); } \[/sourcecode\]

Segundo passo é dado a lista de feriados `dataDosFeriados`, vamos utilizar o calendário do [ObjectLab Holiday Calculation](http://objectlabkit.sourceforge.net/) para descobrir se uma determinada data é dia útil, baseado tanto nos feriados quanto nos fins de semana. Fazemos isso através do método `isNonWorkingDay`

\[sourcecode language="java"\] // popula com os feriados brasileiros HolidayCalendar<LocalDate> calendarioDeFeriados = new DefaultHolidayCalendar<LocalDate>(dataDosFeriados);

LocalDateKitCalculatorsFactory.getDefaultInstance() .registerHolidays("BR", calendarioDeFeriados);

DateCalculator<LocalDate> calendario = LocalDateKitCalculatorsFactory.getDefaultInstance(). getDateCalculator("BR", HolidayHandlerType.FORWARD);

// true - sábado calendario.isNonWorkingDay(new LocalDate(dataInicial));

// false calendario.isNonWorkingDay(new LocalDate(dataFinal));

// true - natal calendario.isNonWorkingDay(new LocalDate(feriado)); \[/sourcecode\]

Por fim, agora temos que descobrir quantos dias não úteis existem no período e descontar das horas já calculadas. Vamos caminhando de data em data e perguntando se ela é ou não dia útil. Se não for, incrementamos `diasNaoUteis`:

\[sourcecode language="java"\] int diasNaoUteis = 0;

LocalDate dataInicialTemporaria = new LocalDate(dataInicial); LocalDate dataFinalTemporaria = new LocalDate(dataFinal);

while (!dataInicialTemporaria.isAfter(dataFinalTemporaria)) { if (calendario.isNonWorkingDay(dataInicialTemporaria)) { diasNaoUteis++; } dataInicialTemporaria = dataInicialTemporaria.plusDays(1); } \[/sourcecode\]

Se quisermos calcular o número de horas úteis entre essas datas, basta subtrair o total de horas que já calculamos de `24*diasNaoUteis`:

\[sourcecode language="java"\] System.out.println("Horas finais líquidas: " + horas.subtract(new BigDecimal(24 \* diasNaoUteis))); \[/sourcecode\]

O código pode ser um pouco mais eficiente. Há uma outra solução para não precisar fazer um loop que passe por todas as datas do nosso intervalo. Podemos chamar `HolidayManager.getHolidays()` passando o intervalo das nossas datas, recebendo um `Set`. Invocando o `size` desse conjunto, temos o número de feriados do intervalo. Também calculamos quantos fins de semana tivemos, utilizando `Weeks.weeksIn` no nosso intervalo. Por último, precisamos tomar cuidado e verificar quais feriados cairam em fins de semana, para não contá-los duas vezes.

E você, como costuma resolver estes problemas? Vale lembrar que [JodaTime possui integração com Hibernate](http://joda-time.sourceforge.net/contrib/hibernate/index.html) e pode ser facilmente configurada no seu projeto. Também é fundamental pensar nos casos particulares quando escrever seus testes de unidade. É muito fácil você cair em situações estranhas, em especial se sua data possui horas e minutos, além de anos bissextos, feriados que caem em fins de semana, etc.
