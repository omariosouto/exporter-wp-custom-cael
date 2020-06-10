---
title: "Conheça a nova API de datas do Java 8"
date: "2014-04-08"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Manipular datas no Java sempre foi algo trabalhoso. No Java 1.0 havia apenas a classe `Date`, que era complicada de usar e não funcionava bem com internacionalização. Com o lançamento do Java 1.1, surgiu a classe abstrata `Calendar`, com muito mais recursos, porém com mutabilidade e decisões de design questionáveis.

A partir do Java 8, que foi [lançado recentemente](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/), há uma nova API de datas disponível no pacote [java.time](http://docs.oracle.com/javase/8/docs/api/java/time/package-summary.html). Essa API é uma excelente adição às bibliotecas padrão do Java e já vinha sendo desenvolvida [desde 2007](https://blog.caelum.com.br/jsr-310-date-and-time-api/).

Essa nova API foi baseada na famosa biblioteca [JodaTime](http://www.joda.org/joda-time/), que era a salvação para lidar com datas até então. Já falamos sobre [como usar o JodaTime](https://blog.caelum.com.br/o-eterno-problema-de-calcular-a-diferenca-de-dias-entre-duas-datas-em-java/) para resolver problemas difíceis. A nova API não é exatamente igual ao JodaTime, já que vários [detalhes conceituais e de implementação](http://blog.joda.org/2009/11/why-jsr-310-isn-joda-time_4941.html) foram melhorados.

Um dos principais conceitos dessa nova API é a separação de como _dados temporais_ são interpretados em duas categorias: a dos computadores e a dos humanos.

### Datas para computadores

Para um computador, o tempo é um número que cresce a cada instante. No Java, historicamente era utilizado um long que representava os milissegundos desde 01/01/1970 às 00:00:00. Na nova API, a classe `Instant` é utilizada para representar esse número, agora com precisão de nanossegundos.

\[code language="java"\] Instant agora = Instant.now(); System.out.println(agora); //2014-04-08T10:02:52.036Z (formato ISO-8601) \[/code\]

Podemos usar um `Instant`, por exemplo, para medir o tempo de execução de um algoritmo.

\[code language="java"\] Instant inicio = Instant.now(); rodaAlgoritmo(); Instant fim = Instant.now();

Duration duracao = Duration.between(inicio, fim); long duracaoEmMilissegundos = duracao.toMillis(); \[/code\]

Observe que utilizamos a classe `Duration`. Essa classe serve para medir uma quantidade de tempo em termos de nanossegundos. Você pode obter essa quantidade de tempo em diversas unidades chamando métodos como `toNanos`, `toMillis`, `getSeconds`, etc.

### Datas para humanos

Já para um humano, há uma divisão do tempo em anos, meses, dias, semanas, horas, minutos, segundos e por aí vai. Temos ainda fusos horários, horário de verão e diferentes calendários.

Várias questões surgem ao considerarmos a interpretação humana do tempo. Por exemplo, no calendário judaico, um ano pode ter 13 meses. As classes do pacote `java.time` permitem que essas interpretações do tempo sejam definidas e manipuladas de forma precisa, ao contrário do que acontecia ao usarmos `Date` ou `Calendar`.

Temos, por exemplo, a classe `LocalDate` que representa uma data, ou seja, um período de 24 horas com dia, mês e ano definidos.

\[code language="java"\] LocalDate hoje = LocalDate.now(); System.out.println(hoje); //2014-04-08 (formato ISO-8601) \[/code\]

Um `LocalDate` serve para representarmos, por exemplo, a data de emissão do nosso RG, em que não nos importa as horas ou minutos, mas o dia todo. Podemos criar um `LocalDate` para uma data específica utilizando o método `of`:

\[code language="java"\] LocalDate emissaoRG = LocalDate.of(2000, 1, 15); \[/code\]

Note que utilizamos o valor 1 para representar o mês de Janeiro. Poderíamos ter utilizado o enum `Month` com o valor _JANUARY_. Há ainda o enum `DayOfWeek`, que representa os dias da semana.

Para calcularmos a duração entre dois `LocalDate`, devemos utilizar um `Period`, que já trata anos bissextos e outros detalhes. \[code language="java"\] LocalDate homemNoEspaco = LocalDate.of(1961, Month.APRIL, 12); LocalDate homemNaLua = LocalDate.of(1969, Month.MAY, 25);

Period periodo = Period.between(homemNoEspaco, homemNaLua);

System.out.printf("%s anos, %s mês e %s dias", periodo.getYears() , periodo.getMonths(), periodo.getDays()); //8 anos, 1 mês e 13 dias \[/code\]

Já a classe `LocalTime` serve para representar apenas um horário, sem data específica. Podemos, por exemplo, usá-la para representar o horário de entrada no trabalho.

\[code language="java"\] LocalTime horarioDeEntrada = LocalTime.of(9, 0); System.out.println(horarioDeEntrada); //09:00 \[/code\]

A classe `LocalDateTime` serve para representar uma data e hora específicas. Podemos representar uma data e hora de uma prova importante ou de uma audiência em um tribunal.

\[code language="java"\] LocalDateTime agora = LocalDateTime.now(); LocalDateTime aberturaDaCopa = LocalDateTime.of(2014, Month.JUNE, 12, 17, 0); System.out.println(aberturaDaCopa); //2014-06-12T17:00 (formato ISO-8601) \[/code\]

### Datas com fuso horário

Para representarmos uma data e hora em um fuso horário específico, devemos utilizar a classe `ZonedDateTime`. \[code language="java"\] ZoneId fusoHorarioDeSaoPaulo = ZoneId.of("America/Sao\_Paulo"); ZonedDateTime agoraEmSaoPaulo = ZonedDateTime.now(fusoHorarioDeSaoPaulo); System.out.println(agoraEmSaoPaulo); //2014-04-08T10:02:57.838-03:00\[America/Sao\_Paulo\] \[/code\]

Com um `ZonedDateTime`, podemos representar, por exemplo, a data de um voo. \[code language="java"\] ZoneId fusoHorarioDeSaoPaulo = ZoneId.of("America/Sao\_Paulo"); ZoneId fusoHorarioDeNovaYork = ZoneId.of("America/New\_York");

LocalDateTime saidaDeSaoPauloSemFusoHorario = LocalDateTime.of(2014, Month.APRIL, 4, 22, 30); LocalDateTime chegadaEmNovaYorkSemFusoHorario = LocalDateTime.of(2014, Month.APRIL, 5, 7, 10); ZonedDateTime saidaDeSaoPauloComFusoHorario = ZonedDateTime.of(saidaDeSaoPauloSemFusoHorario, fusoHorarioDeSaoPaulo); System.out.println(saidaDeSaoPauloComFusoHorario); //2014-04-04T22:30-03:00\[America/Sao\_Paulo\]

ZonedDateTime chegadaEmNovaYorkComFusoHorario = ZonedDateTime.of(chegadaEmNovaYorkSemFusoHorario, fusoHorarioDeNovaYork); System.out.println(chegadaEmNovaYorkComFusoHorario); //2014-04-05T07:10-04:00\[America/New\_York\] Duration duracaoDoVoo = Duration.between(saidaDeSaoPauloComFusoHorario, chegadaEmNovaYorkComFusoHorario); System.out.println(duracaoDoVoo); //PT9H40M \[/code\]

Se calcularmos de maneira ingênua a duração do voo, teríamos 8:40. Porém, como há uma diferença entre os fusos horários de São Paulo e Nova York, a duração correta é 9:40. Repare que a API já faz o tratamento de fusos horários distintos.

Outro cuidado importante que devemos ter é em relação ao horário de verão. No fim do horário de verão, por exemplo, a mesma hora existe duas vezes!

\[code language="java"\] ZoneId fusoHorarioDeSaoPaulo = ZoneId.of("America/Sao\_Paulo");

LocalDateTime fimDoHorarioDeVerao2013SemFusoHorario = LocalDateTime.of(2014, Month.FEBRUARY, 15, 23, 00);

ZonedDateTime fimDoHorarioVerao2013ComFusoHorario = fimDoHorarioDeVerao2013SemFusoHorario.atZone(fusoHorarioDeSaoPaulo); System.out.println(fimDoHorarioVerao2013ComFusoHorario); //2014-02-15T23:00-02:00\[America/Sao\_Paulo\]

ZonedDateTime maisUmaHora = fimDoHorarioVerao2013ComFusoHorario.plusHours(1); System.out.println(maisUmaHora); //2014-02-15T23:00-03:00\[America/Sao\_Paulo\] \[/code\]

Repare no código anterior que, mesmo aumentando uma hora, o horário continuou 23:00. Entretanto, observe que o fuso horário foi alterado de -02:00 para -03:00.

### Datas e meses importantes

Existem também as classes `MonthDay`, que deve ser utilizada para representar datas importantes que se repetem todos os anos, e `YearMonth`, que deve ser utilizada para representar um mês inteiro de um ano específico. \[code language="java"\] MonthDay natal = MonthDay.of(Month.DECEMBER, 25); YearMonth copaDoMundo2014 = YearMonth.of(2014, Month.JUNE); \[/code\]

### Formatando datas

O `toString` padrão das classes da API utiliza o formato ISO-8601. Se quisermos definir o formato de apresentação da data, devemos utilizar o método `format`, passando um `DateTimeFormatter`.

\[code language="java"\] LocalDate hoje = LocalDate.now(); DateTimeFormatter formatador = DateTimeFormatter.ofPattern("dd/MM/yyyy"); hoje.format(formatador); //08/04/2014 \[/code\]

O enum `FormatStyle` possui alguns formatos pré-definidos, que podem ser combinados com um `Locale`.

\[code language="java"\] LocalDateTime agora = LocalDateTime.now(); DateTimeFormatter formatador = DateTimeFormatter .ofLocalizedDateTime(FormatStyle.SHORT) .withLocale(new Locale("pt", "br")); agora.format(formatador); //08/04/14 10:02 \[/code\]

### Manipulando datas

Todas as classes mencionadas possuem diversos métodos que permitem manipular as medidas de tempo. Por exemplo, podemos usar o método `plusDays` da classe `LocalDate` para aumentarmos um dia:

\[code language="java"\] LocalDate hoje = LocalDate.now(); LocalDate amanha = hoje.plusDays(1); \[/code\]

Outro cálculo interessante é o número de medidas de tempo até uma determinada data, que podemos fazer através do método `until`. Para descobrir o número de dias até uma data, por exemplo, devemos passar `ChronoUnit.DAYS` como parâmetro. \[code language="java"\] MonthDay natal = MonthDay.of(Month.DECEMBER, 25); LocalDate natalDesseAno = natal.atYear(Year.now().getValue()); long diasAteONatal = LocalDate.now() .until(natalDesseAno, ChronoUnit.DAYS); \[/code\]

Podemos utilizar a interface `TemporalAdjuster` para definir diferentes maneiras de manipular as medidas de tempo. É interessante notar que essa é uma interface funcional, permitindo o uso de lambdas.

A classe auxiliar `TemporalAdjusters` já possui diversos métodos que agem como _factories_ para diferentes implementações úteis de `TemporalAdjuster`. Podemos, por exemplo, descobrir qual é a próxima sexta-feira. \[code language="java"\] TemporalAdjuster ajustadorParaProximaSexta = TemporalAdjusters.next(DayOfWeek.FRIDAY); LocalDate proximaSexta = LocalDate.now().with(ajustadorParaProximaSexta); \[/code\]

### Imutabilidade e Testabilidade

Se você adicionar um dia a um `LocalDate`, as informações de data não serão alteradas. \[code language="java"\] LocalDate hoje = LocalDate.now(); //2014-04-08 hoje.plusDays(1); System.out.println(hoje); //2014-04-08 (ainda é hoje, e não amanhã!) \[/code\]

Na verdade, qualquer método que alteraria o objeto retorna uma referência a um novo objeto com as informações alteradas. \[code language="java"\] LocalDate hoje = LocalDate.now(); LocalDate amanha = hoje.plusDays(1); boolean mesmoObjeto = hoje == amanha; //false, já que é imutável \[/code\]

Isso vale para todas as classes do pacote `java.time`, que são imutáveis e, por isso, são thread-safe e mais fáceis de dar manutenção.

Um outro ponto importante da API é a [melhor testabilidade](https://gist.github.com/alexandreaquiles/9956165) com o uso da classe `Clock`.

### Trabalhando com código legado

Não poderemos mudar todo o nosso código existente para trabalhar com o poder do pacote `java.time` de uma hora pra outra. Por isso, o Java 8 trouxe alguns pontos de interoperabilidade entre os antigos `Date` e `Calendar` e a nova API. \[code language="java"\] Calendar calendar = Calendar.getInstance(); Instant instantAPartirDoCalendar = calendar.toInstant(); Date dateAPartirDoInstant = Date.from(instantAPartirDoCalendar); Instant instantAPartirDaDate = dateAPartirDoInstant.toInstant(); \[/code\]

Além disso, classe abstrata `Calendar` ganhou um _builder_, que possibilita a criação de uma instância de maneira fluente. \[code language="java"\] Calendar calendario = new Calendar.Builder() .setDate(2014, Calendar.APRIL, 8) .setTimeOfDay(10, 2, 57) .setTimeZone(TimeZone.getTimeZone("America/Sao\_Paulo")) .setLocale(new Locale("pt", "br")) .build(); \[/code\]

A nova API de datas do Java 8 é bastante extensa, possuindo diversos outros recursos interessantes. Com certeza, é uma adição muito bem-vinda ao Java, que vai facilitar bastante o trabalho de manipulação de datas.
