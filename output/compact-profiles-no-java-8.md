---
title: "Compact Profiles no Java 8"
date: "2014-09-16"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Em 2009, o engenheiro chefe da plataforma Java na Oracle, [Mark Reinhold](http://mreinhold.org/), falou no evento JavaOne a famosa frase _“[The classpath is dead](http://tech.puredanger.com/2009/06/04/javaone-jigsaw/ "The classpath is dead.").”_. Desde a época muito se escreveu sobre o projeto [JigSaw](http://openjdk.java.net/projects/jigsaw/ "Projeto JigSaw") que visa [modularizar a plataforma](http://mreinhold.org/blog/modular-java-platform "Modular Java Platform") Java, mas a implementação final só sairá para o Java 9, caso o [trem não se atrase](http://mreinhold.org/blog/late-for-the-train) novamente.

O [objetivo principal](http://openjdk.java.net/projects/jigsaw/history) do Jigsaw é trazer a modularização para a plataforma Java, mas há outras motivações: diminuir o tamanho de download (_footprint_) e melhorar o desempenho principalmente na inicialização (_bootstrap_).

Apesar do lançamento do projeto JigSaw ter sido procrastinado do Java 7 para o Java 9, foi criada uma solução intermediária para a versão 8 do Java. No Java 8 já foram definidos os [perfis da plataforma](http://openjdk.java.net/jeps/161), também chamados de [**Compact Profiles**](https://blogs.oracle.com/jtc/entry/a_first_look_at_compact "Compact Profiles"). São três profiles (1, 2 e 3) que são compostos de vários pacotes. **Compact** 1 é o menor e possui apenas os pacotes mais importantes como `java.lang` e `java.util`, **Compact 2** se baseia no 1 e adiciona outros pacotes como `java.sql`. O último, **Compact 3**, possui pacotes mais específicos como `javax.naming`. [![compact-profiles](https://blog.caelum.com.br/wp-content/uploads/2014/09/compact-profiles.png)](https://blog.caelum.com.br/wp-content/uploads/2014/09/compact-profiles.png)

O [JavaDoc](http://docs.oracle.com/javase/8/docs/api/) possui a lista completa dos pacotes e, na descrição de cada classe, foi adicionada uma declaração que documenta os perfis compatíveis.

Os [Compact Profiles](https://www.youtube.com/watch?v=QzYKAjCqzVY) tornam o Java ainda mais interessante para sistemas embarcados, pois é possível gerar uma JRE especificando um desses 3 profiles. Para tal existe a ferramenta **[jrecreate](http://docs.oracle.com/javase/8/embedded/develop-apps-platforms/jrecreate.htm "jrecreate")** que vem com o download do [JavaSE Embedded](http://www.oracle.com/technetwork/java/embedded/embedded-se/downloads/index.html "Java Embedded"). Por exemplo, ao gerar uma JRE no perfil Compact 1, um arquivo em torno de 11MB é gerado, possível usar em um RaspberryPI: `jrecreate.sh --profile compact1 --dest /tmp/smallJRE/`

Para a aplicação realmente funcionar em um determinado perfil, o compilador Java ganhou um novo flag: **`-profile`**. Esse flag determina o perfil (_compact1, compact2 ou compact3_) e o `javac` gera um erro caso uma classe incompatível esteja sendo utilizada.

Por exemplo, para compilar a classe abaixo é preciso usar pelo menos o Compact 2, pois o pacote `java.sql` não faz parte do Compact 1:

\[code language="java"\] import java.sql.\*;

class Teste {

public static void main(String... args) throws SQLException { Connection c = DriverManager.getConnection("teste"); System.out.println("oi"); } } \[/code\]

`javac -profile compact2 Teste.java //compact1 geraria um erro`

Para saber se a classe faz parte de um profile ou não, foi criada uma outra ferramenta: **[jdeps](ttp://docs.oracle.com/javase/8/docs/technotes/tools/unix/jdeps.html "jdeps")** (_java dependency analyzer_). Ela vem instalada com o JDK 8 e analisa os pacotes dependentes. Para a classe acima, a _jdeps_ mostra a saída seguinte: `jdeps -verbose:class -P Teste.class

Teste.class -> /../jdk1.8.0_20.jdk/Contents/Home/jre/lib/rt.jar (compact2) Teste (Teste.class) -> java.io.PrintStream compact1 -> java.lang.Object compact1 -> java.lang.String compact1 -> java.lang.System compact1 -> java.sql.Connection compact2 -> java.sql.DriverManager compact2 -> java.sql.SQLException compact2`

Repare que a _jdeps_ informa que a classe `Teste` depende do JAR `rt.jar` e relata todos os pacotes utilizados. _jdeps_ também serve para analisar as dependências de um JAR. Veja abaixo um exemplo usando o JAR principal do Hibernate: `jdeps -P hibernate-core-4.3.6.Final.jar

hibernate-core-4.3.6.Final.jar → /../jdk1.8.0_20.jdk/Contents/Home/jre/lib/rt.jar (Full JRE) org.hibernate (hibernate-core-4.3.6.Final.jar) -> java.io compact1 -> java.lang compact1 -> java.math compact1 -> java.sql compact2 -> java.util compact1 -> javax.naming compact3 -> javax.transaction not found -> org.jboss.logging not found org.hibernate.action.internal (hibernate-core-4.3.6.Final.jar) -> java.io compact1 -> java.lang compact1 -> java.util compact1 -> java.util.concurrent.atomic compact1 … muito mais`

No Java 9, com a entrada do JigSaw, somos obrigados a definir mais [meta informações](http://cr.openjdk.java.net/~mr/jigsaw/notes/jigsaw-big-picture-01  ) sobre os JARs/Modulos como versionamento e imports/exports, que serão [verificadas no build](http://openjdk.java.net/projects/jigsaw/doc/quickstart.html "jmod") da aplicação. Isso também significa que várias bibliotecas e frameworks vão quebrar, pois se basearam em pacotes internos da JRE que o sistema de módulos vai proibir.

Para quem procura uma solução de modularização para já, deve dar uma olhada no OSGI que é foco da [minha palestra](http://qconrio.com/presentation/do-inferno-ao-c%C3%A9u-com-osgi-fugindo-do-jar-e-do-classloader-hell) na próxima QCon no Rio de Janeiro. Espero te encontrar lá!
