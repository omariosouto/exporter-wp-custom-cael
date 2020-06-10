---
title: "ConcurrentModificationException e os fail-fast iterators"
date: "2010-08-18"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A `java.util.ConcurrentModificationException` costuma surpreender a muitos: como uma exception com esse nome pode aparecer mesmo em uma aplicação single threaded, que não envolve concorrência alguma no acesso dessa coleção?

Para entender melhor, vale relembrar que as coleções muito antigas, como `Vector` e `Hashtable`, são _thread safe_, implementado através do uso do `synchronized` em seus métodos e iteradores (`Enumeration` na época). No Java 1.2, com a entrada das interfaces `Collection`, `List`, `Set` e `Iterator`, as novas implementações optaram por não ser thread safe, dado o custo de performance que o `synchronized` apresentava (hoje em dia é muito, muito menor), e de que a grande maioria dos casos de uso dessas estruturas não necessitavam de _thread safety_.

Essa novidade, das novas coleções não serem preparadas para o uso em um ambiente multi thread, poderia causar surpresas para quem não a estivesse esperando. Em vez de deixar alguém percorrer uma coleção através de um `Iterator` enquanto ela é modificada concorrentemente (acarretando em dados incorretos, `null`s, etc) optou-se por evitar esses casos. Para isso tenta-se "adivinhar" quando houver mais de uma thread trabalhando com a mesma coleção concorrentemente (uma modificando a coleção, e outra iterando-a).

Como fizeram isso sem usar mecanismos de lock? Usando um contador!

Imagine que a cada modificação na estrutura de uma `ArrayList`, nós incrementamos um `contador` (`modCount`). Quando um `Iterator` é requisitado pelo método `iterator()`, esse contador é guardado como atributo (`expectedModCount`). Cada vez que você invocar os métodos `next()` e `remove()` (lembrando que temos apenas 3 métodos na interface [`Iterator`](http://download.oracle.com/javase/6/docs/api/java/util/Iterator.html)), esse iterador vai checar se o contador da `ArrayList` é exatamente igual ao número que era esperado, isso é, tem o mesmo valor desde quando começamos a percorrer seus elementos. Caso os valores sejam diferentes, a `java.util.ConcurrentModificationException` é lançada, pois foi detectada uma modificação concorrente (_comodification_).

Mas como essa exceção pode ocorrer mesmo quando não temos outras threads acessando a mesma coleção? O problema é exatamente essa tentativa de detectar um acesso concorrente:

\[java\] List<String> nomesDosAlunos = new ArrayList<String>(); // ... popula lista com strings for (String nome : nomesDosAlunos) { if (nome.equals("nome procurado")) { nomesDosAlunos.remove(nome); } } \[/java\]

Esse código vai lançar `ConcurrentModificationException` caso encontre a `String` procurada:

\[code\] Exception in thread "main" java.util.ConcurrentModificationException at java.util.AbstractList$Itr.checkForComodification(AbstractList.java:372) at java.util.AbstractList$Itr.next(AbstractList.java:343) ... \[/code\]

Isso ocorre pois o `Iterator` utilizado internamente nesse laço vai detectar, na próxima chamada ao seu método `next()`, que o número de modificações desta `ArrayList` é diferente de quando ele foi instanciado. Essa stacktrace pode confundir um pouco, já que o uso do _enhanced for_ esconde a utilização do `iterator`, e não conseguimos ver explicitamente a invocação do método `next` na linha do `for`.

Repare que a detecção nesse caso single threaded é arbitrária para evitar casos estranhos (se removessemos o objeto da lista, o `iterator` deveria ainda percorre-lo?), e coleções sem _fail fast iterators_ vão possibilitar esse tipo de remoção e outras modificações sem lançar excessões. Como então evitar essa exception nas coleções com _fail fast iterators_? Utilizando o `iterator.remove()` em vez do _enhanced for_:

\[java\] List<String> nomesDosAlunos = new ArrayList<String>(); // ... popula lista com strings for (Iterator<String> i = nomesDosAlunos.iterator(); i.hasNext();) { String nome = i.next(); if (nome.equals("nome procurado")) { i.remove(); } } \[/java\]

Esses iteradores são chamados de **_fail-fast_** por possuirem essa característica de falhar quando uma modificação concorrente é detectada. Essa é apenas uma **tentativa** do `Iterator` em encontrar possíveis bugs, e existem casos e combinações em que a modificação concorrente pode passar desapercebida por esse mecanismo, e você não deve se basear nessa exception para garantir que sua aplicação é _thread safe_.

E se precisarmos usar uma lista em ambiente multi thread e percorrer seu iterator enquanto a modificamos? Podemos usar a `[CopyOnWriteArrayList](http://download.oracle.com/javase/6/docs/api/java/util/concurrent/CopyOnWriteArrayList.html)` (nos casos de muita leitura e pouca escrita) ou ainda, se não for precisar de acesso aleatório aos elementos, utilizar uma `Queue` como a `[ConcurrentLinkedQueue](http://download.oracle.com/javase/6/docs/api/java/util/concurrent/ConcurrentLinkedQueue.html)`. Utilizar o antigo `Vector` ou a própria `ArrayList` em conjunto com `[Collections.synchronizedList](http://download.oracle.com/javase/6/docs/api/java/util/Collections.html#synchronizedList(java.util.List))` é thread safe e não vai lançar `ConcurrentModificationException` desde que você sincronize o uso de seus iterators:

\[java\] synchronized(nomesDosAlunos) { for (String nome : nomesDosAlunos) System.out.println(nome); } \[/java\]

A performance dessas diferentes escolhas pode mudar drasticamente dependendo do contexto de sua aplicação. Pode também não ser necessário utilizar uma coleção que se preocupa com _thread safety_: algumas vezes basta diminuir o escopo da coleção (pode não ser necessário deixá-la na session, por exemplo) ou ainda trabalhar com cópias defensivas.
