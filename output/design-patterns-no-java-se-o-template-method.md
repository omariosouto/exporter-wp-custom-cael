---
title: "Design Patterns no Java SE: o Template Method"
date: "2007-09-04"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando alguém aprende o que é Design Pattern, ou mesmo um novo Design Pattern, fica com aquele sentimento de que já viu isso em algum lugar antes. A API do Java SE é um excelente lugar para encontrar milhares de exemplos de Design Patterns. Só a `java.util`, a `java.io` e a `java.lang` já são suficientes para estudar os mais usados.

A classe abstrata `java.io.InputStream` é um excelente exemplo. Ela possui um conjunto de métodos para leitura de bytes, porém apenas um deles é abstrato: o método `read` que lê apenas um único byte. Segue seu fonte:

\[java\] public abstract int read() throws IOException; \[/java\] Ele é abstrato pois essa classe não sabe exatamente de onde será realizada a leitura: da entrada padrão? de um arquivo? de uma socket? Esse comportamento vai ser definido através da reescrita desse método em uma de suas subclasses concretas: `FileInputStream`, `SocketInputStream`, `ByteArrayInputStream`, entre outras. Essas sim sabem realizar a operação de leitura de um byte.

Se a classe `InputStream` não sabe ler um byte, como então é possível existir um método `read` que recebe um array de bytes a ser preenchido pela leitura, que não seja abstrato? Vamos ver o fonte deste método:

\[java\] public int read(byte b\[\]) throws IOException { return read(b, 0, b.length); } \[/java\]

Este por sua vez esta invocando o método sobrecarregado do read que recebe, além da array a ser preenchida, a posição inicial e quantos bytes devem ser lidos. O fonte deste método está abaixo:

\[code lang="java" highlight="10,20"\] public int read(byte b\[\], int off, int len) throws IOException { if (b == null) { throw new NullPointerException(); } else if (off < 0 || len < 0 || len > b.length - off) { throw new IndexOutOfBoundsException(); } else if (len == 0) { return 0; }

int c = read(); if (c == -1) { return -1; } b\[off\] = (byte) c;

int i = 1; try { for (; i < len ; i++) { c = read(); if (c == -1) { break; } b\[off + i\] = (byte)c; } } catch (IOException ee) { } return i; } \[/code\]

Nas linhas 10 e 20 temos invocações ao método `read` que é abstrato! Isso é possível pois sabemos que não existe como instanciar a classe `InputStream`: ela é abstrata. Essa invocação recairá sobre um objeto que foi instanciado, logo ele possuirá uma implementação deste método `read`.

Esse é uma ótima ilustração do _Template Method_. Os métodos `read` que lêem mais de um byte são templates: eles possuem o algoritmo em si, mas ainda falta um pouco para que toda a funcionalidade deles esteja pronta. Essa parte que falta é suprida com a implementação concreta do método `read` nas classes filhas de `InputStream`. Quando a classe filha implementa esse método, os demais métodos de `InputStream` que dependem deste (os _template methods_) estarão prontos para uso!

Obviamente a `OutputStream` funciona de maneira análoga. No `java.io` ainda encontramos o _Decorator Pattern_, na `java.awt` o _Composite Pattern_, no `java.lang` temos o _Builder_ e no `java.util` temos _Iterator_, _Strategy_, _Prototype_ e muitos outros. Qual é seu exemplo preferido de Design Pattern dentro do Java SE? Conheça muitos outros no nosso [curso online de Design Patterns](http://www.caelum.com.br/curso/online/design-patterns/).
