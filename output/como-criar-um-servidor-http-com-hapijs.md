---
title: "Como criar um servidor HTTP com HapiJS"
date: "2017-04-18"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Esse é o terceiro post que estou fazendo sobre JavaScript utilizando o NodeJS como plataforma. No primeiro aprendemos a [criar um servidor HTTP sem qualquer framework](https://blog.caelum.com.br/como-criar-um-servidor-http-com-nodejs/) e no segundo vimos [como criar o servidor HTTP com ExpressJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-expressjs/). Agora veremos como criar o servidor com o HapiJS, um concorrente do ExpressJS, que gostei de usar, acho que vou adicionar um apêndice com coisas do HapiJS no [curso da Caelum de NodeJS](https://www.caelum.com.br/curso-nodejs-express). :-)

## Rotas que criaremos com HapiJS

Nesse post nos vamos criar um pequeno projeto com 3 URLs:

1. Nossa **Home** que terá apenas um texto dizendo **Hello!** -- http://localhost:3000/ (GET)
2. Um formulário de contato com dois campos -- http://localhost:3000/contato (GET)
3. Por último uma rota com o mesmo path da anterior mas que responda ao método POST -- http://localhost:3000/contato (POST)

## Criando um servidor web com HapiJS

Para conseguirmos responder às 3 rotas que vimos acima, primeiro precisamos preparar o campo criando um servidor web. Para isso, criaremos um arquivo **server.js** que ficará dentro de uma pasta chamada **app**. Por favor, crie a pasta app no Desktop (Área de trabalho).

Dentro do nosso arquivo **server.js** faremos o código responsável por criar o nosso servidor HTTP com HapiJS:

\[code language="javascript"\] const Hapi = require('hapi') const server = new Hapi.Server() const config = { port : 3000, host: 'localhost' }

server.connection(config)

server.start((err) => { if (err) { throw err }

console.log(\`Server rodando em: ${server.info.uri}\`); console.log('Para derrubar o servidor: ctrl + c'); }) \[/code\]

Com o código finalizado e salvo, podemos pedir para o NodeJS executá-lo. Abra o terminal e execute as seguintes linhas:

\[code language="shell"\] ~$ cd ~/Desktop/app ~/Desktop/app$ node server.js \[/code\]

Infelizmente após rodarmos o segundo comando receberemos uma mensagem de erro :-(

\[code language="shell"\] module.js:471 throw err; ^

Error: Cannot find module 'hapi' at Function.Module.\_resolveFilename (module.js:469:15) at Function.Module.\_load (module.js:417:25) at Module.require (module.js:497:17) at require (internal/module.js:20:19) at Object.<anonymous> (/Users/marcobrunobr/gh/posts/teste-hapijs/server.js:1:76) at Module.\_compile (module.js:570:32) at Object.Module.\_extensions..js (module.js:579:10) at Module.load (module.js:487:32) at tryModuleLoad (module.js:446:12) at Function.Module.\_load (module.js:438:3) \[/code\] **Obs:** Talvez seja necessário você utilizar o scroll horizontal para ver toda a mensagem de erro.

Mesmo as mensagens de erro de JavaScript executados do lado do servidor não são amigáveis. Felizmente desta vez temos uma boa pista do que fizemos de errado:

\[code language="shell"\] Error: Cannot find module 'hapi \[/code\]

## Definindo o HapiJS como dependência do projeto

É isso aí, nós não instalamos e definimos o hapi como dependência do nosso projeto. Bora resolver esse problema. Primeiro precisamos criar um arquivo chamado **package.json** que tem a reponsabilidade de cuidar das informações do nosso projeto e suas dependências. Para criar o nosso package.json vamos rodar o comando abaixo dentro do diretório do projeto no terminal:

\[code language="shell"\] ~/Desktop/app$ npm init \[/code\]

Após rodar esse comando teremos que responder à algumas perguntas como (_name, version, description, entry point, test command, git repository, keywords, author and license_) referente ao projeto. Após respondermos estas perguntas, teremos o package.json dentro da pasta do nosso projeto. O conteúdo do **package.json** deverá ser algo parecido com isso:

\[code language="javascript"\] { "name": "app", "version": "1.0.0", "description": "", "main": "server.js", "scripts": { "test": "echo \\"Error: no test specified\\" && exit 1", "start": "node server.js" }, "author": "Marco Bruno", "license": "ISC" } \[/code\]

Pronto! Com o package.json criado podemos instalar o **HapiJS** e defini-lo como dependência do nosso projeto. Para fazemos isso, é bem simples, basta executarmos a seguinte linha no terminal:

\[code language="shell"\] ~/Desktop/app$ npm install hapi --save \[/code\]

Após a instalação ter sido finalizada, teremos 3 linhas a mais no nosso package.json: \[code language="javascript"\] "dependencies": { "hapi": "^16.1.1" } \[/code\]

Será que agora conseguimos subir o nosso servidor de boas. Volter ao terminal e execute o seguinte comando para o NodeJS executar o arquivo **server.js**:

\[code language="shell"\] ~/Desktop/app$ node server.js \[/code\]

Se tudo der certo você terá a seguinte saída no terminal:

\[code language="shell"\] Server rodando em: http://localhost:3000 Para derrubar o servidor: ctrl + c \[/code\]

Se você abrir o browser (navegador) e tentar utilizar o servidor que acabamos de criar pela URL **http://localhost:3000/**, receberá como resposta um json elegante:

\[code language="javascript"\] {"statusCode":404,"error":"Not Found"} \[/code\]

Este json aparece pois ainda não temos nenhuma rota que responda a esta URL.

## Como criar rotas no HapiJS

Com o nosso servidor criado podemos construir as 3 rotas do nosso pequeno projeto. Vamos começar criando a rota para a **Home**. Abra o arquivo **server.js** e adicione as 7 linhas abaixo da linha que passamos as configurações do nosso servidor:

\[code language="javascript"\] const Hapi = require('hapi') const server = new Hapi.Server() const config = { port : 3000, host: 'localhost' }

server.connection(config)

// Aqui criamos a rota server.route({ method : 'GET', path : '/', handler : (req, res) => { res('Hello!') } })

server.start((err) => { if (err) { throw err }

console.log(\`Server rodando em: ${server.info.uri}\`); console.log('Para derrubar o servidor: ctrl + c'); }) \[/code\]

Antes de ir ao browser, precisamos derrubar o servidor apertando as teclas **CTRL** e **c** juntas, e em seguida rodar o comando abaixo para subir o servidor novamente:

\[code language="shell"\] ~/Desktop/app$ node server.js \[/code\]

Agora sim, vá até o browser e acesse **http://localhost:3000/**, você deverá ter como resposta um texto com o conteúdo **Hello!**.

Show! Agora podemos criar a nossa segunda rota que irá responder para o path **/contato**. Precisamos novamente abrir o arquivo **server.js** e adicionar algumas linhas após o código responsável pela rota da home:

\[code language="javascript"\] const Hapi = require('hapi') const server = new Hapi.Server() const config = { port : 3000, host: 'localhost' }

server.connection(config)

server.route({ method : 'GET', path : '/', handler : (req, res) => { res('Hello!') } })

// Criamos aqui a segunda rota server.route({ method : 'GET', path : '/contato', handler : (req, res) => { res(\` <h1>Contato</h1>

<form action="/contato" method="POST"> <label for="email">Email:</label> <input type="email" name="email" id="email">

<label for="mensagem">Mensagem:</label> <textarea name="mensagem" id="mensagem"></textarea>

<input type="submit" value="Enviar"> </form> \`) } })

server.start((err) => { if (err) { throw err }

console.log(\`Server rodando em: ${server.info.uri}\`); console.log('Para derrubar o servidor: ctrl + c'); }) \[/code\]

Todas vez que nós mudarmos o arquivo server.js precisamos derrubar o servidor pressionando as teclas **CTRL** e **c** juntas, em seguida executar o comando reponsável por subir o servidor com o código atualizado:

\[code language="shell"\] ~/Desktop/app$ node server.js \[/code\]

Indo até o browser e acessando a URL **http://localhost:3000/contato** você terá como resposta um form bem simples e com um layout feito como este:

![](https://blog.caelum.com.br/wp-content/uploads/2017/04/Screen-Shot-2017-04-01-at-23.39.33.png)

Clicando no botão enviar do formulário seremos enviados para a mesma rota que nós estamos mas com o método **POST**. Como não criamos um código para responder a essa rota, veremos como resposta o elegante JSON de 404 no nosso browser: {"statusCode":404,"error":"Not Found"}

Para resolvermos esse problema precisamos criar a nossa rota dentro do arquivo **server.js**. Nesse arquivo vamos adicionar outras 7 linhas após o código responsável pela rota do formulário:

\[code language="javascript"\] const Hapi = require('hapi') const server = new Hapi.Server() const config = { port : 3000, host: 'localhost' }

server.connection(config)

server.route({ method : 'GET', path : '/', handler : (req, res) => { res('Hello!') } })

server.route({ method : 'GET', path : '/contato', handler : (req, res) => { res(\` <h1>Contato</h1>

<form action="/contato" method="POST"> <label for="email">Email:</label> <input type="email" name="email" id="email">

<label for="mensagem">Mensagem:</label> <textarea name="mensagem" id="mensagem"></textarea>

<input type="submit" value="Enviar"> </form> \`) } })

// Criando a última rota server.route({ method : 'POST', path : '/contato', handler : (req, res) => { res('<h1>Precisamos aprender a pegar os valores que o usuário digitou!</h1>') } })

server.start((err) => { if (err) { throw err }

console.log(\`Server rodando em: ${server.info.uri}\`); console.log('Para derrubar o servidor: ctrl + c'); }) \[/code\]

Se você for até o terminal e reiniciar o servidor teremos a nossa rota com o método POST criada e pronta para ser utilizada. Volte ao browser, acesse o formulário e logo em seguida clique no botão enviar. Desta vez não teremos mais o erro, nossa resposta será a seguinte mensagem:

**Precisamos aprender a pegar os valores que o usuário digitou!**

Ser tiver qualquer dúvida ou o seu código do post não funcionou, por favor não deixe de perguntar pelo comentário ou se preferir pode me adicionar nas redes socias da vida, você vai conseguir me achar por **MarcoBrunoBR**. Até o próximo post sobre KoaJS.
