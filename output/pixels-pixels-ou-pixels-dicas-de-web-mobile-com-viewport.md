---
title: "Pixels, pixels ou pixels? Dicas de Web Mobile com viewport"
date: "2012-02-27"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Foi-se o tempo em que **pixel** significava apenas o menor ponto na tela. Bastava dizer que uma imagem tinha _200px_, e então ela ocuparia 200 pontos, ou seja, 25% de uma tela de tamanho padrão 800x600.

**Mas o mundo mobile mudou completamente o jogo** e, hoje, o conceito de pixel pode significar várias coisas.

(**Atualização:** escrevi mais detalhadamente sobre esses aspectos dos pixels diferentes e viewport, incluindo telas retina, no meu [livro A Web Mobile](http://www.casadocodigo.com.br/products/livro-web-mobile), publicado pela editora Casa do Código. Se você estuda design responsivo, sites mobile, e assuntos relacionados, vai gostar desse livro.)

### Os primeiros Smartphones

Era muito comum que os smartphones da Nokia lá pelos idos de 2007 tivessem uma resolução de 240x320 pixels, como um [N95](http://www.gsmarena.com/nokia_n95-1716.php).

Quando surgiu o [primeiro iPhone](http://www.gsmarena.com/apple_iphone-1827.php), sem teclado e só touch, a Apple decidiu explorar um tamanho maior de tela, **320x480 pixels**. Era o dobro dos pixels normalmente usados na época, com um tamanho físico mais ou menos também com o dobro do tamanho.

Esses valores representam o tamanho físico do aparelho, o número de **pixels físicos** existentes. Na prática, um iPhone conseguia exibir páginas com mais que 320 pixels de largura. O truque era trabalhar com a ideia de **zoom**.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/02/caelum-iphone-159x300.png "Caelum.com.br no iPhone")](https://blog.caelum.com.br/wp-content/uploads/2012/02/caelum-iphone.png)

Na imagem anterior, abrimos o [site da Caelum](http://www.caelum.com.br), que tem 960px de largura, em um iPhone de 320px. Repare como, apesar de menor, o Site está sendo renderizado corretamente.

Mas nosso HTML e CSS não foi codificado pensando em 320px, e sim em 960px. Quando colocamos a imagem do logotipo, por exemplo, nosso HTML diz `<img src=".." width="160" height="50">`. E, obviamente, o logo não está sendo renderizado a 160px, senão ocuparia metade da tela de 320px do iPhone. Se você medir, verá que o logo está sendo renderizado em mais ou menos 52px, ou 1/6 da tela do iPhone.

### CSS pixels e o layout viewport

Repare que usamos uma medida de pixels no HTML/CSS que difere do pixel real usado na tela. O navegador do iPhone, na verdade, se comporta **como se tivesse 980px de largura**, embora o aparelho tenha apenas 320px. Isso é feito para que o usuário possa ver páginas feitas para Desktop sem problemas.

Nossa página funciona como se tivéssemos 980px disponíveis. Quando escrevemos "245px" no CSS, estamos nos referindo a 1/4 dessa _tela imaginária de 980px_. Na hora de exibir, porém, os 980px serão encaixados nos 320px reais, aplicando um **zoom out**.

Essa tela imaginária de 980px é o que chamamos de **layout viewport**. É o tamanho com o qual trabalhamos no nosso HTML/CSS, sem nos preocuparmos com a renderização no aparelho. Repare que um pixel no _layout viewport_ tem outro significado do pixel físico do aparelho. É comum chamá-lo de **CSS pixel**.

### Zoom e o visual viewport

Mas navegar no celular nessa página gigante sem zoom é praticamente impossível. A grande diferença da navegação mobile com a Desktop é o frequente uso do **zoom** e o scroll em todas as direções.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/02/caelum-iphone-zoom-159x300.png "Dando zoom no iPhone em um pedaço da página da Caelum")](https://blog.caelum.com.br/wp-content/uploads/2012/02/caelum-iphone-zoom.png)

Na imagem acima, demos um zoom para ver mais detalhes. Repare que a o layout da página continua o mesmo. Um elemento de "245px" continua ocupando 1/4 do total do nosso _layout viewport_. A diferença é que, agora, só estamos visualizando uma parte do _layout viewport_; o restante está fora da tela, e precisaríamos fazer scroll para ver.

Isso nos leva para outro conceito importante: o **visual viewport**, que representa o tanto do _layout viewport_ que conseguimos visualizar no momento.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/02/viewports-194x300.png "Os diferentes viewports")](https://blog.caelum.com.br/wp-content/uploads/2012/02/viewports.png)

Geralmente não estamos interessados no tamanho do _visual viewport_. Lembre que os _CSS pixels_ são sempre relativos ao _layout viewport_.

### Sites mobile e a meta tag viewport

Abrir um site Desktop no celular é uma experiência pouco agradável. Frequentemente, vamos querer criar uma **página otimizada para mobile**, que não demande tanto zoom e já mostre o conteúdo em tamanho e formato interessantes para uma tela tão pequena.

Como fazer? Obviamente, não podemos deixar a página com layout fixo em, por exemplo, 960px. Podemos tentar um `width:100%` no elemento principal, pensando em se adaptar a diversos tamanhos de tela.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/02/width-100-porcento-159x300.png "Página simples com width 100%")](https://blog.caelum.com.br/wp-content/uploads/2012/02/width-100-porcento.png)

Nosso _layout viewport_ é considerado como 980px e o site é mostrado como se fosse de Desktop, com zoom mínimo e conteúdo praticamente ilegível. Que tal colocar `width:320px`, o tamanho real do dispositivo?

[![](https://blog.caelum.com.br/wp-content/uploads/2012/02/width-320px-159x300.png "Colocando width em 320px")](https://blog.caelum.com.br/wp-content/uploads/2012/02/width-320px.png)

O _layout viewport_ continua em 980px mas o conteúdo fica em 320px. O usuário precisa dar zoom para visualizar e, pior, a página fica com um imenso espaço em branco.

O que precisamos é uma forma de **redimensionar o layout viewport** para que ele seja mais adequado a tela pequena do mobile. A Apple introduziu uma **meta tag viewport** no iPhone que, depois, foi adotada em praticamente todas as plataformas móveis - Android, Opera, Windows Phone etc.

\[html\] <meta name="viewport" content="width=320"> \[/html\]

Isso indica ao navegador que o _layout viewport_ deve ser 320px. Agora, colocar `width:100%` vai significar 320px, deixando a visualização mais confortável.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/02/viewport-320-159x300.png "viewport-320")](https://blog.caelum.com.br/wp-content/uploads/2012/02/viewport-320.png)

### Viewport flexível com device-width

Deixar "320" fixo na nossa tag de viewport pode não ser uma boa ideia. Há diversos aparelhos diferentes no mercado, cada um com tamanho diferente. E mobile agora também inclui tablets, como o iPad, que tem largura de 800px.

É possível deixar a **meta tag viewport com tamanho flexível**, baseado no tamanho do aparelho. Basta usarmos:

\[html\] <meta name="viewport" content="width=device-width"> \[/html\]

Isso assumirá o valor, por exemplo, de 320px no iPhone e 800px no iPad. Outros aparelhos poderão assumir outros valores.

### Altíssimas resoluções

Antes de aparecerem os Androids de alta resolução e, depois, o iPhone 4, toda a história dos pixels se resumia a diferença entre os _CSS pixels_ e os _device pixels_. Isso porque um _device pixel_ no iPhone clássico significava um pixel físico na tela.

A retina display mudou isso. O [iPhone 4](http://www.gsmarena.com/apple_iphone_4-3275.php) passou a vir com resolução de **640x960** pixels, melhorando a renderização de textos e imagens. Outros celulares foram até além. O [Galaxy Nexus](http://www.gsmarena.com/samsung_galaxy_nexus-4219.php), por exemplo, tem resolução HD de **720x1280px**.

Como ficam nossas páginas mobile então que assumiam uma resolução bem menor? Com resolução tão alta quanto um Desktop, os celulares mais modernos vão renderizar as páginas bem pequenas, como um site Desktop? Nossas páginas continuam funcionando porque **esses dispositivos de alta resolução continuam reportando um `device-width` de 320px**, pra manter a compatibilidade.

A ideia de reportar um `device-width` diferente do tamanho de pixels físicos [surgiu no Android](http://developer.android.com/guide/practices/screens_support.html) e depois foi copiada pelo iOS e outras plataformas.

Dessa forma, conseguimos evoluir a resolução da tela com densidades de pixels maiores (dpi) sem afetar a forma como o usuário usa nosso Site mobile, que continua otimizado para telas pequenas.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/02/galaxynexus-300x266.png "Galaxy Nexus")](https://blog.caelum.com.br/wp-content/uploads/2012/02/galaxynexus.png)

### Os três pixels

Um pixel, pode então, representar três conceitos diferentes quando lidamos com mobile:

**Pixel físico**: número real de pixels na tela. Nos celulares modernos, é um número altíssimo, com ótima resolução, geralmente com densidade acima de 300 dpi.

**Device pixel**: é o número de pixels reportado pelo aparelho como sendo seu tamanho. É pensado pra ser um valor que ofereça conforto visual para o usuário olhando para aquele tamanho de tela. É comum que esse valor seja 320px em celulares, copiado do iPhone original.

**CSS pixel:** é o que usamos no HTML/CSS como **px**, representando um tamanho dentro do _layout viewport_. Quando colocamos a meta tag viewport com valor `width=device-width`, estamos dizendo que nosso _CSS pixel_ é igual a um _Device pixel_.

Hoje, no Desktop, esses três pixels são equivalentes\*\*. Mas, em breve, teremos que lidar com esse tipo de diferença também no Desktop com a chegada das **telas de alta densidade** também aos computadores.

### Lidando com zoom

Mesmo otimizando nossa página para 320px, o usuário ainda pode dar zoom na página. Em alguns cenários, pode ser interessante **desabilitar o zoom**, o que pode ser feito na tag viewport com `user-scalable`:

\[html\] <meta name="viewport" content="width=device-width, user-scalable=no"> \[/html\]

De maneira geral, é interessante deixar o usuário dar zoom caso queira, já que este é um gesto comum ao usar a Web no celular. Podemos, porém, **controlar os níveis de zoom** com as propriedades `minimum-scale` e `maximum-scale`:

\[html\] <meta name="viewport" content="width=device-width, minimum-scale=0.5, maximum-scale=4"> \[/html\]

O código acima indica que o usuário pode aumentar até 4x a página e diminuir até pela metade.

Podemos controlar também o **nível padrão de zoom** quando a página é aberta, com `initial-scale`:

\[html\] <meta name="viewport" content="width=device-width, initial-scale=1.0"> \[/html\]

O valor 1.0 é muito comum quando trabalhamos com device-width e significa o zoom padrão. Se tivermos uma página Desktop não otimizada pro viewport de mobile, podemos usar essa propriedade para controlar o zoom inicial (lembre que o inicial é mostrar todo o _layout viewport_ de 980px, o que pode não ser interessante).

Por fim, é importante citar um **bug do iOS** que afeta o zoom e o viewport quando rotacionamos o dispositivo em uma página com `width=device-width` que permita zoom. Se você abre a página no modo retrato, ele vai assumir o scale como 1.0, deixando o _visual viewport_ igual ao _layout viewport_. Ao rotacioná-lo para modo paisagem, o dispositivo mantém o _visual viewport_ no valor antigo, mas aumentando o _layout viewport_. Na prática, a página dá zoom automático e o lado direito da página não fica visível. O usuário, que não deu zoom, precisa diminuir o zoom para ver tudo.

É um bug famoso que acontece só no Mobile Safari do iOS, não existindo no Android e outras plataformas. A solução mais direta é _desabilitar o zoom por completo_, algo que é feito em diversos sites mobile por causa desse bug. Mas **não** é a solução ideal, já que poder dar zoom é uma feature que interessa ao usuário mobile. Existem [alguns](https://github.com/h5bp/mobile-boilerplate/blob/master/js/helper.js) [hacks](https://github.com/scottjehl/iOS-Orientationchange-Fix) para tentar resolver esse problema no iOS.

### Conclusão

Trabalhar com telas diferentes é um grande desafio. O uso da meta tag `viewport` procura facilitar a padronização das páginas nos mais diversos tamanhos de telas e densidades de pixels. Compreender os diferentes significados de viewports e pixels é essencial para se desenvolver para mobile.

E, usando ainda **media queries**, podemos criar páginas que se adaptem facilmente a diversos dispositivos.

O [curso WD-43 da Caelum](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/), de [desenvolvimento Web](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/), trata também de tópicos de Web Mobile. Mostramos o uso do viewport e media queries para criação de uma página responsiva. Além disso, meu [livro A Web Mobile](http://www.casadocodigo.com.br/products/livro-web-mobile), aprofunda em diversos assuntos de design responsivo e aspectos técnicos de sites para dispositivos móveis.

### Referências

- [A tale of two viewports — part one](http://www.quirksmode.org/mobile/viewports.html), por Peter-Paul Koch
- [A tale of two viewports — part two](http://www.quirksmode.org/mobile/viewports2.html), por Peter-Paul Koch
- [A pixel is not a pixel is not a pixel](http://www.quirksmode.org/blog/archives/2010/04/a_pixel_is_not.html), por Peter-Paul Koch
- [Preparing Your Web Content for iPad](https://developer.apple.com/library/ios/#technotes/tn2010/tn2262/_index.html), Apple Tech Note
- [An introduction to meta viewport and @viewport](http://dev.opera.com/articles/view/an-introduction-to-meta-viewport-and-viewport/), por Andreas Bovens, Opera dev
- [Configuring the Viewport](https://developer.apple.com/library/ios/#documentation/AppleApplications/Reference/SafariWebContent/UsingtheViewport/UsingtheViewport.html), Safari Web Content Guide

\*\* No Desktop, quando damos zoom numa página, também temos a complicação dos viewports diferentes e a diferença entre CSS pixels e device pixels. Mas, na prática, todo mundo ignora e assume zoom de 100%, onde os CSS pixels são iguais aos device pixels.
