---
title: "Ordenando coleções com Comparable e Comparator"
date: "2017-03-22"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Uma tarefa comum no dia a dia dos desenvolvedores é ordenar uma lista ou array. Para não inventar a roda, a [Collections API](http://docs.oracle.com/javase/tutorial/collections/index.html) do Java (também conhecida pelo nome do seu pacote, o [java.util](http://docs.oracle.com/javase/6/docs/api/java/util/package-summary.html)) vem pronta para ajudar nessa tarefa. Falamos dessa API extensivamente na [apostila do curso FJ-11](http://www.caelum.com.br/curso/fj-11-java-orientacao-objetos/), e vou aqui passar para o problema específico de ordenação.

> _Este post foi criado em 2010 e foi atualizado neste ano de 2017._

Imagine que você gostaria de ordenar uma lista de Contas bancárias. Cada conta possui um `número` (_int_) e um `titular` (_String_):

```java

Conta conta1 = new Conta(5452, "Phillip Lahm");
Conta conta2 = new Conta(1234, "Lucas Podolski");
Conta conta3 = new Conta(3145, "Arne Friedrich");

List lista = new ArrayList();
lista.add(conta1);
lista.add(conta2);
lista.add(conta3);
```

O método para ordenar uma lista se encontra na classe [java.util.Collections](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html) (repare o "s" no final). Ela possui métodos estáticos que ajudam a manipular coleções, entre eles o método `sort`. Assim podemos tentar ordenar a lista de contas:

```java

Collections.sort(lista);
```

Mas infelizmente a linha acima nem compila. Antes de invocar o método _sort_ é preciso definir o critério de ordenação: uma forma de informar, dado duas contas, qual vem "antes" e qual vem "depois".

Considerando que queremos ordenar pelo número da conta, a classe `Conta` implementar a interface [java.lang.Comparable](http://docs.oracle.com/javase/6/docs/api/java/lang/Comparable.html) que define o que será nossa "ordem natural". A interface possui apenas um método _compareTo_:

```java

public interface Comparable {
    int compareTo(T outro);
}
```

As contas então devem ser comparáveis. Vamos definir a ordem natural baseada no número da conta:

```java

public Conta implements Comparable {
        
    private int numero;
    private String titular;
    // outros metodos e atributos

    public int compareTo(Conta outraConta) {
        if (this.numero < outraConta.numero) {
            return -1;
        }
        if (this.numero > outraConta.numero) {
            return 1;
        }
        return 0;
    }
}
```

Se o número da conta atual é menor do que da _outraConta_ retormamos -1 (ou qualquer _int_ negativo, indicando que `this` deve vir "antes" de `outraConta`), se for maior retornamos 1 (ou qualquer _int_ positivo) e se for igual então devolvemos `0`.

Agora podemos invocar `Collections.sort(lista)`.

Mas se surgir a necessidade de ordenar pelo titular da conta? Não queremos alterar o método _compareTo_ na classe _Conta_, já que isso mudaria a ordem natural. Queremos definir um outro critério de ordenação. Para tal, existe uma outra interface: a _**[Comparator](http://docs.oracle.com/javase/6/docs/api/java/util/Comparator.html)**_:

```java

public interface Comparator {
    int compare(T o1, T o2);
}
```

Vamos então implementar a interface para definir a ordem pelo titular (_String_) da conta. Comparar duas Strings é difícil, mas, como você pode imaginar, esse problema já foi resolvido na API do Java.

A classe [_String_](http://docs.oracle.com/javase/6/docs/api/java/lang/String.html) já sabe compara dois strings, sabemos isso pois ela implementa a interface `Comparable` (por esse mesmo motivo podemos invocar `Collections.sort` para uma `List` de `String`). Podemos então delegar essa tarefa ao método `compareTo` das Strings:

```java

public class TitularComparator implements Comparator {
    public int compare(Conta conta, Conta outraConta) {
        return conta.getTitular().
                compareTo(outraConta.getTitular());
    }
}
```

Como escolher para que esse critério de comparação seja utilizado em vez da ordem natural? O método [sort é sobrecarregado](http://docs.oracle.com/javase/6/docs/api/java/util/Collections.html#sort%28java.util.List,%20java.util.Comparator%29) e pode receber um objeto do tipo _Comparator_:

```java

TitularComparator comparator = new TitularComparator();
Collections.sort(lista, comparator);
```

Falta mencionar que o método _compareTo_ da interface _Comparable_ deve ser consistente com o método _equals_. Quando uma conta é igual a outra (a classe _Conta_ sobreescreve o método _equals_ para definir igualdade), o método _compareTo_ deve devolver zero também. Devemos também pensar se receberemos `null` como Contas para ordenar, e tomar as devidas precauções nos comparadores.

Através de _Comparable_ e _Comparator_ também são controladas a ordenação da coleção [TreeSet](http://docs.oracle.com/javase/6/docs/api/java/util/TreeSet.html) e as chaves do mapa [TreeMap](http://docs.oracle.com/javase/6/docs/api/java/util/TreeMap.html).

Vale lembrar que muita coisa mudou desde esse post, com a entrada do Java 8, e você pode ler sobre lambdas e [outras facilidades que ajudam em funções como comparação](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/) aqui no blog da Caelum.
