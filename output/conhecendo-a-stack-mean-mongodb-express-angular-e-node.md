---
title: "Conhecendo a stack MEAN: MongoDB, Express, Angular e Node."
date: "2013-12-10"
author: "vitor.mattos"
authorEmail: "vitor.mattos@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nos últimos anos, talvez por culpa da revolução Ajax, o JavaScript deixou de ser uma toy language e ganhou muita força. Foi nessa expansão dos horizontes da linguagem que surgiu o Node, um ambiente de execução JavaScript baseado na engine V8 da Google. Agora temos JavaScript também no lado do servidor! Node.js segue o paradigma de programação orientada a eventos e non-blocking I/O, [resultando em ótima performance](https://www.paypal-engineering.com/2013/11/22/node-js-at-paypal/) para determinados cenários de aplicações real-time. Além do Node, um outro framework que vem ganhando tração é o AngularJS, da Google.

Em meio a essa expansão do JavaScript na web, foi criado o [MEAN stack](http://mean.io/):

- [**M**ongoDB](http://www.mongodb.org/) - Banco de dados orientado a documentos
- [**E**xpress](http://expressjs.com/) - Framework de desenvolvimento web para Node
- [**A**ngularJS](http://angularjs.org/) - Framework MVC para JavaScript
- [**N**ode.js](http://nodejs.org/) - Ambiente de execução JavaScript

[![nodejs-1024x768](https://blog.caelum.com.br/wp-content/uploads/2013/12/nodejs-1024x768-300x225.png)](https://blog.caelum.com.br/wp-content/uploads/2013/12/nodejs-1024x768.png)

Mas por que usar essa seleção de ferramentas?

De início, temos a vantagem de não precisar saber nenhuma linguagem além do JavaScript. O que também é uma grande vantagem quando queremos utilizar um banco de dados NoSql, como o MongoDB, já que estaremos trabalhando direto com objetos muito similares ao JSON. Essas características tornam o MEAN Stack ideal para rápida prototipação de software e desenvolvimento de aplicações escaláveis. Por ser usado apenas uma linguagem bem difundida, como o JavaScript, é adequado também para desenvolvedores que não tem muita experiência com o desenvolvimento backend.

Por onde começar?

Com o MongoDB, Node, npm (node package manager) e Express instalados, vamos criar um novo projeto usando o Express e instalando os módulos **mongodb** e **mongoose**. Usaremos o mongoose para trabalhar de forma simplificada com MongoDB. No exemplo abaixo, usaremos o template engine "ejs"

**express -e cadastro-de-contatos**

Dentro do diretório cadastro-de-contatos, altere as dependências no arquivo package.json da seguinte maneira:

\[code language="javascript"\] "dependencies": { "express": "3.4.3", "ejs": "\*", "mongodb": "~1.3.19", "mongoose": "~3.8.1" } \[/code\]

Agora, rode o comando **npm install** para baixar os módulos necessários

Primeiro, vamos iniciar o AngularJS:

\[code language="html"\] <html ng-app> \[/code\]

Agora, podemos criar o formulário para adicionar novos contato, no arquivo **views/index.ejs**, que invocará a função `adicionaContato`.

\[code language="html"\] <section ng-controller='ContatosController'> <form ng-submit='adicionaContato()'> <input type='text' placeholder='nome' ng-model='contato.nome' /> <input type='tel' placeholder='telefone' ng-model='contato.telefone' /> <input type='submit' value='salvar' /> </form> </section> \[/code\]

Repare que definimos que os campos de input estão vinculados a um objeto no controller, e o formulário chamará a função **adicionaContato** quando o submetermos. Vamos criar o `ContatosController.js`:

\[code language="javascript"\] function ContatosController($scope, $http) {

function Contato() { this.nome = ''; this.telefone = ''; }

$scope.contato = new Contato();

$scope.contatos = \[\];

$scope.adicionaContato = function() { $http.post('/contato', $scope.contato).success(function() { $scope.contatos.push($scope.contato); $scope.contato = new Contato(); }); } } \[/code\]

A função `adicionaContato` faz um post assíncrono para `/contato`, enviando o objeto contato no corpo da requisição. Mas essa rota ainda não existe! Ainda temos que defini-la no servidor, no  arquivo **app.js**:

\[code language="javascript"\] app.post('/contato', routes.adicionaContato); \[/code\]

Quando a requisição for feita, o contato será persistido no banco, mas ainda temos que criar a estrutura dos documentos de contato que serão gravados. Faremos isso utilizando o mongoose em nosso **routes/index.js**:

\[code language="javascript"\] var mongoose = require('mongoose'); mongoose.connect('mongodb://localhost/mean'); var Schema = mongoose.Schema;

var contatoSchema = new Schema({ nome: { type: String, required: true }, telefone: { type: String, required: true } }); \[/code\]

Ainda no **routes/index.js**, devemos criar a função que será chamada quando o post para **/contato** for feito:

\[code language="javascript"\] exports.adicionaContato = function(req, res) { var contato = new Contato(req.body); contato.save(function(error, contato) { if(error) res.send(500);

res.send(201); }); } \[/code\]

Pronto. Já estamos persistindo o contato no banco. Podemos testar nossa aplicação rodando o comando **node app.js** e abrindo a acessando a url **[http://localhost:3000/](http://localhost:3000/)**. Repare que não mapeamos o contato em momento algum, e todo o código escrito, tanto no cliente quanto no servidor, foi JavaScript.

Podemos agora fazer a listagem dos contatos. No **views/index.ejs**:

\[code language="html"\] <table class="table table-striped"> <tr> <th>Nome</th> <th>Telefone</th> </tr> <tr ng-repeat='contato in contatos'> <td>{{contato.nome}}</td> <td>{{contato.telefone}}</td> </tr> </table> \[/code\]

Vamos também fazer a busca dos contatos no controller do Angular **ContatosController.js**:

\[code language="javascript"\] function ContatosController($scope, $http) {

// Resto do código

$http.get('/contatos').success(function(retorno) { $scope.contatos = retorno.contatos; }); } \[/code\]

Estamos fazendo um get assíncrono para **/contatos**. Da mesma forma que fizemos com o post, temos que definir a rota no servidor, no arquivo **app.js**:

\[code language="javascript"\] app.get('/contatos', routes.listaContatos); \[/code\]

E no **routes/index.js**, faremos a busca no banco:

\[code language="javascript"\] exports.listaContatos = function(req, res) { Contato.find({}, function(error, contatos) { if(error) res.send(500);

res.json({ contatos: contatos }); }); } \[/code\]

Pronto! É JavaScript em todos os lados, que pode até confundi-lo, no início, de onde vai cada código!

Todo o código do artigo pode ser encontrado neste [repositório](https://github.com/vmattos/mean-stack).
