---
title: "Não use jQuery no seu site mobile: conheça o Zepto.JS"
date: "2012-07-31"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

_Já foi dito que [boa performance é a melhor funcionalidade que seu site mobile pode oferecer](http://sergiolopes.org/a-funcionalidade-mobile-mais-importante/). Ajustar a interface com [viewport](https://blog.caelum.com.br/pixels-pixels-ou-pixels-dicas-de-web-mobile-com-viewport/) e [media queries](https://blog.caelum.com.br/flexibilidade-em-paginas-para-dispositivos-moveis-com-media-queries/) é interessante, mas nada adiante se a página for pesada e tornar-se inutilizável._

Falamos de [otimizações web](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/) aqui no blog há bastante tempo. Mas em mobile a figura é outra: dispositivos mais lentos, menos capazes, redes móveis lentas, com alta latência, navegadores com poucas atualizações e muitos outros desafios.

E, se você se interessa por desesenvolvimento web mobile, não deixe de conferir meu livro [A Web Mobile](http://www.casadocodigo.com.br/products/livro-web-mobile) pela editora Casa do Código e o [curso de front-end da Caelum](http://www.caelum.com.br/curso-html-css-javascript/), onde mobile é um dos tópicos tratados.

## Esqueça o jQuery

[O jQuery é onipresente](https://blog.caelum.com.br/boas-praticas-com-javascript-e-jquery-codigo-nao-obstrusivo/). Há milhares de excelentes plugins pra ele. Componentes ricos, fáceis de usar e lindos. **Mas passe longe do jQuery em sites mobile.**

São 32 KB de dados gzipados para se transferir numa rede potencialmente ruim como 3G. E quando os dados chegam, ocupam 95 KB sem gzip (mesmo minificado). E tudo isso tem que ser lido e parseado antes mesmo de se pensar em executar seu código. [As estatísticas de carregamento do jQuery em dispositivos móveis são assustadoras](http://calendar.perfplanet.com/2011/lazy-evaluation-of-commonjs-modules/). Num iPad 2 topo de linha, só o parsing e `eval` do jQuery demora **5x mais que no Desktop**. Um iPhone 3, que, apesar de antigo, é melhor que a maioria dos celulares que as pessoas têm em seu bolso, demora incríveis **850ms**. E mesmo um iPhone 4 gasta uma eternidade se comparado ao Desktop - **320ms vs. 35ms**.

**Grande, lento e, pior, com um monte de código desnecessário.** Muitas das funcionalidades do jQuery já são implementadas nos navegadores mobile do iOS, Android, Windows Phone etc. A busca por seletores pode ser feita com [querySelector](http://caniuse.com/queryselector), [animações, com CSS3](http://caniuse.com/css-transitions) etc.

## Conheça o Zepto.JS!

![](https://blog.caelum.com.br/wp-content/uploads/2012/06/logo-1-300x71.png "Logo Zepto JS")

Se livrar do jQuery não é uma decisão fácil. Sintaxe simples e familiar. Vários mimos já prontos. Facilidade de achar plugins. E se pudermos ter tudo isso num framework leve e otimizado pra mobile?

O [Zepto.JS](http://zeptojs.com/) é um framework JavaScript pequeno (8 KB gzipped, 24 KB no final) com **API compatível com jQuery**. Eles pegaram as [principais funcionalidades](http://zeptojs.com/#core) do irmão maior e reimplementaram de forma mais simples e focada em navegadores modernos, em especial os dos smartphones.

Ou seja, **você já sabe usar o Zepto.JS**! Quer adicionar um evento de clique? `$('button').click(...)`. Quer mudar uma propriedade CSS? `$('div').css('color', 'red')`. Ajax? `$.ajax(...)`. E muito mais!

Óbvio que há um preço. Primeiro, nem todas as funcionalidades do jQuery estão lá. [Seletores estendidos](http://api.jquery.com/category/selectors/), por exemplo, não existem, já que o Zepto usa a API de seletores do próprio navegador. Coisas mais avançadas tipo [Deferred](http://api.jquery.com/category/deferred-object/), também não. E nem sonhe em tentar rodar o Zepto no IE, claro. Há [suporte](http://zeptojs.com/#platforms) pra iOS, Android, webOS, BlackBerry, Silk, Opera, Firefox e Chrome no mobile; além de suporte a Safari, Chrome, Firefox e Opera no Desktop.

## Eventos de touch

Uma boa adição específica do Zepto é o suporte a eventos de touch. Você pode escutar **tap**, **double tap**, **long tap** e **swipe** (embora não haja suporte pro pinch ainda). O código é familiar:

\[code language="javascript"\] $('#elemento').longTap(function() { alert('Você pressionou o elemento!'); }); \[/code\]

Ou ainda, para uma funcionalidade de swipe para remover:

\[code language="javascript"\] $('#elemento').swipeRight(function() { $(this).remove(); }); \[/code\]

Veja [alguns testes simples](http://sergiolopes.github.com/zeptojs-demo/exemplos.html) em seu smartphone. [O código fonte está todo no Github](https://github.com/sergiolopes/zeptojs-demo).

## E outros frameworks mobile?

Nem só de jQuery é feito o mundo dos frameworks JavaScript. Há vários inclusive _focados em mobile!_ O famoso [jQuery Mobile](http://jquerymobile.com/) é um plugin cheio de componentes para mobile, com temas interessantes e muitas funções. Há ainda o [jqTouch](http://jqtouch.com/) e o fantástico [Sencha Touch](http://www.sencha.com/products/touch).

**Não use nenhum deles se você vai fazer um site mobile.** O Sencha Touch, outro poderoso framework JavaScript com foco mobile, atinge inacreditáveis 580 KB em sua versão completa, e o jQuery Mobile, incríveis 91 KB, além dos 95 KB do jQuery em si. Esses frameworks de componentes são indicados para **mobile apps** construídas na Web, em especial se você for empacotá-las, usando algo como o [PhoneGap](http://phonegap.com/). Pra sites, são um tiro no pé. Obviamente eles podem melhorar muito em próximas releases, mas há muito a ser enxugado para serem candidatos a um site web mobile.

## Esqueça o Zepto.JS também!

No fim, como a justificativa pra substituirmos o jQuery pelo Zepto.JS é deixar a página mais leve e se livrar de peso desnecessário, pense se não vale a pena **se livrar do Zepto.JS também!**

O cache do iPhone, por exemplo, não guarda nenhum componente acima de 15 KB, e isso sem gzip ([fonte](http://www.phpied.com/iphone-caching/)). O Zepto.JS tem 24 KB na sua versão atual já minificada (o jQuery tem 95 KB). Se der, prefira JavaScript puro e talvez alguns [microframeworks](http://microjs.com/) pra complementar - o excelente [hammer.js](http://eightmedia.github.com/hammer.js/), por exemplo, adiciona eventos touch por apenas 5 KB (2KB gzipped).

Qual é a sua estratégia nos sites mobile?
