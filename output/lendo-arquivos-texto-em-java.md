---
title: "Lendo arquivos texto em Java com Scanner"
date: "2012-11-26"
author: "Natanael Pantoja"
authorEmail: "natanael.pantoja@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

É frequente precisarmos ler arquivos textos para processá-los em lote. Eles estão nos mais variados formatos. Há muitos onde determinada coluna representa o fim de um campo (posicionais), ou cada campo é demarcado com um separador especial, como por barras, vírgulas, espaços ou tabs. Por exemplo, um arquivo que lista o nome, data de nascimento, cidade e número de compras de cada cliente:

\[code\] Natanael Pantoja|Fortaleza|27 Paulo Silveira|São Paulo|35 \[/code\]

Como fazer isso em Java? Se você buscar rapidamente, vai acabar caindo nas antigas classes `BufferedReader` e `InputStreamReader`, além de lotar seu código com `String.indexOf`, `String.substring` e os demais métodos utilitários de `String` e `StringBuilder`. Por exemplo:

\[sourcecode language="java"\] FileInputStream stream = new FileInputStream("arquivo.txt"); InputStreamReader reader = new InputStreamReader(stream); BufferedReader br = new BufferedReader(reader); String linha = br.readLine(); while(linha != null) { String nome = linha.substring(0, linha.indexOf('|')); String cidade = linha.substring(linha.indexOf('|') + 1, linha.lastIndexOf('|')); String compras = linha.substring(linha.lastIndexOf('|') + 1, linha.length()); System.out.println(nome); System.out.println(cidade); System.out.println(compras); linha = br.readLine(); } \[/sourcecode\]

Sim, um código como esse funciona, mas não é tão sucinto. Há ainda o problema de converter as `compras` para `Integer`. O código completo ficaria bastante verboso, com um `Integer.parseInt(compras)` por aí. Claro, ainda falta o correto tratamento de erros e alguns casos particulares, mas o código ilustra bem uma forma clássica de abordar esse problema. Como simplificar?

A classe `java.util.Scanner`, que existe desde o Java 5, acaba sendo preterida muitas vezes por falta de conhecimento. Ela permite trabalharmos com textos (não só de arquivos), de uma maneira diferente. Para começar, podemos ler todos os campos como `String` de maneira simples:

\[sourcecode language="java"\] Scanner scanner = new Scanner(new FileReader("arquivo.txt")) .useDelimiter("\\\\||\\\\n"); while (scanner.hasNext()) { String nome = scanner.next(); String cidade = scanner.next(); String compras = scanner.next(); System.out.println(nome); System.out.println(cidade); System.out.println(compras); } \[/sourcecode\]

Note que o código ficou mais curto. Usando o método `Scanner.useDelimiter()` podemos passar uma expressão regular para dizer qual caractere deve ser considerado o divisor de campos. Chamamos de _token_ o que há entre dois delimitadores. Nesse caso dissemos que pode ser tanto a nova linha (`\\n`) quanto o pipe (`\\|`). Com isso podemos usar o método `hasNext()` para iterar em cada token e o `next()` para recuperar o token corrente.

Outra grande vantagem da classe `Scanner` é a possibilidade de conversão de tipo de forma menos verbosa. Por exemplo, podemos extrair a quantidade de compras fazendo um simples `int compras = scanner.nextInt()`. Claro, podemos ter um problema de conversão aqui.

Além disso, há metodos análogos. Como existe o `nextInt`, existe também o `hasNextInt` para saber se há um inteiro no próximo token. Existem muitos outros, como o `scanner.nextBigDecimal()`, `scanner.nextBoolean()`, etc, como você pode ver no [JavaDoc da classe Scanner](http://docs.oracle.com/javase/7/docs/api/java/util/Scanner.html).

O recurso mais poderoso é poder utilizá-la em conjunto com [as expressões regulares no Java](http://www.vogella.com/articles/JavaRegularExpressions/article.html). Há os métodos `next(...)` e `hasNext(...)` que recebem expressões regulares como parâmetros (sendo Strings ou Patterns), não precisando ficar preso ao `useDelimiter`. Outros métodos trabalham como expressões regulares e com a API do `java.util.regex`, como o `match()`, o `findInLine()` e o `findWithinHorizon()`.

Dá até para ler um arquivo inteiro na memória com um simples código como esse:

\[sourcecode language="java"\] String arquivo = new Scanner(new File("compras.txt")) .useDelimiter("\\\\Z").next(); \[/sourcecode\]

É claro você precisa tomar muito cuidado se não souber o quão grande esse arquivo pode ser. O melhor era já ir consumindo o arquivo através dos campos e quebras que for utilizar dentro da sua aplicação. Há o outro conselho óbvio: tomar cuidado com o fechamento dos recursos, fazendo o `close()` do `Scanner`.

Quais ferramentas você utiliza para trabalhar com textos e arquivos em Java? Quais seus problemas mais comuns?
