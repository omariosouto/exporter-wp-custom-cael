---
title: "Angular 2: o fim do two-way data binding?"
date: "2016-04-01"
author: "flavio.almeida"
authorEmail: "flavio.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em 2009 a Google tornou open source seu framework MVC client-side batizado Angular. Sem dúvidas, uma de suas características que o tornou tão popular foi a **associação de dados bidirecional descomplicada**, o famoso **two-way data binding**.

## Two-way data binding

No two-way data binding, alterações na view são refletidas na fonte de dados e atualizações na fonte refletem na view sem a necessidade de manipulação explícita do DOM. Vejamos um exemplo:

\[code language="javascript"\] // controller angular .module('app') .controller('PalestranteController', function($scope) { $scope.palestrante = {nome: 'Flávio', email: 'N/A'};

$scope.grava = function() { // envia os dados do palestrante para uma API $scope.palestrante = {}; // limpa o formulário } }); \[/code\]

\[code language="html"\] <!-- template --> <form ng-submit="grava()"> <input ng-model="palestrante.nome" placeholder="Nome"> <input ng-model="palestrante.email" placeholder="E-mail"> <button type="submit">Gravar</button> </form> \[/code\]

No exemplo anterior, realizamos o _two-way data binding_ através da diretiva `ng-model`. A cada interação com os elementos `input`, os dados de entrada são enviados da view para a fonte de dados `$scope.palestrante`.

Quando clicamos no botão "Gravar", os dados são enviados para uma API e logo em seguida atribuímos um objeto `{}` na propriedade `$scope.palestrante` para limparmos o formulário. Nesse momento, os dados fluirão da fonte de dados para a view.

Há outros recursos não menos importantes como injeção de dependências e um sistema de módulos próprios, mas talvez o _two-way data binding_ seja o que mais se destacou.

## Onde está o two-way data binding no Angular 2?

Em outubro de 2014 foi anunciado o desenvolvimento da versão 2.0 do framework da Google. A nova versão gerou muita polêmica por ser muito diferente da anterior, inclusive por **não oferecer mais o two-way data binding**, pelo menos não como o conhecemos no Angular 1.X.

Vejamos o exemplo anterior, agora em Angular 2:

\[code language="javascript"\] // o componente

import {Component} from 'angular2/core';

@Component({ selector: 'cadastro', templateUrl: 'cadastro.html' }) export class Cadastro {

private palestrante: Object = {nome: 'Flávio', email: 'N/A'};

public grava():void { // envia os dados do palestrante para uma API this.palestrante = {}; // limpa o formulário } } \[/code\]

\[code language="html"\] <!-- template do componente --> <form ng-submit="grava()"> <input \[value\]="palestrante.nome" placeholder="Nome"> <input \[value\]="palestrante.email" placeholder="E-mail"> <button type="submit">Gravar</button> </form> \[/code\]

Veja que o atributo `value` está entre **colchetes**, a nova sintaxe de associação de dados unidirecional que flui da fonte de dados para a view. Quando o componente é renderizado, ele exibe "Flávio" e "N/A" em seus respectivos input's. Contudo qualquer interação do usuário com os campos não se propagará para a fonte de dados. E agora?

Angular 2 possui uma associação, também unidirecional, mas que flui da view para a fonte de dados que é a **associação de eventos**, ou **event binding** no inglês.

## Implementando two-way data binding no Angular 2

Podemos conseguir algo semelhante ao two-way data binding combinando os dois tipos de associação:

\[code language="html"\] <!-- template --> <form ng-submit="grava()"> <input (input)="palestrante.nome = $event.target.value" \[value\]="palestrante.nome" placeholder="Nome">

<input (input)="palestrante.email = $event.target.value" \[value\]="palestrante.email" placeholder="E-mail">

<button type="submit">Gravar</button> </form> \[/code\]

Quando o evento `input` é disparado, `$event.target` representa quem disparou o evento. Sendo um elemento do DOM, podemos acessar seu valor com `$event.target.value`. É este valor que é atualizado na propriedade `palestrante` do nosso componente.

## NgModel está de volta, mas não como você o conhecia!

Contudo, a equipe do Angular 2 criou uma diretiva que funciona como uma espécie de atalho para esses dois tipos de associação, a `ngModel`:

\[code language="html"\] <!-- template --> <form ng-submit="grava()"> <input \[(ngModel)\]="palestrante.nome" placeholder="Nome">

<input \[(ngModel)\]="palestrante.email" placeholder="E-mail">

<button type="submit">Gravar</button> </form> \[/code\]

Veja que a diretiva está envolvida por `[()]`, ou seja, temos os dois tipos de associações unidirecionais ao mesmo tempo, mas com direções opostas no fluxo de atualização.

## Conclusão

Apesar de uma sintaxe e uma implementação um tanto diferente (data binding + event binding), é possível conseguir uma resultado semelhante ao _two-way data binding_ em Angular 2.

A Caelum e o Alura possuem treinamentos de Angular que você pode conferir [aqui](https://www.caelum.com.br/curso-angularjs/) e [aqui](https://www.alura.com.br/curso-online-angularjs-mvc)! Inclusive a Casa do Código possui um [livro de MEAN](https://www.casadocodigo.com.br/products/livro-mean) que aborda também o assunto e no caso, com Angular 1.

## Twitter: @flaviohalmeida
