---
title: "Generics, inferência de tipos e reificação no Java 7"
date: "2007-04-08"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muito tem se falado sobre o Java 7 SE e que mudanças na linguagem essa versão poderá trazer. Alex Miller [criou um excelente post](http://tech.puredanger.com/java7) com muito links para JSRs e blogs sobre as propostas para a linguagem. Peter Ahe publicou também um [interessante wish list](http://blogs.sun.com/ahe/entry/java_se_7_wish_list).

Maneiras alternativas de criarmos coleções usando uma sintaxe mais enxuta já foram discutidas por [Stephen Colebourne](http://www.jroller.com/page/scolebourne?entry=java_7_short_declarations), [Peter Ahe](http://blogs.sun.com/ahe/entry/factory_methods) e [Rafael Ferreira](http://rfframblings.blogspot.com/2006/09/arrays-genricos-e-o-maldito-compilador.html). Gostaria de salientar que o Java 5 já trouxe alguns métodos interessantes na `Collections` com essa finalidade. Exemplos são quando queremos criar uma coleção vazia ou com apenas um elemento:

\[java\] List<String> x = Collections.emptyList(); List<Integer> y = Collections.singletonList(5); \[/java\]

Existem métodos análogos para a criação de conjuntos e mapas. A assinatura do método `emptyList` é `<T> List<T> emptyList()`. É interessante que a linguagem _infere_ <T>, descobrindo que nesse primeiro caso `T` deve ser `String`. Mas isso nem sempre acontece:

\[java\] static void metodo(List<String> lista) { // faz alguma coisa } // dentro do main: metodo(Collections.emptyList()); \[/java\] A invocação acima não compila, pois tenta fazer `T` como `Object`! Algumas pessoas acham que [isso é um bug](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6293352), mas o pessoal da Sun decidiu por não tentar inferir o tipo em determinadas situações. Na verdade, o compilador só tenta inferir o tipo em dois específicos casos: na clausula de `return` ou em uma atribuição.

A especificação da linguagem Java tem uma sintaxe especial no caso de você querer indicar qual é o tipo que deve ser utilizado em uma invocação de método genérico:

\[java\] metodo(Collections.<String>emptyList()); \[/java\]

Pronto! Agora o Java sabe que `T` deve ser `String` nessa invocação, e nosso código passa a compilar. Uma sintaxe esdruxula e pouco comum.

Enquanto todo mundo comenta sobre as closures do Java 7, sem dúvida meu principal interesse sobre o Java 7 é a [reificação de tipos genéricos](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=5098163), isso é, em tempo de execução, poder determinar os tipos parametrizados de um objeto. Hoje em dia isso não é possível por causa da _erasure_, que mantém a compatibilidade com o código pré-generics: um objeto não sabe, em tempo de execução, quais são os tipos que foram usados nos seus parâmetros durante sua instanciação. Isso cria algumas clássicas questões nos fóruns, como "_porque não posso criar uma array de `T`?_" ou "_Porque não posso usar `T.class`?_". [Muitas](http://www.weiqigao.com/blog/2007/01/20/java_generics_let_the_other_shoe_drop.html) [pessoas](http://gafter.blogspot.com/2006/11/reified-generics-for-java.html) [consideram](http://blogs.sun.com/ahe/entry/reification) até a hipótese de quebrar a compatibilidade binária do Java, e fazem propostas interessantes para resolver esse problema.

Creio que o Java 7 trará muitas novas surpresas, algumas interessantes como as closures, outras infames como o suporte a literais XML dentro da linguagem. Só espero que eles [continuem com a premissa do Java](http://developers.sun.com/learning/javaoneonline/2005/coreplatform/TS-7955.pdf), de deixá-la legível e simples, e não façam como no C++, onde todo novo recurso interessante foi adicionado, tornando a linguagem de difícil legibilidade em alguns casos.
