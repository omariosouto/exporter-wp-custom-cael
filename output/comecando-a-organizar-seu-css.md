---
title: "Começando a organizar seu CSS"
date: "2015-09-09"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Você já deve ter aberto um **CSS** com 2.000 linhas com o nome de _styles.css_, dentro de uma pasta chamada _css_. Esse é um bom momento em focar um tempo para estudar um dos modelo de **arquitetura de CSS**:

- [DRY CSS](http://vanseodesign.com/css/dry-principles/ "DRY CSS: Don’t Repeat Your CSS")
- [SMACSS](https://smacss.com/ "Scalable and Modular Architecture for CSS")
- [BEM](http://vanseodesign.com/css/block-element-modifier/ "BEM: The Block, Element, Modifier Approach To Decoupling HTML And CSS")
- [OOCSS](http://oocss.org/ "Object-Oriented CSS")
- [Atomic Design](http://bradfrost.com/blog/post/atomic-web-design/ "Atomic Desing")

Vale a pena ler os cinco modelos de arquitetura e em seguida definir com sua equipe a melhor maneira de organizar o seu CSS. Foi o que realizamos na Caelum, antes de começar a desenvolver a parte interna do nossa plataforma de curso online, o [Alura](https://www.alura.com.br "Alura plataforma de curso online").

Após ler as cinco arquiteturas de CSS, o time de desenvolvedores (Back end e Front end) se reuniu durante 3 dias por uma hora diariamente focado em resolver os seguintes pontos:

## Como definir um bom nome para uma _class_?

Durante a discussão decidimos tomar os seguintes cuidados para definir o nome de uma _class_:

- **Evitar** nome genérico demais, como _title_;
- **Evitar** nome preso a tag, como _inputText_;
- **Não** dar nome com associação a uma propriedade CSS, como _floatLeft_;
- **Não** dar nome que descreva o valor de um propriedade CSS, como _colorBlue_;
- Utilizar o **hífen** para identificar o **filho** lendo apenas o nome da _class_, como _pai**\-**filho_
- Utilizar o **underline** para adicionar um **modificador**, como _pai-filho\_modificador_;
- O nome de uma _class_ tem que começar com uma analogia com o mundo real e a segunda parte quando necessário, representar o conteúdo do elemento, por exemplo: _cardCourse_.

Utilizando os pontos acima como base, vamos dar nome para os elementos do seguinte layout:

[![cardCourse](https://blog.caelum.com.br/wp-content/uploads/2015/08/cardCourse-1024x283.png)](https://blog.caelum.com.br/wp-content/uploads/2015/08/cardCourse.png)

\[code language="html"\] <section class="cardCourse"> <img class="cardCourse-icon" src="iconHtmlCss.png"> <h3 class="cardCourse-title">Avançado a HTML e CSS</h2>

<div class="progressBar"> <span class="prgressBar-value">50%</span> <progressbar class="progressBar-bar"> Seu progresso é de 50%. </progressbar> </div> </section> \[/code\]

## Como organizar os arquivos _CSS_?

O time curtiu a organização criada pelo [BEM](http://vanseodesign.com/css/block-element-modifier/ "BEM: The Block, Element, Modifier Approach To Decoupling HTML And CSS"). Seguindo o exemplo do layout apresentado acima, criamos uma pasta _css_, dentro dela criamos outra pasta com o nome de _block_. Todos os seletores dos blocos _cardCourse_ e _progressBar_, ficam dentro de um arquivo com o nome de _cardCourse.css_ e _progressBar.css_, que fica salvo dentro da pasta _block_. E qualquer arquivo _CSS_ que não for um bloco fica na raiz da pasta _css_, como o famoso arquivo _reset.css_. Quando temos um _CSS_ que vai ser utilizado apenas em uma página, colocamos ele dentro da pasta _page_.

[![tree](https://blog.caelum.com.br/wp-content/uploads/2015/09/tree.png)](https://blog.caelum.com.br/wp-content/uploads/2015/09/tree.png)

## Como escrever seletores sem muitos problemas de especificidade?

Ter seletores com valores altos de especificidade torna a manutenção do CSS mais custosa. A melhor forma de resolver isso é utilizar seletores com baixo valor e especificidade. Pra escrever seletores com um baixo valor de especificidade, vamos entender melhor como funciona esse negócio de especificidade:

### Valores de especificidade dos seletores CSS

Id `#card`

Class `.cardCourse`  
Pseudo-class `:first-child`

Tag `section`

Seletores avançados `> + ~` e `:not()`

100

10

1

0

Cada seletor CSS tem um valor conforme foi apresentado na tabela acima. Para entender um pouco melhor vamos ver uns exemplos:

\[source language="css"\] /\* Valor de especificidade: 100 \*/ #card { ... }

/\* Valor de especificidade: 10 \*/ .cardCourse { ... }

/\* Valor de especificidade: 1 \*/ section { ... }

/\* Valor de especificidade: 10 + 10 = 20 \*/ .cardCourse .cardCourse-title { ... }

/\* Valor de especificidade: 10 + 10 + 1 = 21 \*/ .cardCourse h3.cardCourse-title { ... }

/\* Valor de especificidade: 10 + 10 = 20 \*/ .cardCourse:nth-of-type(2n) { ... } \[/source\]

Para não termos muitos problemas com valores altos de especificidades optamos em seguir as seguintes regras:

- A **ordem dos seletores no arquivo CSS** devem respeitar a **ordem do HTML**;
- **Evitar** seletores com **id** e **tag**;
- **Evitar** o uso de **seletores avançados** e **pseudo-class**.

Com técnicas como essa conseguimos deixar nosso CSS no Alura muito mais modular e de fácil manutenção. A produtividade da equipe aumentou muito. E você, como faz no seu trabalho? Tem outras dicas de como melhorar a forma que organizamos nosso CSS?
