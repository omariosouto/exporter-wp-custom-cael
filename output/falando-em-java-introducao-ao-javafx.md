---
title: "Falando em Java: Introdução ao JavaFX"
date: "2007-07-19"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

**Atualização**: [Segunda parte do artigo de JavaFX aqui](https://blog.caelum.com.br/falando-de-javafx-parte-2/).

No último [Falando em Java](http://www.falandoemjava.com.br) apresentei uma palestra sobre JavaFX, apresentando a tecnologia e especulando sobre o futuro desse mercado. É a grande aposta da Sun no mercado das **Rich Internet Applications - RIA**, onde o Adobe Flex hoje lidera.

O [JavaFX](http://www.sun.com/software/javafx/index.jsp) foi apresentado[\[1\]](#javafx-1) pela Sun no último [JavaOne](http://sun.com/javaone) em maio desse ano como uma nova estratégia da empresa para levar o Java ao desenvolvimento fácil de interfaces ricas com o usuário. Dois produtos foram anunciados e novidades foram prometidas para breve. Hoje o que temos é o [JavaFX Mobile](http://www.sun.com/software/javafx/mobile/index.jsp) e o [JavaFX Script](http://www.sun.com/software/javafx/script/index.jsp).

O **JavaFX Mobile** é um novo sistema operacional baseado no kernel do Linux e com uma JVM portátil que pretende levar o Java ao novo mercado dos "smartphones" a la iPhone. Seria voltado ao desenvolvimento fácil de aplicativos bonitos e usáveis em ambiente móvel.

Já o **JavaFX Script** é uma nova linguagem de programação pensada para o desenvolvimento de interfaces Swing/AWT/Java2D de forma mais fácil do que hoje. A idéia da Sun é atacar o mercado Desktop, onde o Java tem ganho certo mercado ultimamente, mas também atacar o Adobe Flex e o MS Silverlight no mercado de Rich Internet Applications, as RIAs.

Hoje o mercado de aplicativos ricos embutidos no navegador é dominado pelo Adobe Flash (diretamente ou através do Flex, por exemplo). Os Applets foram a primeira tentativa da Sun de atacar esse mercado lá no começo da década de 90 mas o Java não vingou nesse mercado; o JavaFX hoje é a tentativa de retomá-lo. Isso ao mesmo tempo que a Microsoft entra com o Silverlight e onde JavaScript/AJAX consegue fazer já boa parte do que essas tecnologias se propõe a fazer.

Alguns problemas frequentemente apontados para o fracasso dos Applets:

- **Difícil** de desenvolver: programação "pura" em Swing é chato e improdutivo

- Falta de boas **ferramentas** para desenvolvimento visual: o Flash só é o que é hoje porque atacou o mercado dos designers e desenhistas antes de se preocupar com os programadores

- Java é feio?
- Java é lento? E ninguém tem Java instalado na máquina?
  
Todas essas questões já estão sendo vislumbradas pela Sun. O Java hoje não é lento (era, uma década atrás) e estima-se que 91% dos PCs possuam Java instalado. Mas ainda temos problemas: o tempo de Startup da JVM ainda é alto (em grande parte devido às operações de I/O para subir a VM) e o download é grande. Ambas as questões devem ser solucionadas com a chamada **Consumer JRE**[\[2\]](#javafx-2), uma JRE modular onde se baixa apenas os componentes necessários para rodar as aplicações normais e módulos extra são baixados automaticamente sob demanda.

As questões da dificuldade de desenvolvimento e falta de boas ferramentas são justamente o foco do JavaFX.

O JavaFX Script é uma linguagem de script declarativa e orientada a objetos, com sintaxe simplificada para desenvolvimento de GUIs. É bem diferente do Java tradicional mas já possui plugins para Eclipse e Netbeans para facilitar o desenvolvimento.

Veja uma comparação entre um Hello World em Swing e em JavaFX:

\[java\] // em Swing public class HelloSwing { public static void main(String\[\] args) { JFrame frame = new JFrame("Meu programa em Swing"); JLabel label = new JLabel("Olá Mundo"); Dimension d = new Dimension(300, 50); frame.setSize(d); frame.add(label); frame.setVisible(true); } } \[/java\] Em JavaFX: \[javafx\] Frame { title: "Meu programa em javafx" width: 200 height: 50 visible: true content: Label { text: "Olá mundo" } } \[/javafx\]

Note a semelhança com linguagens de marcação como XML, onde a forma declarativa e aninhada de se agrupar os componentes facilita o desenvolvimento e a leitura do programa.

Você pode executar esse exemplo através do Java Web Start, [clicando aqui](http://www.caelum.com.br/javafx/resources/demo-helloworld.jnlp).

Na palestra, desenvolvi um pedaço de uma loja virtual com efeitos 3D e animações. Usamos nesse exemplo recursos avançados do JavaFX como binding de componentes para classes de modelo, buscas otimizadas em arrays, triggers para eventos em variáveis, Java2D, animações e outros. No próximo artigo trato desses assuntos com mais detalhes, mostro como configurar o ambiente de desenvolvimento, bem como outras coisas como concorrência de Threads, integração com o Java e conectividade.

Você pode executar o demo [clicando aqui](http://www.caelum.com.br/javafx/resources/demo-lojavirtual.jnlp).

Os exemplos são apresentados em Java Web Start em formato jar. O ambiente de execução do JavaFX é distribuído junto e possui 2MB; nas futuras versões do JRE, o JavaFX deverá vir embutido, diminuindo consideravelmente o tamanho do download.

Para ver os fontes, basta descompactar o jar e ver os arquivos .fx. O código fonte é de propriedade da Caelum, exceto o componente DisplayShelf desenvolvido por Chris Oliver e liberado no Site dele. [\[5\]](#javafx-5)

Até o próximo artigo!

**Atualização**: [Segunda parte do artigo de JavaFX aqui](https://blog.caelum.com.br/falando-de-javafx-parte-2/)

* * *

**Algumas referências:** [\[1\]](http://developers.sun.com/learning/javaoneonline/2007/pdf/TS-3420.pdf) Apresentação do Chris Oliver sobre o JavaFX/F3 no JavaOne  
[\[2\]](http://developers.sun.com/learning/javaoneonline/2007/pdf/TS-3160.pdf) Palestra apresentada no JavaOne sobre o mercado Desktop Java e a Consumer JRE  
[\[3\]](https://openjfx.dev.java.net/Getting_Started_With_JavaFX.html) Tutorial para iniciantes em JavaFX  
[\[4\]](https://openjfx.dev.java.net/JavaFX_Programming_Language.html) Especificação da linguagem JavaFX Script  
[\[5\]](http://blogs.sun.com/chrisoliver/) Blog do Chris Oliver, desenvolvedor do JavaFX  
[\[6\]](https://openjfx.dev.java.net/) Projeto OpenJFX com a implementação do JavaFX e muito material
