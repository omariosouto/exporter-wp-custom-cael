---
title: "10 mixins em Sass que você deveria usar em seus projetos"
date: "2016-09-05"
author: "natan.souza"
authorEmail: "natan.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O texto a seguir é uma tradução livre e autorizada do excelente post “[10 Sass (SCSS) mixins you should be using in your projects](http://engageinteractive.co.uk/blog/top-10-scss-mixins)”, de [Jamie Wright](http://engageinteractive.co.uk/about/jamie-wright). É mostrado dez dicas de mixins feitos em Sass que ele usa em seu trabalho e que com certeza são códigos muito úteis para qualquer dev front-end que usa o Sass como pré-processador. 

Primeiramente, obrigado Jamie pelo post. E muito obrigado também por ter permitido que eu fizesse essa tradução. Ah...Importante frisar que a tradução não é literal, adaptei algumas coisas para pt-br. 

Se você quer aprender esse pré-processador CSS na prática, do zero, dá uma olhada no meu [livro de Sass](https://www.casadocodigo.com.br/products/livro-sass), publicado pela [Casa do Código](https://www.casadocodigo.com.br). E há também meu [curso de Sass na Alura](https://www.alura.com.br/curso-online-sass). Agora vamos a lista de mixins!

## 1 - Centralizar

Para centralizar rapidamente um elemento de bloco sem ter que se preocupar se há uma \`margin-top\` ou \`margin-bottom\` já aplicada:

\[code language="css"\] @mixin push--auto { margin: { left: auto; right: auto; } } \[/code\]

## 2 - Pseudo-elementos

Quando você usa o _::before_ e o _::after_ você sempre precisa desses três, então economize duas linhas de código todas vez que usar este mixin.

\[code language="css"\] @mixin pseudo($display: block, $pos: absolute, $content: ''){ content: $content; display: $display; position: $pos; }

\[/code\]

Veja abaixo um exemplo usando nenhum argumento (você também vai ver este mixin em outros abaixo também).

\[code language="css"\]

div::after { @include pseudo; top: -1rem; left: -1rem; width: 1rem; height: 1rem; }

\[/code\]

 

## 3 - Proporção responsiva

Nós usamos este aqui para criar elementos escaláveis (normalmente imagens / backgrounds) que precisem manter a proporção.

\[code language="css"\] @mixin responsive-ratio($x,$y, $pseudo: false) { $padding: unquote( ( $y / $x ) \* 100 + '%' ); @if $pseudo { &:before { @include pseudo($pos: relative); width: 100%; padding-top: $padding; } } @else { padding-top: $padding; } } \[/code\]

Então para usar este mixin você poderia escrever o código abaixo e sua \`div\` teria uma proporção comumente utilizada por vídeos (16:9), como do Youtube:

\[code language="css"\] div { @include responsive-ratio(16,9); } \[/code\]

 

## 4 - Triângulos em CSS

Este mixin resolve toda a trabalheira de criar triângulo em CSS, tudo isso sem imagens. Basta você especificar a cor, o tamanho e a direção que vai sair do elemento, e pronto!

\[code language="css"\]

@mixin css-triangle($color, $direction, $size: 6px, $position: absolute, $round: false){ @include pseudo($pos: $position); width: 0; height: 0; @if $round { border-radius: 3px; } @if $direction == down { border-left: $size solid transparent; border-right: $size solid transparent; border-top: $size solid $color; margin-top: 0 - round( $size / 2.5 ); } @else if $direction == up { border-left: $size solid transparent; border-right: $size solid transparent; border-bottom: $size solid $color; margin-bottom: 0 - round( $size / 2.5 ); } @else if $direction == right { border-top: $size solid transparent; border-bottom: $size solid transparent; border-left: $size solid $color; margin-right: -$size; } @else if $direction == left { border-top: $size solid transparent; border-bottom: $size solid transparent; border-right: $size solid $color; margin-left: -$size; } }

\[/code\]

 

## 5 - Estilos de fonte

Elimine a dor de definir estilos para uma fonte (este mixin assume que você já incluiu a fonte). Defina a fonte de fallback uma vez e nunca mais se preocupe com isso de novo.

\[code language="css"\]&lt;/pre&gt;

@mixin font-source-sans($size: false, $colour: false, $weight: false, $lh: false) { font-family: 'Source Sans Pro', Helvetica, Arial, sans-serif; @if $size { font-size: $size; } @if $colour { color: $colour; } @if $weight { font-weight: $weight; } @if $lh { line-height: $lh; } }

\[/code\]

 

## 6 - Placeholders

Eles são uma chatice quando você tem que definir o estilo em todos os formatos diferentes, esse mixin o arruma pra você:

\[code language="css"\]

@mixin input-placeholder { &.placeholder { @content; } &:-moz-placeholder { @content; } &::-moz-placeholder { @content; } &:-ms-input-placeholder { @content; } &::-webkit-input-placeholder { @content; } }

\[/code\]

Se por acaso você não tiver acostumado a usar o @content, um exemplo de uso:

\[code language="css"\]

input, textarea { @include input-placeholder { color: $grey; } }

\[/code\]

 

## 7 - Media queries

Essa é pra economizar tempo! Você pode usar valores em pixel mas nós temos _breakpoints_ padrões definidos que também funcionam, economizando tempo enquanto você vai deixando seus próprios _breakpoints_ mais consistentes.

\[code language="css"\] $breakpoints: ( "phone": 400px, "phone-wide": 480px, "phablet": 560px, "tablet-small": 640px, "tablet": 768px, "tablet-wide": 1024px, "desktop": 1248px, "desktop-wide": 1440px ); @mixin mq($width, $type: min) { @if map\_has\_key($breakpoints, $width) { $width: map\_get($breakpoints, $width); @if $type == max { $width: $width - 1px; } @media only screen and (#{$type}-width: $width) { @content; } } } \[/code\]

O exemplo abaixo mostra o \`padding\` e o \`font-size\` aumentando quando a largura da tela fica maior que 1024px.

\[code language="css"\]

.site-header { padding: 2rem; font-size: 1.8rem; @include mq('tablet-wide') { padding-top: 4rem; font-size: 2.4rem; } }

\[/code\]

 

## 8 - Z-index

Tecnicamente este aqui é uma função, mas eu senti que valia colocá-lo na lista, já que é muito fácil perder o controle sobre os valores de \`z-index\` quando se trabalha com vários arquivos diferentes. Então nós criamos este mixin para que pudéssemos colocar esses valores em um só lugar para facilitar a manutenção. Apenas coloque esse _snippet_ no seu arquivo variaveis.scss ou configs.scss (precisa ser importando antes de qualquer outro arquivo que faz referência ao z-index, claro).

\[code language="css"\]

@function z($name) { @if index($z-indexes, $name) { @return (length($z-indexes) - index($z-indexes, $name)) + 1; } @else { @warn 'There is no item "#{$name}" in this list; choose one of: #{$z-indexes}'; @return null; } } $z-indexes: ( "outdated-browser", "modal", "site-header", "page-wrapper", "site-footer" );

\[/code\]

Aí sempre que você for usar um valor de z-index, nomeie-o igual a sua classe e adicione no arquivo de variáveis/configs, como no código abaixo. Você nunca mais vai ter que esquentar a cabeça com um \`z-ndex: 9999999\`.

\[code language="css"\] .site-header { z-index: z('site-header'); } \[/code\]

 

## 9 - Hardware

Simples e eficaz para quando você precisa ligar a aceleração de hardware para alguma animação, deixando tudo rápido, elegante e sem lag.

\[code language="css"\]

@mixin hardware($backface: true, $perspective: 1000) { @if $backface { backface-visibility: hidden; } perspective: $perspective; } \[/code\]

 

## 10 - Truncar

Truncar um elemento não é tão simples como deveria ser, mas pode ser com este mixin. O que significa que quando você incluí-lo terá apenas que especificar um limite com um valor de \`max-width\`, e ser feliz!

\[code language="css"\] @mixin truncate($truncation-boundary) { max-width: $truncation-boundary; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; } \[/code\]

 

## E isso não é tudo!

Nós temos muito mais de onde veio esses, desde adicionar um _anti-aliasing_ (suavização) em texto, ou remover a opção de selecionar texto em um elemento (útil para menus ou botões etc). Você pode achar todos eles no nosso [arquivo de mixins](https://github.com/engageinteractive/front-end-baseplate/blob/master/src/scss/utility/_mixins.scss) (que faz parte do [Engage Front End Baseplate](https://github.com/engageinteractive/front-end-baseplate) lá no Github).
