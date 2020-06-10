---
title: "Falando de JavaFX, parte 2"
date: "2007-11-10"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Faz tempo que postei [a primeira parte dessa introdução ao JavaFX](https://blog.caelum.com.br/falando-em-java-introducao-ao-javafx/), baseada na minha palestra no [Falando em Java 2007](https://blog.caelum.com.br/fatos-e-fotos-do-falando-em-java-2007/). Se você não leu, leia primeiro [aquele artigo](https://blog.caelum.com.br/falando-em-java-introducao-ao-javafx/) antes de seguir com este. O Java FX é a proposta do mundo Java para combater o domínio do Adobe Flex no mercado de RIAs.

Relembrando, falamos que o **JavaFX Script** é uma nova linguagem de programação orientada a objetos, com sintaxe declarativa, recursos de programação funcional, tipagem estática (mas com inferência de tipos). Através do JFX podemos acessar as APIs do Swing e do AWT de forma fácil para construir interfaces ricas.

Observe o uso de componentes Swing de forma declarativa: \[javafx\] import javafx.ui.\*; Frame { width: 300 height: 100 centerOnScreen: true

title: "Minha aplicacao JavaFX" visible: true

content: Box { orientation: VERTICAL content: \[ Label { text: "Nome" }, TextField { }, Button { text: "OK" } \] } } \[/javafx\]

Nesse exemplo, usamos um Frame com um Label, um TextField e um Button dentro. O Box é um componente de layout, análogo ao uso que se faz do BoxLayout do Swing.

Como uma linguagem orientada a objetos, o Java FX tem o suporte a **classes**. A sintaxe é um pouco diferente do Java, não se assuste:

\[javafx\] public class Pessoa { public attribute nome: String; public operation limpa();

} attribute Pessoa.nome = "Nome padrão"; operation Pessoa.limpa() { this.nome = ""; } \[/javafx\]

Dentro de uma classe, podemos declarar **attributes**, **operations** e **functions** (uma operation é o análogo ao método do Java; uma function está mais para uma função como na matemática, sem efeitos colaterais). Repare que dentro da classe declaramos apenas a interface; a implementação deve ficar fora da classe. Repare também que os valores default dos atributos também são declarados fora da classe.

Agora instanciamos a classe para usá-la:

\[javafx\] var p1 = Pessoa{}; p1.nome = "Sérgio"; println(p1.nome); \[/javafx\]

Repare que não há chamada a construtor; usa-se as chaves para instanciar objetos, como nos exemplos anteriores. E também não declaramos o tipo da variável, ele é descoberto (inferida) automaticamente.

Se você leu atentamente o código da classe Pessoa, deve ter ficado horrorizado com uma coisa: os atributos são públicos! Em JavaFX é possível escrever atributos private e getters/setters como operations, mas o jeito mais comum de se fazer os getters e setters nessa plataforma é através de **triggers**.

O JavaFX possui suporte a uma série de triggers que podem ser disparadas em certos eventos da linguagem. Uma delas é na modificação de uma variável, veja:

\[javafx\] // setNome trigger on Pessoa.nome = outroValor { println("Mudei o nome para: {outroValor}"); } \[/javafx\]

Isso é praticamente um setter! (Nota: obviamente que triggers não garantem encapsulamento, por isso existe o private e a parafernalha de sempre.)

E note também a sintaxe do uso do println: em JavaFX, "concatenamos" strings acessando-as com chaves dentro da string.

Um dos recursos mais interessantes do JavaFX Script é o **data binding** automático entre Model e View: quando os dados do model mudam, a view é automaticamente atualizada e vice-versa. Por exemplo, se fizéssemos um TextField para a digitarem o nome de uma Pessoa, podemos atrelar um objeto Pessoa ao campo de texto com o operador bind:

\[javafx\] // instancia Pessoa em algum ponto do código var pessoa = Pessoa{};

// ... // na hora de fazer o formulario, atrelamos pessoa.nome ao TextField TextField { value: bind pessoa.nome } // ... \[/javafx\]

Nesse exemplo, toda vez que alteraremo valor do campo de texto, nossa instância do model estará atualizada (e se alterássemos o model, a view também veria). Esse tipo de recurso não existe no Java padrão e no Swing. (Nota: a [JSR 295](http://jcp.org/en/jsr/detail?id=295) tenta implementar algo assim para o Swing.)

Até agora usamos bastante coisa do Swing/AWT, mas além disso, podemos usar recursos de desenho em duas dimensões com Java 2D e, em breve, também suporte ao Java 3D e APIs de Multimedia. Veja como é simples desenhar com Java 2D (e observe como a sintaxe é _muito_ semelhante a um SVG):

\[javafx\] import javafx.ui.\*; import javafx.ui.canvas.\*; import java.lang.\*;

Frame { title: "Loja virtual com JavaFX" width: 700 height: 560 visible: true centerOnScreen: true content: Canvas { content: \[ Rect { x: 0 y: 0 width: 700 height: 560 fill: Color {red: 0.086, green: 0.086, blue: 0.086} }, Text { x: 20 y: 15 content: "Loja Virtual de MP3 " font: Font {faceName: "Gill Sans", size: 32, style: PLAIN} fill: Color {red: 0.176, green: 1, blue: 0.435} } \] } } \[/javafx\]

Este exemplo é o princípio da Loja Virtual que fiz para a palestra e que mostrei no artigo anterior. Ele apenas desenha um retângulo e coloca um texto. Repare no uso das classes do java 2D, no uso das cores e fontes e na forma estruturada em que estão declarados os elementos do desenho. No próximo artigo irei mostrar os bastidores da Loja Virtual para apresentar recursos avançados do JavaFX.

Para testar os códigos deste e dos outros artigos, recomendo o uso do [plugin para o Eclipse ou para o Netbeans](https://openjfx.dev.java.net/#downloads). Você pode encontrar mais detalhes sobre como usá-los na [página oficial do projeto](https://openjfx.dev.java.net/#downloads).

Algumas pessoas têm me perguntado sobre o presente e o futuro do JavaFX. A verdade é que, **hoje**, ele ainda é muito instável e muito beta. Não é para ser usado em produção ainda, não é para sairmos migrando nossos projetos para o JFX. Mas a Sun tem falado muito do JavaFX e investido pesado nisso, o que faz com a tecnologia tem um futuro potencialmente promissor.

Como disse no outro artigo, o sucesso do JFX vai depender das ferramentas que surgirem em torno da tecnologia (e a Sun sabe disso). Por isso o Flash é tão mais popular hoje em dia. Hoje já temos algumas ferramentas para o JFX desenvolvidas mais como provas de conceito, mas que dão idéia de como esse mercado será o futuro.

- A ReportMill lançou o [JFXBuilder](http://reportmill.com/jfx/), uma ferramenta poderosa para gerar relatórios em JFX;
- O projeto oficial openjfx tem o [JavaFXPad](https://openjfx.dev.java.net/#demos) que é um editor dinâmico para JavaFX que mostra um preview da tela enquanto você escreve;
- E há o [JavaFX SVG Translator](http://blogs.sun.com/chrisoliver/entry/javafx_svg_translator_preview), que transforma arquivos SVG em JavaFX; e com isso conseguimos usar qualquer editor vetorial com suporte a SVG para criar coisas para JavaFX, começando já a integrar os designers na plataforma.

Tudo isso em pouco tempo em que apenas versões de teste do JavaFX existem! Minha dica hoje é: brinque com o JavaFX, leia sobre ele e **fique de olho** na tecnologia! Espere grandes avanços em 2008!
