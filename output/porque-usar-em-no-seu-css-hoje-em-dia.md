---
title: "Porque usar 'em' no seu CSS hoje em dia?"
date: "2014-09-30"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Talvez você programe pra Web há muitos anos como eu e tenha ouvido falar como em é melhor que pixel. Mais flexível. Mais acessibilidade. **Será?**

## Como os ems funcionam?

A grande diferença de `em` e `px` é que `em` é uma **medida relativa**. O valor é calculado levando sempre em consideração o `font-size` do pai. Isso quer dizer que um elemento com `font-size: 2em;` vai ter o dobro do tamanho da fonte do pai, seja ele qual for.

E assim por diante. O pai do pai do pai do pai... É uma grande multiplicação de valores até chegar na raiz do documento, o `<html>`. Se não tiver uma fonte definida, a maioria dos navegadores usa **16px como padrão** para o `<html>`.

Isso quer dizer que esse código:

\[code language="html"\] <html> <body> <main style="font-size: 1.5em"> <h1 style="font-size: 2em">Titulo</h1> <p style="font-size: 0.75em">Texto.</p> </main> </body> </html> \[/code\]

..acaba sendo equivalente a:

\[code language="html"\] <html style="font-size: 16px"> <body style="font-size: 16px"> <main style="font-size: 24px"> <h1 style="font-size: 48px">Titulo</h1> <p style="font-size: 18px">Texto.</p> </main> </body> </html> \[/code\]

## Mito: em não é mais acessível

A recomendação de que em é melhor para acessibilidade vem da época dos IEs velhos, 6, 5.5. Nesses navegadores, quando o usuário tinha dificuldade de visão, podia aumentar a fonte. Na prática, a fonte base do `<html>` era aumentada. Ou seja, usava 16px por padrão mas com o zoom isso virava 20px por exemplo.

Assim, todos os elementos que estavam com em tinham seu valor recalculado e a página aumentava de tamanho. **Era uma espécie de zoom, essencial para acessibilidade.** Os elementos que estavam com `px`, porém, não aumentavam de tamanho. Péssima acessibilidade. E assim nasceu o modismo do `em`.

Mas todos os navegadores modernos, IE inclusive, não tratam mais o zoom assim. **Na Web moderna, o conceito de pixel é relatevo também.** [Escrever 1px no CSS não significa necessariamente um pixel físico na tela](http://sergiolopes.org/resolucoes-dpi-pixel-ratio-retina/). Quando o usuário dá um zoom de 200% por exemplo hoje em dia o navegador aumenta todas as medidas, inclusive o pixel. Isso quer dizer que `1px` no CSS vai ser desenhado com o dobro de pixels físicos (`2px x 2px` numa tela comum).

Ou seja, **não precisamos de `em` para acessibilidade**.

## Onde o em faz sentido então?

A grande vantagem do `em` é porém no seu aspecto de multiplicar os valores de acordo com os pais. Isso quer dizer que você pode mudar o `font-size` de um elemento e isso afetar todos os seus filhos. É muito útil para criar seções na página onde os elementos devem aumentar proporcionalmente entre si - um componente, um widget.

Ou seja, usamos `em` para facilitar a escrita do nosso CSS. E por isso é bom dominar o uso de `em` e saber aproveitá-lo no seu código.

O `em`, claro, não é para todos os cenários. Ele vincula as medidas do filho com o font-size do pai. Você mexe no pai e o filho é afetado. Isso às vezes é indesejado. Só usamos `em` quando existe uma relação estrutural entre o filho e o pai e queremos que um afete o outro. Onde não fizer sentido, continue usando `px`.

## O caso famoso dos designs responsivos

O `em` teve uma volta triunfante nos designs responsivos. É que é muito frequente você querer uma fonte menor no mobile, que tem menos espaço visível, mas uma fonte maior no Desktop, mais espaçoso. E você quer que a página toda aumente proporcionalmente. Por isso o `em`.

Fazemos algo assim:

\[code language="css"\] @media (min-width: 500px) { html { font-size: 1.25em; } } @media (min-width: 800px) { html { font-size: 1.5em; } } @media (min-width: 1000px) { html { font-size: 1.75em; } } @media (min-width: 1200px) { html { font-size: 2em; } } \[/code\]

Conforme o tamanho da tela aumenta, aumentamos o valor do `em` base na tag `<html>`. Isso causa um efeito cascata em todos os elementos filhos se estiverem escritos com `em`.

É muito bom usar `em` nos sites responsivos para ajustar, via CSS, o tamanho de todos os elementos proporcionalmente.

Mas `em` não é uma medida flexível nem mais acessível. Pelo menos não no sentido de outras como a porcentagem, que se adapta automaticamente. O `em` é fixo, só o valor que é calculado a partir de uma conta mais complicada.

## Bônus: o rem e outras medidas verdadeiramente flexíveis

O `rem` é uma medida nova parecida. A diferença é que a conta não leva em consideração todos os pais, mas apenas a raiz, o `<html>`. Isso quer dizer que mexer no `font-size` de algum elemento na página não vai refletir e atrapalhar outros. Só se mexermos no `<html>` mesmo. É útil para o caso do design responsivo que vimos antes.

Mas lembre que o `em` multiplicar pelo `font-size` do pai é uma coisa boa em muitas situações. Várias partes da página podem ser encaradas como pequenos componentes autocontidos. E mexer no pai do componente tem que afetar todos os filhos. O `em` é excelente pra isso, ao contrário do `rem`.

E, se você quiser uma medida verdadeiramente flexível e adaptável para fontes, a resposta está nas novas **viewport units**. Em especial, um código como `html { font-size: 2vw; }` vai fazer a fonte se adaptar automaticamente a todo tipo de resolução. Mas isso fica para outro artigo.

Se quiser saber mais sobre medidas flexíveis, boas práticas de CSS e também de design responsivo, não perca o [curso de Web da Caelum](https://www.caelum.com.br/curso-html-css-javascript/).

E você, gosta de usar em?
