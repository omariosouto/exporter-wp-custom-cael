---
title: "Navegando por uma Single Page Application Angular"
date: "2015-02-11"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Depois da ótima repercussão do post sobre as perspectivas e análise do cenário atual do [Angular](https://blog.caelum.com.br/como-anda-o-angular-js-devo-embarcar-nessa/), vamos partir para algo mais técnico. Na série de artigos vamos construir uma aplicação fazendo um CRUD básico em Angular e neste vamos abordar a parte de navegação. Nos textos, vou seguir a padronização definida pelo [John Papa](https://github.com/johnpapa/angularjs-styleguide).

Primeiro vamos começar pela navegação padrão do Angular, usando a api [ngRoute](https://docs.angularjs.org/api/ngRoute/service/$route). Depois de fazer a configuração básica do projeto, temos que importar o JavaScript de navegação necessário.

\[code language="html"\] <script type="text/javascript" src="vendor/angular/angular-route-1.3.0-min.js"> \[/code\]

Não é escopo desse artigo, mas você pode usar uma [biblioteca JS para fazer este trabalho](https://github.com/angular-ui/ui-router/issues/479#issuecomment-26232775) com o [Require.js](http://requirejs.org) e o [Head.js](http://headjs.com).

Além dele, temos que importar o JavaScript que contém as informações básicas da aplicação, o Angular denomina como "_root module_". Nele temos que importar os módulos que fazem parte da nossa aplicação, um deles será o ngRoute

\[code language="javascript"\] var caelumapp = angular.module('caelumapp', \[ 'ngRoute', 'app.login'\]); \[/code\]

Ainda neste arquivo, vamos configurar as rotas que estarão disponíveis na aplicação. As rotas são definidas usando o [$routeProvider](https://docs.angularjs.org/api/ngRoute/provider/$routeProvider), sendo que cada rota é declarada no método when(path, object).O primeiro parâmetro recebe qual será o nome URL e o segundo é um objeto JSON com uma série de configurações básicas.

\[code language="javascript"\] caelumapp.config(\['$routeProvider','$locationProvider', function($routeProvider, $locationProvider) { $routeProvider .when('/login', { templateUrl: "app/login/paginas/login.html", controller : "LoginController", controllerAs: "vm" }) .when('/usuario-cadastro', { templateUrl: "app/usuario/paginas/usuario-cadastro.html", controller: 'UsuarioController', controllerAs: 'vm' }) .otherwise({ redirectTo : '/login' }); } \]); \[/code\]

Por fim, definimos qual será a parte do nosso template que terá o conteúdo dinâmico, fazemos isso atráves da  diretiva ng-view, além de colocar links para navegação

\[code language="html"\]<nav> <ul> <li><a href="#/login">Login</a></li> <li><a href="#/usuario-cadastro">Cadastro de Usuários</a></li> </ul> </nav>

<section> <article ng-view></article> </section> \[/code\]

O navegador básico do angular [possui algumas limitações](https://docs.angularjs.org/tutorial/step_07), como não lidar com _nested e multiple views_, além de ser baseado essencialmente nas URL's, portanto, caso queira modificar alguma URL, terá que fazer isso em todos os arquivos que fazem referência pra ela.

Pensando nisso, foi criado o [AngularUI-router](https://github.com/angular-ui/ui-router),  um framework para o Angular que trabalha baseado em uma máquina de estados. Basicamente, o programador não fica preso às url's, mas sim aos estados. Não obstante, ele resolve problemas de múltiplas _views_ em uma única página, ou seja, várias _ng-views_ (no caso do framework seria _ui-view_) em um template, além das _nested views_ (de uma uma view, tem outra view, que tem outra view e por ai vai). 

Aqui tem uma [apresentação bem divertida demonstrando](http://slides.com/timkindberg/ui-router#/3) as principais diferenças entre as duas soluções e uma [aplicação de exemplo para entender](http://angular-ui.github.io/ui-router/sample/#/contacts/42/item/a) o funcionamento do [framework](https://egghead.io/lessons/angularjs-ui-router-activating-states).

Refatorando nosso código para Angular-ui-Router temos que fazer alguns ajustes básicos, como importar o JavaScript do componente, mudar as injeções no JavaScript responsável pela navegação ( agora injeção do $stateProvider e $urlRouterProvider), além de trocar os links para _ui-sref_ e conteúdo dinâmico para _ui-view._

\[code language="html"\]

<script type="text/javascript" src="vendor/angular-ui-router/angular-ui-router.min.js"></script>

<section> <header>Opções do Sistema</header> <nav> <ul> <li><a ui-sref="login">Login</a></li> <li><a ui-sref="cadastroDeUsuario">Cadastro de Usuários</a></li> <li><a ui-sref="pesquisaDeUsuario({id:vm.idUsuario})">Pesquisa de Usuários</a></li>

</ul> </nav> </section> <section> <article ui-view></article> </section> \[/code\]

 

Perceba que agora os links estão ligados aos nomes dos estados, e a sua url pode ser facilmente mudada somente no arquivo de configuração javascript.

\[code language="javascript"\]

var caelumapp = angular.module('caelumapp', \[ 'ui.router', 'app.login'\]);

caelumapp.config(\['$stateProvider', '$urlRouterProvider', '$httpProvider', function($stateProvider, $urlRouterProvider) { $urlRouterProvider.otherwise("/login"); $stateProvider .state('login', { url: "/login", templateUrl: "app/login/paginas/login.html", controller : "LoginController", controllerAs: "vm" }) .state('cadastroDeUsuario', { url: "/cadastro/usuario", templateUrl: "app/usuario/paginas/usuario-cadastro.html", controller: 'UsuarioController', controllerAs: 'vm' }) .state('pesquisaDeUsuario', { url: "/usuarios/:id", templateUrl: "app/usuario/paginas/usuario-detalhe.html", controller: 'UsuarioDetalheController', controllerAs: 'vm' }); } \]);

\[/code\]

Um caso interessante de navegação é passar um parâmetro por ID, por exemplo acessar a url http://caelum.com.br/usuarios/17. Perceba que no JavaScript de configuração o state pesquisaDeUsuario recebe o parâmetro id na URL, logo o ui-sref ficaria assim:

\[code language="html"\]

<li><a ui-sref="pesquisaDeUsuario({id:vm.idUsuario})">Pesquisa de Usuários</a></li>

\[/code\]

vm.idUsuario é uma varíavel que foi definida no controller. Ela poderia ser preenchida em um input

\[code language="html"\]

<input type="text" ng-model="vm.idUsuario">

\[/code\]

A formas básicas de navegação são com _$state.go_ e _ui-sref. A_ segunda opção é melhor para links HTML, pois permite o link ser copiado, já a primeira é ideal para lógicas internas de JavaScript, quando queremos redirecionar o usuário para a página inicial após o login ser realizado com sucesso por exemplo.

No próximo post falaremos sobre como implementar o cadastro de usuários. Operações GET/POST/PUT/DELETE. Não perca a chance de estudar mais sobre o [framework pela plataforma de ensino online Alura](http://www.alura.com.br/cursos-online-front-end) ou no curso [presencial](https://www.caelum.com.br/curso-javascript-jquery/).

E se quiser saber mais sobre o desenvolvimento Mobile, Front-end, Micro Services, APIs e outros não deixe de visitar a [Mobile Conf](http://www.mobileconf.com.br/ "Mobile Conf 2015"), dia 30/05/2015 no Rio De Janeiro!
