---
title: "Effective Java: segunda edição"
date: "2008-07-25"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Como sabemos, a segunda edição do [Effective Java](http://java.sun.com/docs/books/effective/) foi publicada. O autor é [Joshua Bloch](http://en.wikipedia.org/wiki/Joshua_Bloch), um dos principais responsáveis pelo generics do Java, e atualmente chief java architect no Google. Esse livro é dividido em 78 itens, cada um com cerca de 3 páginas, atacando um ponto específico do java e orientação a objetos, explicando uma boa ou má prática. Simplesmente incrível, durante a leitura você sempre reconhece muita coisa que já aprendeu durante sua experiência de desenvolvimento.

Essa nova edição está estendida e revista, para cobrir as grandes mudanças do Java 5. Esse, juntamente com [outros dois livros](https://blog.caelum.com.br/livros-escolhendo-a-trindade-do-desenvolvedor-java/) (e atualmente incluiríamos também o [The Mythical Man-month](http://en.wikipedia.org/wiki/The_Mythical_Man-Month)), são de extrema importância para todo desenvolvedor na nossa opinião.

O [Fernando Boaglio](http://www.boaglio.com/) tem [um resumo](http://www.boaglio.com/index.php/2007/10/01/effective-java-depois-de-6-anos/) em seu blog, sobre todos os itens dessa nova versão. A [Vanessa Sabino](http://www.java.blogger.com.br/) publicou anos atrás um resumo completo sobre a primeira edição, que você pode conferir na coluna da direita do seu blog.

O Fernando também [postou no GUJ](http://www.guj.com.br/posts/list/90779.java) um link para [uma excelente entrevista do Joshua Bloch](http://www.infoq.com/articles/bloch-effective-java-2e), onde ele tenta resumir as más práticas, o java inefetivo: **otimização prematura**, e **escrever o próprio código quando bibliotecas boas já existem**. Além disso, Joshua Bloch é categório sobre a grande importância dos testes unitários: "_Unit testing is key. And writing your tests first is a great thing._"

Lendo essa nova edição e relembrando muito da edição anterior, escolhi aqui quatro itens que considero vitais, e vou falar sucintamente sobre cada um deles. Curiosamente todos os selecionados aqui já existiam na edição anterior, e estão mais relacionados a design que a idiomismos da linguagem, mas isso não tira a importância dos outros aqui não citados. Esses itens são muito debatidos no capítulo de Tópicos em Orientação a Objetos no nosso [trienamento de Design e Arquitetura de projetos Java](http://www.caelum.com.br/curso/fj-91-arquitetura-design-projetos-java/). Vamos a eles:

**Item 15: Minimize mutabilidade**

Classes imutáveis possuem uma série de vantagens: fáceis de manter, não possuem efeitos colaterais e acima de tudo são thread safe. Uma classe deve ser imutável a não ser que você tenha muito bons motivos para isso. Mesmo se não for possível tornar sua classe imutável, minimize a quantidade de métodos que alteram o estado do objeto. Um objeto previsível é muito mais simples de manter. Joshua Bloch cita `String`, `BigInteger` e diz que `java.util.Date` e `java.awt.Point` deveriam ter sido criadas imutáveis! Muitas APIs novas abusam da imutabilidade, como a Joda Time, classes wrapper, Money and Time do Eric Evans, etc. Aliás, é com o slogan da imutabilidade que linguagens como [clojure](http://clojure.org/) e [erlang](http://www.erlang.org/) tem chamado tanta atenção. Leia também [essa citação](http://blog.lucindo.com.br/2008/05/22/state-youre-doing-it-wrong/) no blog do Renato Lucindo.

**Item 16: Favoreça composição em vez de herança**

Esse é um tópico que já foi [discutido anteriormente nesse post](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/). O fato é o seguinte: é muito fácil usar herança de maneira errada, como é o caso de `Stack extends Vector` e `Properties extends Hashtable`. Mesmo quando usada corretamente, herança pode causar efeitos colaterais com muita facilidade, sendo que utilizar interfaces e composição pode substitui-la por completo, com o pequeno acréscimo de algumas linhas de delegação. Esse item também é citado no livro _Design Patterns_ como um dos dois princípios básicos do bom design orientado a objetos.

**Item 47: Conheça e use as bibliotecas!**

Você conhece a `ArrayDeque` do java 6? Sabia que a [java.util.Scanner](http://java.sun.com/j2se/1.5.0/docs/api/java/util/Scanner.html) pode ler facilmente arquivos com formatos caseiros, e já trazer para você `double`s, `String`s e até mesmo `BigDecimal`s? Que JAXB e JAXWS podem agora ser usados apenas com Java SE? Sabia que a `Collections` possui hoje métodos para calcular a frequência de um elemento e inverter a ordem de um `Comparator`?. Conhecer bem a biblioteca padrão do Java pode te salvar de escrever muito código já existente, testado e de qualidade. `java.io`, `java.lang` e `java.util` são APIs que funcionam como base para todo desenvolvedor e merecem um estudo aprofundado.

**Item 52: Refira a objetos pelas suas interfaces** Sem dúvida uma boa prática mais que necessária. Através dela conseguimos diminuir muito o acoplamento entre classes, deixando apenas uma fina camada entre elas: as interfaces. Sempre usar `InputStream` em vez de se acoplar em `FileInputStream`, sempre usar `List` em vez de se acoplar a `ArrayList`. Muitas vezes podemos ir mais longe, nesse último caso `Collection` pode ser o suficiente, ou até mesmo `Iterable`! Algumas pessoas levam isso tão a sério que nunca criam uma única classe concreta que não implemente uma interface. Esse item também é citado no livro _Design Patterns_, e é o outro princípio básico do bom design orientado a objetos desta forma: "_Programe voltado a interface, e não a implementação_".

Ainda existem itens fundamentais sobre Enums, Exceptions, Concorrência e Generics. Esse livro é realmente importante na sua cabeceira. Boa leitura!
