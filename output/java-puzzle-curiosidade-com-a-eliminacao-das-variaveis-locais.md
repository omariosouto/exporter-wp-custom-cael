---
title: "Java Puzzle: curiosidade com a eliminação das variáveis locais"
date: "2009-06-14"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A lista de emails interna de desenvolvedores da Caelum sempre foi muito ativa, e ultimamente anda aparecendo alguns dos clássicos Java Puzzlers para serem debatidos. O Márcio Hasegawa recentemente postou [o problema mais recente da Java Specialists Newsletter](http://www.javaspecialists.eu/archive/Issue173.html):

**Problema**

Por que isso dá `OutOfMemoryError`? Repare que criamos duas arrays que gastarão mais da metade da memória que temos, porém a primeira pode (?) ser captada pelo garbage collector, já que seu escopo termina logo:

\[java\] class JavaMemoryPuzzle { private final int dataSize = (int) (Runtime.getRuntime().maxMemory() \* 0.6);

public void f() { { byte\[\] data = new byte\[dataSize\]; }

byte\[\] data2 = new byte\[dataSize\]; }

public static void main(String\[\] args) { JavaMemoryPuzzle jmp = new JavaMemoryPuzzle(); jmp.f(); } } \[/java\]

Já esse código, com um pequeno int i = 0 no meio, roda sem estourar a memória:

\[java\] class JavaMemoryPuzzlePolite { private final int dataSize = (int) (Runtime.getRuntime().maxMemory() \* 0.6);

public void f() { { byte\[\] data = new byte\[dataSize\]; }

int i = 0; byte\[\] data2 = new byte\[dataSize\]; }

public static void main(String\[\] args) { JavaMemoryPuzzlePolite jmp = new JavaMemoryPuzzlePolite(); jmp.f(); System.out.println("sem OutOfMemoryError"); } } \[/java\]

**Solução**

O Sérgio Lopes respondeu na lista de maneira muito apropriada. Utilizou o bytecode para justificar o comportamento do garbage collector. Vou parafrasea-lo a partir daqui:

Se você olhar o bytecode gerado dá pra ver a diferença (`javap -c Puzzle`). A versão sem declaração do `int` gera:

   0: aload\_0
   1: getfield #24; //Field dataSize:I
   4: newarray byte
   6: astore\_1
   7: aload\_0
   8: getfield #24; //Field dataSize:I
   11: newarray byte
   13: astore\_1
   14: return

Vemos que no `6` ele guarda a referência do primeiro array (`astore`) na variável local `_1` e depois ele cria o novo array na `11` (`newarray`). O problema é que a variável `_1` ainda se referência para a primeira array, impedindo que o GC colete-a! **Apenas depoisde já ter instanciado a segunda array** ele guardará essa referência na mesma posição de variável local (`_1`). Nesse caso já é tarde demais e o heap estourou.

O bytecode da versão que não estoura é parecido, porém mostra a **variável local `int i = 0` "reutilizando"** o espaço da referência a primeira array e, portanto, liberando o objeto referenciado anteriormente naquela posição para uma possível coleta:

   0: aload\_0
   1: getfield #24; //Field dataSize:I
   4: newarray byte
   6: astore\_1
   7: iconst\_0
   8: istore\_1
   9: aload\_0
   10: getfield #24; //Field dataSize:I
   13: newarray byte
   15: astore\_2
   16: return

Reparem que em `6` ele guarda a referência ao array na variável de posição `_1` e depois ele guarda `int` (que vale `0`, valor empilhado por `iconst_0`) na **mesma** posição (linha `8`), "reutilizando" o espaço da variável **antes** de criar outro array gigante. Nesse caso, a referência ao segundo array é colocada na variável local `_2` (linha 15).

Moral da história: só teremos liberadas as variáveis locais quando o método acaba e não quando os escopos acabam, mas o compilador pode "sem querer" liberar algumas no meio do caminho caso vá usar mais variáveis, reutilizando espaços não mais utilizados. Interessante!
