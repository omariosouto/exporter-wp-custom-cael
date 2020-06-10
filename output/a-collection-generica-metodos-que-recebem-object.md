---
title: "A Collection genérica: métodos que recebem Object"
date: "2007-04-16"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Os desenvolvedores costumam levar um susto com as assinaturas dos métodos das interfaces do framework das coleções pós-java5: muito mais difícil do que o monte de `E`s, `super`s, `extends`, `?`s e `&`s são os métodos que ainda recebem `Object` como argumento.

Um exemplo é o método `contains`. Apesar dele estar na interface parametrizada `Collection<E>` ele recebe `Object` em vez de `E`! Parece um paradoxo: a vantagem do generics não seria exatamente ter uma tipagem mais forte nesses casos, em especial em classes containers? Algumas pessoas acham que é por compatibilidade binária, mas não é, porque a _erasure_ de um método que recebe `T` é `Object`, e isso manteria a compatibilidade.

Para entender melhor a decisão da Sun em cada uma dessas assinaturas de métodos você sempre precisa levar em conta dois pontos: _erasure_ e o uso do coringa. Antes vamos relembrar esses conceitos. Considere o código que imprime uma lista de objetos `Comparable`:

\[java\] void imprime(List<Comparable> lista) { for (Comparable c : lista) System.out.println(c); } \[/java\] Podemos passar para esse método uma `List<Comparable>`. E uma `List<String>`? Não podemos, porque uma `List<String>` **não é** uma `List<Comparable>`. Se isso fosse possível, poderíamos adicionar objetos `Comparable` que não apenas `String`s dentro de uma `List<String>`, quebrando o contrato! Para resolver esse problema, mudamos a assinatura do nosso método para:

\[java\] void imprime(List<? extends Comparable> lista) { for (Comparable c : lista) System.out.println(c); } \[/java\]

Agora podemos passar uma `List<String>`, pois isso é uma `List<? extends Comparable>`, isto é, uma lista de algum tipo que é compatível com `Comparable`. Porque então não usamos sempre esse idiomismo? Repare então no seguinte método:

\[java\] public void adicionaString(List<Comparable> lista) { lista.add("caelum"); } \[/java\]

Aqui temos o mesmo problema. Não podemos receber como referência uma `List<String>`! Vamos tentar aplicar o mesmo procedimento:

\[java\] public void adicionaString(List<? extends Comparable> lista) { lista.add("caelum"); // não podemos invocar métodos que recebem o tipo // parametrizado (a menos que seja passado null) } \[/java\] O código acima não compila! Isso ocorre porque `List<? extends Comparable>` pode receber uma lista de qualquer tipo que seja `Comparable`. Se isso fosse possível, alguém poderia passar como argumento uma `List<Integer>`, e no fim do método esta lista de inteiro estaria contendo uma `String`, quebrando o contrato novamente!

Como resolver esse problema? Não tem como! Se você recebe um objeto parametrizado pelo coringa `?`, você nunca poderá invocar um método desse objeto, porque você não sabe qual o tipo que ele realmente aceita! A linguagem não permite isso porque não sabe exatamente o que você vai fazer com esse objeto. Em alguns casos, semanticamente, apesar de um método receber um tipo parametrizado, não tem problema ele receber um objeto que não esteja de acordo com seu tipo parametrizado. Esse é exatamente o caso do método `contains`: não haveria problema de passar um `Integer` para o `contains` de uma `List<String>`! Mas se contains recebesse `E`, o seguinte código não compilaria:

\[java\] public boolean contemCaelum(List<? extends Comparable> lista) { return lista.contains("caelum"); // ok, recebe Object! } \[/java\]

Essa situação é a mesma para outros métodos, como o `Collection.remove(Object)`, e o `Map.get(Object)`. Ambos os casos seriam catastróficos se recebessem o tipo parametrizado como argumento.

A API de coleções traz assinaturas muito mais estranhas. Eu demorei bastante até entender por completo a assinatura de alguns métodos da `Collections`, como por exemplo o `Collections.min`:

\[java\] public static <T extends Object & Comparable<? super T>> T min(Collection<? extends T> coll); \[/java\]

em vez de simplesmente uma ingênua assinatura como:

\[java\] public static <T extends Comparable<T>> T min(Collection<T> coll); \[/java\]

Fica aí o desafio para você também queimar neurônios...
