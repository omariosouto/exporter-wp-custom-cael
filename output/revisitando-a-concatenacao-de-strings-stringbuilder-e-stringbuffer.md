---
title: "Revisitando a concatenação de Strings: StringBuilder e StringBuffer"
date: "2010-06-13"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma [discussão muito antiga](http://www.javaworld.com/javaworld/jw-03-2000/jw-0324-javaperf.html) que frequentemente aparece no Java é o uso errado da concatenação de Strings, que pode [acarretar numa grave perda de performance](http://www.guj.com.br/posts/list/15/88999.java) e trashing de memória. Mas por que?

O problema é muito simples de enxergar. Imagine um laço em que você concatena uma `String` com todos os números de 0 a 30 mil:

\[java\] String numeros = ""; for (int i = 0; i<30000; i++) { numeros += i; } System.out.println(numeros.length()); \[/java\]

Em um computador bom, isso vai levar vários segundos. Agora vamos verificar o mesmo código usando um `StringBuider`:

\[java\] StringBuilder numeros = new StringBuilder(); for (int i = 0; i<30000; i++) { numeros.append(i); } System.out.println(numeros.toString().length()); \[/java\]

Rodando esse segundo código, o tempo gasto é irrisório, mal sendo percepitível. Alguns chegam até a dizer que não devemos utilizar o operador **`+`**, nem mesmo em operações simples como essas:

\[java\] String hql = "select u from"; hql += " User as u"; System.out.println(hql); \[/java\]

Porém este é o caso que o uso do operador `+` é mais que bem vindo. No fundo, este operador não existe para a JVM, é apenas um syntactic sugar na linguagem e [único operator overload do Java](http://onthethought.blogspot.com/2004/11/java-and-operator-overloading.html). O próprio compilador trata este operador, como podemos verificar no bytecode desse código através do `javap -c`, resultando no trecho de [mneumônicos como este](http://gist.github.com/436275). Logo, o que está acontecendo na verdade é um código como:

\[java\] System.out.println(new StringBuilder() .append("select u from").append(" User as u:").toString()); \[/java\]

Em outras palavras, **o operador `+` sempre usa o `StringBuilder`**, o que torna desnecessário evitar o uso do operador `+` neste caso. Se ele já usa o `StringBuilder`, por que o código que vimos primeiramente roda tão mais lento com o operador em relação ao `StringBuilder` puro? Voltando ao primeiro código, ele [gera este bytecode](http://gist.github.com/436287), que podemos facilmente perceber que há uma instanciação de um novo `StringBuilder` a cada iteração do laço, laço o qual está definido entre as instruções 5 e 34 (o `goto`). Em Java teríamos:

\[java\] String numeros = ""; for (int i = 0; i<30000; i++) { numeros = new StringBuilder() .append(numeros).append(i).toString(); } System.out.println(numeros.length()); \[/java\]

Repare que, com um novo `StringBuilder` sendo instanciado a cada iteração, a `String numeros` está sendo copiada inteiramente (`append(numeros)`) toda vez para esse novo objeto, gastando bastante tempo (no final, é um tempo quadrático em relação ao tamanho da `String`). O nosso segundo código já apresentado é bem mais eficiente: ele cria um `StringBuilder` uma única vez, fora do laço, e depois invoca o `append` apenas para as novas partes da `String`, sem ter de copiar o que já foi previamente processado (resultando em tempo linear).

Por último temos o `StringBuffer`: é a versão antiga e thread safe do `StringBuilder`, que era usado antigamente para realizar as operações do `+`. Como ele usa sincronização, [custa um pouco mais caro para executar seus métodos](http://kaioa.com/node/59), e foi preterido por essa ser uma situação thread safe.
