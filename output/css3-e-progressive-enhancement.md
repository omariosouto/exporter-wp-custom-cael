---
title: "CSS3 e progressive enhancement"
date: "2010-05-06"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Desde o meu último [post sobre CSS3](https://blog.caelum.com.br/css3-e-o-futuro-da-web/ "CSS3 e o futuro da Web"), muita coisa tem acontecido no mundo dos browsers. A [guerra que Steve Jobs começou contra o Adobe Flash](http://www.apple.com/hotnews/thoughts-on-flash/ "Steve Jobs mostra as razões para não gostar de Flash") se intensificou e tem ganho muitos [reforços](http://www.appleinsider.com/articles/10/05/05/scribd_scrapping_flash_and_betting_the_company_on_html5.html "Scribd anuncia migração para HTML5"). O HTML5 e o CSS3 despontaram de vez como soluções, e só se [fala disso](http://news.dzone.com/articles/state-web-2010-css3-rise) na blogosfera. Os fabricantes de browsers têm [corrido](http://www.html5trends.com/browsers/chrome-5-beta-supports-more-html5-features/) bastante para suportar as novidades e a feliz surpresa é o incrível comprometimento com os novos padrões que a [Microsoft tem feito no IE9](http://blogs.msdn.com/ie/archive/2010/05/05/html5-and-same-markup-second-ie9-platform-preview-available-for-developers.aspx).

Mas, como vimos, o grande problema _hoje_ é, ainda, o suporte nos navegadores. Apenas as últimas versões dos navegadores mais modernos possuem suporte a CSS3 e HTML5 e, mesmo assim, de forma ainda incompleta e limitada. Isso quer dizer que ainda não podemos usar CSS3 em nossos sites? Podemos e a chave pra isso é **progressive ehancement** - ou _melhorias progressivas_. Esse e outros assuntos de CSS, HTML e JavaScript são tratados no novo curso [WD-43](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/) da Caelum.

### Progressive enhancement e graceful degradation

A ideia é fazer seu site funcionar em qualquer navegador, sem prejudicar os navegadores mais antigos e sem deixar de usar os novos recursos em navegadores novos. _Graceful degradation_ foi o nome da primeira técnica a pregar isso; o objetivo era montar seu site voltado aos navegadores modernos e fazer com que ele degradasse "graciosamente", removendo funcionalidades não suportadas. A abordagem mais recente, chamada **progressive enhancement** tem uma ideia parecida mas ao contrário: comece desenvolvendo as funcionalidades normalmente e vá acrescentando pequenas melhorias mesmo que só funcionem nos navegadores modernos.

Com CSS3, podemos usar _progressive enhancement_. Não é possível ainda ter um site que dependa hoje totalmente em CSS3. Mas é possível desenvolver seu site e acrescentar melhorias progressivamente usando CSS3.

### Montando uma página recheada de novidades do CSS3

Recentemente, reorganizamos a página dos cursos da Caelum e usamos vários recursos do CSS3 (veja a [página do WD-43](http://www.caelum.com.br/curso/wd43/)). Por exemplo, no fim da página, mostramos a ementa de cada treinamento. O conteúdo em si é simples: uma lista de tópicos usando listas com <ul> e <li>, o que funciona em qualquer navegador até os em modo texto ou em celulares simples. Mas, se você acessar usando um navegador com CSS3, verá uma melhoria interessante: para melhor organizar o conteúdo, usamos a nova funcionalidade de quebrar o conteúdo em colunas, a propriedade [column-count](http://www.quirksmode.org/css/multicolumn.html):

![Exemplo do column-count](https://blog.caelum.com.br/wp-content/uploads/2010/05/caelum-column-count-300x83.png "caelum-column-count")

Firefox e Webkit suportam o _column-count_ já há algum tempo, mas com auxílio das extensões proprietárias:

\[css\] #ementa { /\* multi-coluna com firefox, chrome, safari \*/ -moz-column-count: 2; -moz-column-gap: 1.5em; -moz-column-rule: none; -webkit-column-count: 2; -webkit-column-gap: 1.5em; -webkit-column-rule: none; /\* css3 \*/ column-count: 2; column-gap: 1.5em; column-rule: none; } \[/css\]

Na nossa implementação, ainda temos um JavaScript a mais que simula o _column-count_ em alguns browsers como o IE (mas não tão elegantemente quanto a versão CSS3).

### Botões CSS3 sem imagens com progressive enhancement

Na página dos cursos, temos também links para contato, download etc. Links são a coisa mais antiga da Web, funcionam em qualquer navegador dos últimos 20 anos. Mas que tal melhorarmos um pouco o link para ele se parecer mais com botões? Fundo degradé, bordas redondas, algumas sombras, uns efeitos aqui e ali e conseguimos um resultado interessante. E, com CSS3, todos esses efeitos são extremamente simples de se fazer sem a necessidade de nenhuma imagem. Veja um screenshot do botão ou acesse a [página do FJ-21](http://www.caelum.com.br/curso/fj-21-java-web/) para vê-lo no ar:

![](https://blog.caelum.com.br/wp-content/uploads/2010/05/caelum-botao-css3.png "caelum-botao-css3")

Mas e nos browsers sem CSS3? Podemos fazer diversas formas de melhorias progressivas para dar uma experiência interessante (mas diferente) ao usuário de qualquer navegador.

Degradés, por exemplo, são suportados pela propriedade _background-image_ com o valor [linear-gradient](https://developer.mozilla.org/en/CSS/-moz-linear-gradient), mas apenas no Webkit e no Firefox 3.6. No IE, podemos usar um [filtro proprietário](http://msdn.microsoft.com/en-us/library/ms532997(VS.85).aspx) e, em navegadores mais antigos como Firefox 3.0 e 3.5, podemos simular gradientes usando uma [técnica muito usada pelo Google](http://stopdesign.com/archive/2009/02/04/recreating-the-button.html) de criar faixas de cores sólidas.

Já bordas redondas são suportadas pela propriedade [border-radius](http://www.css3.info/preview/rounded-border/) do CSS3 e não funcionam no IE e em outros navegadores antigos (o IE9 irá suportar). Outras características, como [text-shadow](http://www.quirksmode.org/css/textshadow.html) e [box-shadow](http://www.css3.info/preview/box-shadow/), também possuem abordagens diferentes nos navegadores por aí, e as suportamos como uma melhoria a mais.

O resultado? Um botão elegante em cada browser, uns com mais recursos, outros com menos. Mas uma página usável em todos os lugares:

![](https://blog.caelum.com.br/wp-content/uploads/2010/05/screenshots.png "screenshots")

Isolei o exemplo dos [botões com CSS3 em uma página específica](http://sergiolopes.github.com/css3-experimentos/botoes-css3/index.html) que você pode acessar. Lá há exemplos de uso do botão, links pro HTML e CSS usados e screenshots em diversos browsers do mercado.

### Conclusão

Um site inteiro baseado em CSS3 e HTML5 é, na prática, ainda um sonho de um futuro distante. Mas as novidades já estão sendo implementadas nos navegadores e podemos usá-las já hoje! A boa prática é usar as técnicas de _[progressive enhancement](http://dev.opera.com/articles/view/progressive-enhancement-with-css-3-a-be/)_ para incrementar seu design em navegadores mais modernos.

Saber usar as novidades e escrever código HTML e CSS seguindo as boas práticas são desafios dos bons WebDesigners de hoje em dia e assunto do novo curso [WD-43](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/) da Caelum.
