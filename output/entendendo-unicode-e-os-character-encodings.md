---
title: "Entendendo Unicode e os Character Encodings"
date: "2006-10-23"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Todo mundo já passou por problemas com character encodings. Quem nunca abriu uma conexão JDBC com o MySQL e puxou do banco um monte de caracteres onde em vez de acentos só se viam pontos de interrogação e caracteres estranhos?

O blog do Joel Spolsky já publicou um [post sobre esse assunto](http://www.joelonsoftware.com/articles/Unicode.html), que é bem simples e direto. O fato importante é mostar que [Unicode](http://www.unicode.org/) não é um encoding. Unicode define _codepoints_ (um número) para cada letra (ou símbolo). Por exemplo, a letra ´`A`´ é o codepoint 65. A partir do Unicode 3.1, o codepoint pode até mesmo ser maior que 2^16: o fatídico 65536 (atualmente vai até 16\*65536, ou seja 0x10FFFF). Sim! Unicode nada mais é que um _tabelão_! Nas [palavras do wikipedia](http://en.wikipedia.org/wiki/Unicode), "_... (Unicode) assign a unique number to each character used in the written languages of the world_", traduzindo, Unicode associa um número único para cada caractere usado nas línguas escritas de todo o mundo.

Pois é, unicode não é uma maneira de se representar caracteres com 2 bytes. Aliás, **Unicode não é um encoding**. A pergunta "_Você está usando unicode ou latin1?_" está completamente errada. Quem é responsável por codificar um codepoint em bytes é o encoding. Aqui estamos falando de [UTF-8](http://en.wikipedia.org/wiki/Utf-8), o [ISO-8859-1](http://en.wikipedia.org/wiki/ISO_8859-1) (vulgo latin1), entre outros. Alguns encodings podem não suportar todos os codepoints possíveis, outros podem tentar economizar alguns bytes quando codificar alguns caracteres (caso do UTF-8).

Mas não quero ficar na teoria, quero passar para o código. Vamos então codificar o '`ç`' em diferentes encodings: **ISO-8859-1**, **UTF-8** e **UTF-16**. Basta colocar o seguinte código no main:

\[java\] String\[\] codes = { "ISO-8859-1", "UTF-8", "UTF-16" }; String palavra = "ç";

for (String encoding : codes) { byte\[\] b = palavra.getBytes(encoding); System.out.printf("%10s\\t%d\\t", encoding, b.length); for (int k = 0; k < b.length; k++) { String hex = Integer.toHexString((b\[k\] + 256) % 256); if (hex.length() == 1) hex = "0" + hex; System.out.print(hex); } System.out.println(); } \[/java\]

E ao rodar, teremos em cada linha o encoding, a quantidade de bytes utilizada para codificar o '`ç`' e sua representação em hexadecimal codificada.

`ISO-8859-1	1	e7
     UTF-8	2	c3a7
    UTF-16	4	feff00e7` 

Vamos tentar o mesmo para a letra '`a`':

`ISO-8859-1	1	61
     UTF-8	1	61
    UTF-16	4	feff0061` 

É interessante reparar aqui que o **UTF-8** gastou 2 bytes em um caso, e 1 byte no outro. Outro fato importante é que **UTF-8** codifica diversos caracteres da mesma forma que o **ISO-8859-1** (e este por sua vez tem uma estrita relação com o **US-ASCII**). Você pode incrementar esse código e testar com outros encodings, tais como **US-ASCII**, **Cp1252** e **UTF-16**. Você pode ver quais encodings a sua JVM suporta com `Charset.availableCharsets()`.

Como falei anteriormente, existem caracteres que extrapolam o índice do 65535. Então como ficam esses caracteres no java, já que o `char` tem apenas 2 bytes? É aí que entram os _surrogate pairs_: alguns caracteres agora são utilizados para indicar que o restante do caractere ainda está por vir! Alguns problemas surgem com isso: o método `length()` da `String` não funciona mais tão bem: ele apenas diz quantos `char`s aquela `String` possui.

Para resolver esses problemas novos métodos e classes foram adicionados ao java 5 (através da [JSR-204](http://jcp.org/en/jsr/detail?id=204)), como o `codePointCount`, na `String`. [Esse artigo da Sun](http://java.sun.com/mailers/techtips/corejava/2006/tt0822.html) discute bem esse assunto.

Mas quando ocorrem os problemas de encoding que citamos no começo do post? Um caso em potencial é quando tentamos ler uma sequência de bytes usando um encoding que não foi o que utilizamos para codificar aquela `String`. Vamos simular isso escrevendo o 'ç' em **UTF-8** e lendo como **ISO-8859-1**, e vice-versa:

\[java\] // ç escrito em UTF-8 mas lido em ISO-8859-1 System.out.println(new String("ç".getBytes("UTF-8"), "ISO-8859-1")); // ç escrito em ISO-8859-1 mas lido em UTF-8 System.out.println(new String("ç".getBytes("ISO-8859-1"), "UTF-8")); \[/java\] E o resultado:

`Ã§
?` 

Cadê o c cedilha? Você pode não ver, mas ele está por aí! Esses caracteres lhe trazem algumas lembranças?
