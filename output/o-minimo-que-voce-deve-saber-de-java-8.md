---
title: "O mínimo que você deve saber de Java 8"
date: "2019-03-08"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Sem dúvida as mudanças do Java 8 foram as mais profundas na plataforma depois de muito tempo. Aproveitamos para atualizar esse post para também linkar as [mudanças do Java 9](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-9/) e as do [Java 10](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-10/) que, mesmo menores, também são interessantes.

Na Caelum trabalhamos por bastante tempo com as versões beta do Java, por isso conseguimos manter o nosso [curso de Java e orientação a objetos](https://www.caelum.com.br/curso-java-orientacao-objetos) atualizado, com uma [seção de classe anônimas e lambdas](http://www.caelum.com.br/apostila-java-orientacao-objetos/collections-framework/#16-17-para-saber-mais-comparators-classes-anonimas-java-8-e-o-lambda), além de seu uso em outras APIs, como as Collections e Streams que veremos aqui. Também escrevemos [um livro de Java 8](http://www.casadocodigo.com.br/products/livro-java8) e a na [nossa carreira Java Júnior da plataforma Alura](https://www.alura.com.br/carreira-desenvolvedor-java-junior).

São muitas as novidades e sumarizamos aqui as principais modificações na linguagem e na API. Vamos direto a um código para enxergar os três principais novos conceitos da linguagem.

## Ordenando coleções

Dada uma lista de Strings:

```java

List<String> palavras = Arrays.asList("rodrigo", "paulo", "caelum"); 
```

Queremos ordená-la de acordo com o tamanho de cada `String`. Para isso criamos um `Comparator` através de uma classe anônima, como já estamos habituados:

```java

Comparator<String> comparador = new Comparator<>() {
    public int compare(String s1, String s2) {
        return Integer.compare(s1.length(), s2.length());
    }
}
```

E em seguida utilizamos o `Collections.sort` para ordenar a lista com o critério de comparação definido:

```java

Collections.sort(palavras, comparador);
```

A primeira novidade do Java 8 é que podemos fazer essa chamada desta forma:

```java

palavras.sort(comparador);
```

Sim, há um novo método em `java.util.List`, que é o `sort`. Por que não fizeram antes? Pois adicionar métodos novos em uma interface pode quebrar muito código já existente em quem implementa `List`. E o que tem de diferente no Java 8?

## Conhecendo os default methods

Abrindo o código da interface `List` podemos ver como é o `sort`:

```java

default void sort(Comparator<? super E> c) {
        Collections.sort(this, c);
}
```

Podemos ter métodos concretos em interfaces a partir do Java 8! Basta utilizar o modificador `default`. Eles serão 'herdados' por todos que implementarem essa interface.

Esse recurso, chamado **default method**, permite evoluir uma interface sem quebrar compatibilidade. É uma técnica já bem conhecida no C#, Scala e outras linguagens.

Há muitos outros métodos default que foram adicionados a [API de coleções](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html), como `Collection.removeIf`, `Map.getOrDefault` e até mesmo no `Comparator`, como o `Comparator.reversed`, que devolve um novo comparador que ordena ao contrário.

## Expressão Lambda

Além de ficar mais prático de escrever o código sem o uso direto da `Collections`, podemos também criar o `Comparator` de maneira bem mais enxuta sem utilizar a sintaxe de classe anônima:

```java

Comparator<String> comparador = (s1, s2) -> {
        return Integer.compare(s1.length(), s2.length());
};
```

Essa é a sintaxe do Lambda no Java 8. Ela pode ser utilizada com qualquer **interface funcional**. Uma interface funcional é aquela que possui apenas um método abstrato (semanticamente falando pode haver diferenças).

Dessa forma o compilador consegue inferir qual método está sendo implementado nessas linhas. Diferente da geração de classes em tempo de compilação, como é feito para as classes anônimas, o [lambda do Java 8 utiliza MethodHandles e o invokedynamic](http://cr.openjdk.java.net/~briangoetz/lambda/lambda-translation.html).

### Reduzindo mais ainda o código

O código pode ficar ainda mais enxuto. Como há apenas uma instrução dentro desse lambda, não precisamos nem do `return`, nem do uso das chaves:

```java

Comparator<String> comparador = (s1, s2) -> Integer.compare(s1.length(), s2.length());
```

Ou ainda passar tudo isso diretamente como argumento para o sort:

```java

palavras.sort((s1, s2) -> Integer.compare(s1.length(), s2.length()));
```

Você pode utizar o lambda em qualquer interface antiga que seja considerada funcional:

```java

new Thread(() -> System.out.println("thread nova rodando")).start();
```

Como um dos construtores de `Thread` recebe uma interface funcional, o compilador sabe que deve tentar converter esse lambda para um `Runnable`! É sempre necessário o envolvimento de uma interface funcional. O seguinte código não compila:

```java

Object o = () -> System.out.println("thread nova rodando");
```

Compilaria se tivéssemos declarado como um `Runnable`.

Muitos dos novos métodos inseridos nas interfaces das collections recebem um argumento que é uma interface funcional, como o `Collection.forEach`:

```java

palavras.forEach(s -> System.out.println(s));
```

O `forEach` recebe um `Consumer<T>` como argumento, que é uma das muitas novas interfaces do pacote `java.util.function`. Ele tem apenas um método, o `accept`, que recebe `T` e não devolve nada. Como o método recebe apenas um argumento, repare que não foi necessário declarar `s` entre parenteses no lambda `s -> System.out.println(s)`.

## Ordenando as coleções com a expressão lambda

Podemos fazer a nossa ordenação ficar ainda mais sucinta. Interfaces podem ter métodos default estáticos, agrupando melhor métodos utilitários. O `Comparator` possui o factory method `comparing`.

Ele recebe uma `Function` que, no nosso caso, recebe uma `String` e devolve algo que queiramos usar como critério de comparação. Vamos passar um lambda que, dado a `String`, devolve seu `length`:

```java

palavras.sort(Comparator.comparing(s -> s.length());
```

## Escrevendo a expressão lambda com method reference

É muito comum um lambda simplesmente invocar um único método. Um lambda também pode ser escrito como forma de `method reference`. Em vez de `s -> s.length()` fazemos simplesmente `String::length`, ficando implícito que queremos, para a `String` passada como argumento, que o `length` seja invocado:

```java

palavras.sort(Comparator.comparing(String::length);
```

Isso é possível pois as duas linhas a seguir são equivalentes:

```java

Function<String, Integer> function = s -> s.length();
Function<String, Integer> function = String::length;
```

A segunda é até mais fácil para o compilador inferir, já que algumas vezes pode haver ambiguidade e a necessidade de tipar os argumentos do lambda, como `(String s) -> s.length()`. E você poderia passar essa `function` como argumento para o `Comparator.comparing`, mas obviamente é desnecessário, pois podemos passar diretamente o lambda, como fizemos.

Para aplicar um filtro nessa lista, como por exemplo retornar apenas as palavras com menos de 6 caracteres, gostaríamos de fazer algo como `palavras.filter(...)`. Porém o método `filter` não foi adicionado em nossa API de Collection!

Existem várias razões para isso, como não querer misturar métodos sem efeitos colaterais em coleções mutáveis, além de evitar deixar as coleções muito poluídas com tantas novas funcionalidades.

## Aumentando o poder com Stream

Para fazer essa e outras transformações comuns em nossas coleções, contamos agora com uma nova API, o `Stream`.

Para criar um `Stream` com os elementos de nossa lista só precisamos chamar o método defaut `.stream()` presente na interface `Collection` (e em outros lugares!). Fazemos `palavras.stream()` para ter um `Stream<string>`.

Essa API traz uma forma mais funcional de trabalhar com nossas coleções. Ela possui diversos métodos, como o `filter`, `map` e `reduce`, que recebem uma interface funcional como parâmetro, nos possibilitando tirar proveito dos novos recursos de `lambda` e `method reference`.

### Filtrando coleções

Para filtrar as `String`s com menos de 6 caracteres em nossa lista podemos fazer:

```java

palavras.stream()
        .filter(s -> s.length() < 6)
        .forEach(System.out::println);
```

O método `filter` recebe a interface funcional `Predicate` como parâmetro. Essa interface possui apenas o método `test` que recebe `T` e retorna um `boolean`. Você nem precisava saber disso! Com o lambda, os nomes das interfaces funcionais perdem um pouco a importância e tornam o código mais simples de ler.

### Utilizando o map

Outro método que será muito utiizado em nosso dia a dia é o `map`, podemos e devemos utilizá-lo quando precisamos aplicar transformações em nossa lista sem a necessidade de variáveis intermediárias. Para mapear as palavras pelo seu tamanho (`length`), podemos fazer:

```java

Stream<Integer> stream = palavras.stream().map(String::length);
```

Porém recebemos um `Stream<Integer>` como retorno. Para evitar esse boxing desnecessário dos tipos primitivos a API de `Streams` possui implementações equivalentes ao %%Stream%% para eles: `IntStream`, `LongStream`, e `DoubleStream`. Para utilizar um `IntStream` neste nosso caso, podemos substituir a chamada do método `map` por `mapToInt`:

```java

IntStream intStream = palavras.stream().mapToInt(String::length);
```

Em outros locais é possível evitar o boxing e unboxing. Há em `Comparator` o método `comparingInt` que recebe um `IntFunction`. Podemos fazer aquele sort do inicio do artigo com `palavras.sort(Comparator.comparingInt(String::length))`.

## Mais sobre o Stream

As implementações de `Stream` para os tipos primitivos possuem diversos métodos para auxiliar na manipulação de seus valores, como é o caso do `sum`, `min`, `max`, `count`, `average`, entre diversos outros. O interessante desse ultimo é que o seu retorno será um `Optional`, outra importante introdução do java 8:

```java

Optional<Double> media = palavras.stream()
        .mapToInt(String::length)
        .average();

System.out.println(media.orElse(0));
```

Note que o `OptionalDouble` é a implementação de `Optional` para esse tipo primitivo, essa estratégia é utilizada em grande parte das novas interfaces!

Voltando ao nosso filtro, vimos que ao fazer `palavras.stream().filter(s -> s.length() < 6)` isso nos retornou um `Stream<string>`. Para que o retorno seja uma `List<string>` contamos com o método `collect()`.

Esse método espera receber a interface funcional `Collector` como parâmetro. Para simplificar nosso trabalho, já existem diversos `factory methods` de collectors prontos na classe `Collectors`, como é o caso do `toList()`:

```java

List<String> resultado = palavras.stream()
        .filter(s -> s.length() < 6)
        .collect(Collectors.toList());
```

Ufa! Esse é um pequeno resumo essencial do que entrou nessa versão.

Se você tiver interesse, pode ver como era a sintaxe proposta para o Java 8 em 2011 [nesse antigo post](http://blog.caelum.com.br/trabalhando-com-closures-no-java-8/).

Das novidades que ficaram de fora do post, temos o suporte a múltiplas anotações do mesmo tipo, a API de `java.time` baseada no Joda Time, melhorias na inferência do generics e do operador diamante além de inúmeros ajustes nas APIs.

Muitas features ficaram de fora, como collection literals e value objects. Você pode ver [o que entrou e ficou de fora](http://openjdk.java.net/jeps/0). Mas quem sabe isso tudo não aparece junto com a reificação do generics no Java 9?
