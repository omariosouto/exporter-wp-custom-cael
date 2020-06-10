---
title: "CSS fácil, flexível e dinâmico com LESS"
date: "2012-06-21"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

_CSS é o padrão para estilização visual de páginas Web, então não temos muito como fugir dele. Mas ele é cheio de limitações e isso empaca o desenvolvimento. **Conheça o [LESS](http://lesscss.org/) e seja mais produtivo no desenvolvimento Web.**_

O **LESS** é uma linguagem baseada em CSS (mesma ideia, sintaxe familiar) que gera CSS no final. O pessoal chama de pré-processador, porque, no fim, o browser só entende CSS mesmo. Você escreve um arquivo `.less` mas usa no final um `.css` compilado com o comando `lessc`. Dá pra fazer isso com um JavaScript também, que você inclui na página e compila o LESS direto no browser; útil pra testes mas não recomendado pra produção - pois toma tempo e bytes a mais.

Mas e quais são as vantagens do LESS?

![](https://blog.caelum.com.br/wp-content/uploads/2012/06/logo-less.png "less app")

## Variáveis e chega de copy/paste!

Você já precisou usar a mesma cor no CSS em vários pontos diferentes? Um título e um botão com mesma cor, por exemplo? O CSS tem uma solução pra evitar copiar e colar, que seria o uso de classes. Mas, muitas vezes, usar essa mesma classe em tantas tags diferentes não é uma boa ideia.

Programadores estão acostumados com variáveis pra isso, mas o CSS não tem nada parecido. Mas o LESS sim!

\[code language="css"\] @corprincipal: #BADA55;

#titulo { font-size: 2em; color: @corprincipal; } button { background-color: @corprincipal; color: white; } \[/code\]

Repare no uso da `@corprincipal` que não é CSS puro, mas tem uma sintaxe bem parecida e familiar. Depois de compilado, o LESS vira esse CSS:

\[code language="css"\] #titulo { font-size: 2em; color: #BADA55; } button { background-color: #BADA55; color: white; } \[/code\]

## Contas matemáticas e adeus valores mágicos

Sabe quando você tem aquele elemento principal com _960px_ mas que precisa de um padding de _35px_ e duas colunas lá dentro de tamanhos iguais mas deixando 20px entre elas? Qual o tamanho de cada coluna mesmo? _435px_. Aí você coloca no CSS:

\[code language="css"\] .container { padding: 35px; width: 960px; } .coluna { width: 435px; } \[/code\]

E quando alguém mudar o tamanho do padding, você torce pra lembrarem de refazer a conta da coluna - que, aliás, seria _(960px - 35px \* 2 - 20px) / 2 = 435px_. No LESS, você pode fazer a conta direito na propriedade e o resultado final é calculado:

\[code language="css"\] .coluna { width: (960px - 35px \* 2 - 20px) / 2; } \[/code\]

Melhor ainda, junte com as **variáveis** que vimos antes e você nem copia e cola valores!

\[code language="css"\] @total: 960px; @respiro: 35px; @espacamento: 20px;

.container { padding: @respiro; width: @total; } .coluna { width: (@total - @respiro \* 2 - @espacamento) / 2; } \[/code\]

E dá pra fazer contas de tudo que é tipo, até com cores!

## Hierarquia em prol da flexibilidade

Você tem um `#topo` com um título `h1` dentro e uma lista `ul` de links. E quer estilizar todos esses elementos. Algo assim:

\[code language="css"\] #topo { width: 100%; } #topo h1 { font-size: 2em; } #topo ul { margin-left: 10px; } \[/code\]

E se você precisar mudar o id do `#topo`? Ou trocá-lo por um `<header>` semântico? Tem que mexer em 3 lugares (e torcer pra ninguém ter usado em outro canto). Fora que o código fica desorganizado já que essas três regras não necessariamente precisam estar agrupadas no arquivo e podiam estar espalhadas por aí, apesar de serem todos sobre nosso cabeçalho.

No LESS, podemos escrever regras de maneira hierárquica, uma dentro da outra, e ele gera os seletores de parent. O mesmo CSS acima podia ser no LESS:

\[code language="css"\] #topo { width: 100%;

h1 { font-size: 2em; } ul { margin-left: 10px; } } \[/code\]

Podemos usar vários níveis de hierarquia (mas não abuse!), deixando nosso código mais estruturado, flexível e legível.

## Funções de cores e palhetas automáticas

Provavelmente você já viu algum design que tem uma cor base principal e algumas cores secundárias combinando. Talvez uma versão mais _light_ dessa cor base é usada como fundo e uma cor mais _saturada_ no botão.

Você então pega o código de cada cor no Photoshop e coloca no CSS. E, se precisar mudar a cor, deve gerar as outras secundárias, certo? No LESS, você pode usar funções pra gerar essas cores:

\[code language="css"\] @corbase: #BADA55;

body { background: lighten(@corbase, 20%); } h1 { color: @corbase; } button { background: saturate(@corbase, 10%); } \[/code\]

Vai gerar cores 20% mais lights e 10% mais saturadas:

\[code language="css"\] body { background:#dceca9; } h1 { color:#bada55; } button { background:#bfe44b; } \[/code\]

Você ainda tem: _darken_, _desaturate_, _fadein_, _fadeout_, _spin_, _mix_ e até funções matemáticas _round_.

## Reaproveitamento com mixins

Uma das coisas mais legais do LESS é sua capacidade de criar as próprias funções, que ele chama de **mixins**. É útil quando você tem que repetir a mesma coisa várias vezes, como nas propriedades CSS3 que precisam de prefixos, tipo uma **borda redonda**.

Você pode definir um mixin recebendo argumento o tamanho da borda e cuspindo as versões pros diversos navegadores:

\[code language="css"\] .arredonda(@raio: 5px) { -webkit-border-radius: @raio; -moz-border-radius: @raio; border-radius: @raio; } \[/code\]

Parece uma classe CSS mas ele recebe uma variável como parâmetro (que pode ter um valor default também). E você pode usar esse mixin facilmente:

\[code language="css"\] .painel { .arredonda(10px); } .container { .arredonda; width: 345px; } \[/code\]

Isso gera o CSS:

\[code language="css"\] .painel{ -webkit-border-radius:10px; -moz-border-radius:10px; border-radius:10px; } .container{ -webkit-border-radius:5px; -moz-border-radius:5px; border-radius:5px;

width:345px; } \[/code\]

As possibilidades são infinitas! Pense num mixin pra te ajudar a gerar aqueles **gradientes CSS3** chatos ou um mixin próprio _.botaoBonito_ que gera botões legais só recebendo uma cor base e um tamanho.

## Executando

No [site do LESS](http://lesscss.org/), você pode baixar a versão standalone dele. Você pode rodá-lo apenas [incluindo um JavaScript](http://lesscss.org/#-client-side-usage) na página que faz o parsing quando ela carrega.

Pra melhor performance, o ideal seria gerar o CSS antes usando o compilador. Há uma versão em [linha de comando](http://lesscss.org/#-server-side-usage) usando Node.JS, mas como é JavaScript, você pode rodá-lo em qualquer canto - até no [Java com Rhino](http://www.asual.com/lesscss/).

Há também programas visuais pra instalar, como o [LESS.app](http://incident57.com/less/) e dá pra testar código rapidamente online mesmo no [LessTester.com](http://lesstester.com/).

## Muito mais

A linguagem LESS tem mais alguns recursos além dos vistos nesse post. Dá pra fazer mixins mais complicados com uma espécie de if/else por exemplo e até usar pattern matching. Você encontra todos os detalhes na [documentação oficial](http://lesscss.org/#docs).

E, além do LESS, existem outros pré-processadores CSS no mercado. O [Sass](http://sass-lang.com/) é muito famoso no mundo Ruby e tem zilhões de funções, tornando-o a mais poderoso que o LESS mas mais complexo também. Há também o [Stylus](http://learnboost.github.com/stylus/) que simplifica ainda mais a sintaxe.

Aqui na Caelum usamos bastante o LESS e ele é o [preferids do mercado](http://css-tricks.com/poll-results-popularity-of-css-preprocessors/) também. E, você, usa algum preprocessador CSS? Pensa em usar dadas as limitações do CSS? Qual? Ou prefere CSS puro mesmo e ficar independente de ferramenta?

No [curso WD-43 da Caelum](http://www.caelum.com.br/curso/wd43) a gente discute boas práticas de CSS e web design, como deixar o código mais flexível, facilitar mudanças e reaproveitamento.
