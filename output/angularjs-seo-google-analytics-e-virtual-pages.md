---
title: "AngularJS: SEO, Google Analytics e Virtual Pages"
date: "2015-02-25"
author: "flavio.almeida"
authorEmail: "flavio.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

## Single Page Application e SEO

Há uma polêmica na utilização do [AngularJS](https://angularjs.org/) e outras soluções de SPA (Single Page Application) no que diz respeito a Searching Engine Optimizations (SEO). Tomarei como base o AngularJS. Nele, seus templates (parciais) e seus dados são processados no lado do cliente, inviabilizando assim a indexação do conteúdo resultante por search engines (buscadores) da web como o Google. Uma solução é pré-processar os templates antes de serem indexados. Para esta tarefa, normalmente há a presença de um browser headless como o [PhantomJS](http://phantomjs.org/) que acessa a view e entrega seu resultado para o search engine, ou seja, é necessário uma infraestrutura especial para lidar com este problema.

Com a popularização de SPA, o próprio [Google passou a processar o script de nossas páginas durante o processo de indexação](http://googlewebmastercentral.blogspot.com.es/2014/05/understanding-web-pages-better.html). Excelente, não? Porém, mesmo com a mudança na política de indexação pelo Google é extremamente recomendado verificar o resultado através do [Google Web Master](http://www.google.com.br/webmasters/). Na opção _Rastreamento_, há a opção _Buscas como Google_ que permite visualizarmos nossa aplicação sob o ponto de vista do robô da Google, responsável pela indexação de nossas páginas. Problema resolvido? Depende! Se para você é suficiente ter o Google como único buscador, sim, mas se você quiser dar a chance para outros buscadores talvez a primeira solução seja a melhor.

## Google Analytics e SPA

Outra questão é o uso de ferramentas como o [Google Analytics](http://www.google.com/analytics/). Como a página carrega apenas uma vez e seu miolo é trocado através de um sistema de rotas (uso de Ajax), o Analytics não saberá que deve considerar cada view parcial uma página, isto é, qualquer interação nas parciais será computada para a view principal, comprometendo assim a coleta de informações (este problema afeta também as estatísticas do mapa de calor de sua aplicação). Este problema pode ser solucionado através de páginas virtuais (Virtual Pages).

## Virtual Pages

Podemos sinalizar para o script do Google Analytics que estamos virtualmente trocando de página quando uma view parcial for acessada. Fazemos isso passando para o Analytics o nome da parcial que estamos acessando. Porém, em que lugar e quando executaremos este código?

No ecossistema do AngularJS, podemos adicionar um código que será executado toda vez que uma rota no lado do cliente for processada. É neste ponto que podemos interagir com o script do Google Analytics sinalizando para ele que estamos acessando uma nova página, neste caso virtual, já que em SPA a página principal nunca recarrega. Normalmente adotamos com página virtual o próprio nome da view parcial que estamos acessando:

\[code language="javascript"\] angular.module('seuModulo', \['SuasDependencias'\]) .config(function($routeProvider) { // rotas da sua aplicação }).run(function ($rootScope) { $rootScope.$on("$routeChangeStart",function(event, next, current){

if(next.templateUrl) { // interagindo com o Analytics através do objeto global ga ga('send', 'pageview', { page: next.templateUrl }); } }); }); \[/code\]

Se você ainda preferir, pode isolar esse código em um módulo em separado:

\[code language="javascript"\] angular.module('virtualPage', \[\]) .run(function ($rootScope) { $rootScope.$on("$routeChangeStart",function(event, next, current){ if(next.templateUrl) { ga('send', 'pageview', { page: next.templateUrl }); } }); }); \[/code\]

E importá-lo no módulo principal da sua aplicação:

\[code language="javascript"\] angular.module('seuModulo', \['virtualPage'\]) .config(function($routeProvider) { // rotas da sua aplicação }) \[/code\]

## Conclusão

A popularidade de SPA fez com que desenvolvedores descobrissem formas de contornar problemas de SEO, inclusive o próprio Google passou a indexar este tipo de aplicação, o que acabou favorecendo seu framework AngularJS e todos aqueles na estrutura SPA. Escolher entre criar uma aplicação tradicional e SPA não é uma escolha trivial, requer que o desenvolvedor conheça, além dos requisitos do negócio, os prós e os contras da abordagem escolhida.

Se você quer saber mais de JavaScript e conhecer o AngularJS, a Caelum possui o [Curso Programação front-end com JavaScript e jQuery](https://www.caelum.com.br/curso-javascript-jquery/). Inclusive, se você se interessa por SPA, mais notadamente o AngularJS, lancei recentemente um [livro](http://www.casadocodigo.com.br/products/livro-mean) sobre MongoDB, Express, Angular e Node pela [Casa do Código](http://www.casadocodigo.com.br/products/livro-mean). Bom estudo!

E se quiser saber mais sobre o desenvolvimento Mobile, Micro Services, APIs e outros não deixe de visitar a [Mobile Conf](http://www.mobileconf.com.br/ "Mobile Conf 2015"), dia 30/05/2015 no Rio De Janeiro!
