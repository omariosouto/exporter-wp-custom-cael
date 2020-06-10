---
title: "Criando animações no Android"
date: "2014-04-30"
author: "felipe.torres"
authorEmail: "felipe.torres@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nos [cursos de Android da Caelum](http://www.caelum.com.br/cursos-mobile/), é comum encontrarmos alunos que queiram incorporar efeitos visuais à aplicação. Porém, essa necessidade é mais visível ao se programar jogos, onde é bastante comum o uso de animações, seja dos personagens ou do cenário. Existem várias técnicas para se criar animações, dentre as quais está aquela que é a mais antiga e intuitiva: a animação quadro a quadro.

[![Animação](https://blog.caelum.com.br/wp-content/uploads/2014/04/animation-150x150.jpg)](https://blog.caelum.com.br/wp-content/uploads/2014/04/animation.jpg)Ao criar uma animação quadro a quadro, colocamos uma sequência de imagens estáticas que, ao serem exibidas numa certa velocidade produz o efeito de movimento. Como o Android já possui suporte para vários tipos de animações, vamos ver como criar uma animação quadro a quadro, que chamamos no Android de _Animation Drawable_.

Primeiramente, vamos colocar o png das nossas imagens na pasta _res/drawable_. É nessa pasta que o Android vai procurar as imagens usadas numa aplicação. Com os png na pasta, vamos dizer para o Android em qual ordem e por quanto tempo as imagens deverão ser exibidas na nossa animação. Para isso, na pasta _res/drawable_ vamos criar um xml chamado _animacao.xml_ e utilizar uma _tag_ chamada `animation-list`:

\[code language="xml"\] <animation-list xmlns:android="http://schemas.android.com/apk/res/android" android:oneshot="false"> <item android:drawable="@drawable/imagem1" android:duration="100"/> <item android:drawable="@drawable/imagem2" android:duration="100"/> <item android:drawable="@drawable/imagem3" android:duration="100"/> <item android:drawable="@drawable/imagem4" android:duration="100"/> </animation-list> \[/code\]

Perceba que cada item dessa lista contém a imagem, que está na pasta _res/drawable_, e a duração do tempo que essa imagem será exibida. Além disso, o atributo `android:oneshot` diz se essa animação deve ficar em _loop_ infinito ou parar assim que o último quadro for exibido. No nosso caso, deixaremos em _loop_ infinito ao setar esse atributo como _false_.

Nossa animação está pronta, agora precisamos atribuí-la a um componente de _view_ da nossa aplicação.

Vamos criar um `ImageView` que conterá nossa animação e chamá-lo de `animacao` com o uso do atributo `android:id`:

\[code language="xml"\] <LinearLayout android:layout\_width="match\_parent" android:layout\_height="match\_parent"> <ImageView android:layout\_width="50dp" android:layout\_height="50dp" android:id="@+id/animacao" /> </LinearLayout> \[/code\]

Com esse `ImageView` criado, basta setar nosso xml de animação nele:

\[code language="java"\] ImageView imageView = findViewById(R.id.animacao); imageView.setBackgroundResource(R.drawable.animacao); AnimationDrawable animation = (AnimationDrawable) imageView.getBackground(); animation.start(); \[/code\]

Com isso, podemos ver nosso `ImageView` com uma animação.

Além de animações quadro a quadro, podemos alterar a posição de um elemento de _View_ dando a ilusão de movimento. Para isso, podemos usar a classe `TranslateAnimation`.

Vamos deslocar nossa `ImageView` para dar a ilusão de movimento:

\[code language="java"\] Animation deslocamento = new TranslateAnimation(0, 1000, 0, 0); deslocamento.setDuration(3000); imageView.startAnimation(deslocamento); \[/code\]

Note que o `TranslateAnimation` recebe quatro inteiros: eles significam, respectivamente, as diferenças entre as posições iniciais e finais no eixo x e y. No nosso caso, estamos fazendo um movimento horizontal (por isso os dois ultimos valores são zeros) da esquerda para a direita começando onde o `ImageView` estiver posicionado (por isso, o primeiro valor é zero), e indo até a posição 1000 da minha tela.

Após definir as posições do nosso movimento, podemos definir a sua duração com o método `setDuration()`.

Além do _Animation Drawable_, o Android oferece [vários outros tipos de animações](http://developer.android.com/guide/topics/graphics/overview.html) que são bastante úteis para jogos ou até mesmo para aplicativos comuns.

E se quiser saber mais sobre o desenvolvimento Android e Mobile em geral não deixe de visitar a [MobileConf RJ 2014](http://www.mobileconf.com.br/ "Mobile Conf Rio de Janeiro")!
