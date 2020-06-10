---
title: "Streams e datas para os desafios do dia a dia no Java 8"
date: "2014-06-16"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já falamos sobre algumas das novidades do Java 8, como [lambdas, streams, default methods e method references](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/) e a [nova API de data/hora](https://blog.caelum.com.br/conheca-a-nova-api-de-datas-do-java-8/).

Que tal juntar tudo isso pra resolver um probleminha bacana?

Imagine que queremos gerar uma lista com todos dias úteis de um determinado mês. Para o nosso problema, consideramos que dia útil é qualquer dia que não estiver no final de semana, ou seja, sem contar feriados.

## Gerando dias úteis da maneira clássica

Recapitulando, a API java.time tem uma classe que representa o período de um dia (`LocalDate`) e uma classe que representa um mês inteiro (`YearMonth`).

Para obter o mês de maio de 2014, faríamos: \[code language="java"\] YearMonth anoMes = YearMonth.of(2014, 5); \[/code\]

Poderíamos obter a duração desse mês: \[code language="java"\] anoMes.lengthOfMonth(); // 31 \[/code\]

Também seria possível obter um dia específico desse mês: \[code language="java"\] LocalDate data = anoMes.atDay(28); // 28/05/2014 \[/code\]

Com o dia em mãos, poderíamos descobrir o dia da semana: \[code language="java"\] data.getDayOfWeek(); // DayOfWeeek.WEDNESDAY \[/code\]

Utilizando os recursos acima, podemos gerar a lista de dias úteis do mês/ano percorrendo com um `for` do primeiro ao último dia do mês e adicionando a uma lista os dias que não são sábado nem domingo.

Juntando tudo, nossa solução iterativa ficaria assim: \[code language="java"\] int ano = 2014; int mes = 5; YearMonth anoMes = YearMonth.of(ano, mes);

List<LocalDate> listaDosDiasUteisDoMes = new ArrayList<>();

//gerando for(int dia=1; dia <= anoMes.lengthOfMonth(); dia++){ LocalDate data = anoMes.atDay(dia);

//filtrando if(!data.getDayOfWeek().equals(DayOfWeek.SATURDAY) && !data.getDayOfWeek().equals(DayOfWeek.SUNDAY)){

//coletando listaDosDiasUteisDoMes.add(data); } } \[/code\]

Observe que **geramos** os dias com o `for` e o método `atDay`.

Depois disso, **filtramos** os dias úteis com a condição de dentro do `if`.

Finalmente, **coletamos** os dias úteis em uma lista com o método `add` de `List`.

## Gerando dias úteis com lambdas e streams

Mas e se utilizarmos os novos recursos do Java 8? Como ficaria nossa solução?

Nosso primeiro passo é, dado um mês, gerar todos os dias possíveis para aquele mês.

A classe `Stream` possui um método chamado `iterate` que, dados um valor inicial e um lambda de incremento, retorna uma sequência infinita de valores, sucessivamente.

Por exemplo, para gerar uma sequência (mais especificamente, uma `Stream`) com todos os números inteiros positivos, poderíamos fazer: \[code language="java"\] Stream<Integer> inteirosPositivos = Stream.iterate(1, n -> n + 1); \[/code\]

Mas como trabalhar com essa sequência infinita? Poderíamos pegar os primeiros 10 inteiros positivos utilizando o método `limit` da classe `Stream`: \[code language="java"\] inteirosPositivos.limit(10); \[/code\]

Interessante, não? Refletindo um pouco, podemos dizer que os meses começam no dia 1º e vão sendo incrementados, limitando-se ao número de dias do mês.

Se tivermos uma variável chamada `anoMes` que contém um `YearMonth` representando um mês qualquer, poderíamos gerar todos os dias desse mês com o seguinte código: \[code language="java"\] Stream<LocalDate> todosOsDiasDoMes = Stream.iterate(anoMes.atDay(1), data -> data.plusDays(1)) .limit(anoMes.lengthOfMonth()); \[/code\]

Observe acima que o valor inicial passado para o método `iterate` foi o dia 1º do mês. Já o lambda de incremento utilizou o método `plusDays` de `LocalDate` para adicionar um dia.

Com os dias do mês na mão, podemos utilizar o método `filter` da classe `Stream`. Esse método recebe um lambda que encapsula uma condição e cria um novo `Stream` com apenas os elementos que atendem a condição.

No nosso caso, precisamos garantir que a data não é um sábado nem um domingo: \[code language="java"\] Stream<LocalDate> diasUteisDoMes = todosOsDiasDoMes.filter(data -> !data.getDayOfWeek().equals(DayOfWeek.SATURDAY) && !data.getDayOfWeek().equals(DayOfWeek.SUNDAY)); \[/code\]

Agora, temos uma `Stream` com apenas os dias úteis do mês. Precisamos coletar o resultado em uma lista. Para isso, utilizaremos o método `collect` de `Stream`, que recebe um `Collector`. Utilizaremos um coletor já existente na classe auxiliar `Collectors`. \[code language="java"\] List<LocalDate> listaDosDiasUteisDoMes = diasUteisDoMes.collect(Collectors.toList()); \[/code\]

Colocando todo o código junto e omitindo variáveis auxiliares, teríamos: \[code language="java"\] int ano = 2014; int mes = 5; YearMonth anoMes = YearMonth.of(ano, mes);

List<LocalDate> listaDosDiasUteisDoMes = Stream.iterate(anoMes.atDay(1), data -> data.plusDays(1)) .limit(anoMes.lengthOfMonth()) .filter(data -> !data.getDayOfWeek().equals(DayOfWeek.SATURDAY) && !data.getDayOfWeek().equals(DayOfWeek.SUNDAY)) .collect(Collectors.toList()); \[/code\]

Observe que os passos do nosso algortimo (gerar valores, filtrá-los e coletar os resultados) foram facilmente mapeados para métodos da classe `Stream`. Fizemos uma sequência de operações (ou _pipeline_) que foram retornando novas instâncias de `Stream`, até coletarmos os resultados, terminando nossa computação .

Poderíamos [isolar o código acima em uma classe](https://gist.github.com/alexandreaquiles/10300153) e utilizá-la em vários pontos de uma aplicação. Seria interessante expandir a solução para gerar todos os dias úteis de um ano e, quem sabe, ler os feriados de um arquivo ou WebService.

Com lambdas e streams, podemos fazer sequências de operações de maneira natural e elegante. E manipular datas com a API java.time é simples e fácil.

E você? Já está pronto para usar os novos recursos do Java 8 nos seus projetos?
