---
title: "Arredondamento e números gigantes: do double ao BigDecimal"
date: "2010-07-15"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

É fácil se deparar com as limitações do `double` no Java e na maioria das outras linguagens: quando vamos trabalhar com dinheiro notamos que as contas não estão saindo exatamente como esperávamos:

\[java\] double d1 = 0.1; double d2 = 0.2; System.out.println(d1 + d2); \[/java\]

O resultado é um estranho `0.30000000000000004`, que pode acarretar em problemas graves dependendo da utilização e arrendondamento aplicado depois nesse número. O problema é que um número com `0.1` não pode ser representado em binário de maneira finita: ele vira uma dízima (no binário ficaria algo como `0.110011001100`...) diferente do número `0.25`, que pode ser representado perfeitamente (no binário `0.01`). A representação é um pouco mais complicada que isso, [a JVM segue o padrão](http://people.uncw.edu/tompkinsj/133/numbers/Reals.htm) [IEEE 754](http://grouper.ieee.org/groups/754/) para trabalhar com números de ponto flutuante.

[![](https://blog.caelum.com.br/wp-content/uploads/2010/07/numbers-270x300.jpeg "numbers")](https://blog.caelum.com.br/wp-content/uploads/2010/07/numbers.jpeg)

Como obter o esperado `0.3`? A sugestão sempre é usar o `[BigDecimal](http://download.oracle.com/docs/cd/E17409_01/javase/6/docs/api/java/math/BigDecimal.html)`. `BigDecimal` é uma classe que trabalha com números de ponto flutuante de precisão arbitrária: você pode escolher quanto de precisão você quer usar. Por padrão ele vai utilizar o que for necessário, e, diferente do `double`, ele consegue guardar números como `0.1`, pois guardará isto como sendo `1 x 10ˆ-1` (isto é, usando a base decimal em vez de binária, evitando a dízima).

\[java\] // nao use esse construtor: BigDecimal big1 = new BigDecimal(0.1); BigDecimal big2 = new BigDecimal(0.2);

System.out.println(big1.add(big2)); \[/java\]

O resultado é uma nova surpresa, um incrível `0.300000000000000016653345369377...`. O que fizemos de errado agora foi que tentar somar `0.1` e `0.2` sendo que esses dois números já estavam armazenados em memória como `double`, e, ao serem passados para o construtor do `BigDecimal`, foram transportados com imprecisão. O [próprio javadoc desse construtor](http://download.oracle.com/docs/cd/E17409_01/javase/6/docs/api/java/math/BigDecimal.html#BigDecimal(double)) diz que "_The results of this constructor can be somewhat unpredictable_". Na verdade o resultado é bem previsível pelas suas regras, mas não é o que gostaríamos.

Como resolver? Basta sempre usar o construtor que trabalha com `String`s, assim o `BigDecimal` vai internamente fazer o parsing desses números sem que eles sejam armazenados em um `double`, evitando os problemas de precisão:

\[java\] // atencao! usando String no construtor: BigDecimal big1 = new BigDecimal("0.1"); BigDecimal big2 = new BigDecimal("0.2");

System.out.println(big1.add(big2)); \[/java\]

Finalmente obtendo o resultado esperado. Há ainda importantes observações sobre o `BigDecimal`: por padrão ele não fará nenhum tipo de arredondamento, o que o obriga a lançar `java.lang.ArithmeticException` no caso de uma dízima decimal (tentar dividir 1/3 por exemplo). Nesses casos é necessário delimitar a quantidade de bits a serem usados ou escolher [o modo de arredondamento](http://download.oracle.com/docs/cd/E17409_01/javase/6/docs/api/java/math/RoundingMode.html):

\[java\] BigDecimal big1 = new BigDecimal("1"); BigDecimal big2 = new BigDecimal("3");

System.out.println(big1.divide(big2, 3, RoundingMode.UP)); \[/java\]

Resultando em `0.334`. Vale lembrar também da imutabilidade da classe `BigDecimal`, que traz diversas vantagens mas deve ser usada com cuidado quando diversas operações serão realizadas em cima de um mesmo número dentro de um laço, já que diversos `BigDecimal`s serão instanciados durante a operação, podendo acarretar no mesmo [problema de performance do uso de concatenação de Strings](https://blog.caelum.com.br/revisitando-a-concatenacao-de-strings-stringbuilder-e-stringbuffer/). O Donizetti lembrou que esse assunto é bastante discutido no item 48 do [Effective Java](https://blog.caelum.com.br/effective-java-segunda-edicao/).

No JavaScript teremos o mesmo problema caso você precise realizar contas no lado do cliente, e aí podemos usar a [BigDecimalJS](http://github.com/jhs/bigdecimal.js/), que funciona de maneira análoga ao Java.

O [Rafael Ferreira](http://blog.rafaelferreira.net/) lembra que podemos ir além, e como dinheiro é algo pertencente ao nosso domínio e lógica de negócios, criamos uma classe `Money` para encapsular todo esse comportamento e evitar que `RoundingMode`, `MathContext` e escalas se espalhem por todo seu código.
