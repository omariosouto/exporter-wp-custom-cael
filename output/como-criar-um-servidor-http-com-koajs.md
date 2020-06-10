---
title: "Como criar um servidor HTTP com KoaJS"
date: "2017-05-18"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Esse é o quarto e último post de uma série de posts que estou escrevendo. No primeiro post nós aprendemos a [criar um servidor web com a API HTTP do NodeJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-nodejs/), no segundo post vimos [como criar o servidor web com o ExpressJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-expressjs/) e no terceiro [como fazemos com o HapiJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-hapijs/). Agora é a vez de implementarmos um **servidor web com o KoaJS**. Vamos rever o projeto que fizemos nos posts anteriores, pois faremos o mesmo agora.

## As 3 rotas com KoaJS

Vamos implementar 3 rotas com o KoaJS, duas delas responderão ao verbo GET e a restante responderá ao POST.

1. Nossa **Home** que terá apenas um texto dizendo **Olás** -- http://localhost:3000/ (GET)
2. Um formulário de contato com dois campos -- http://localhost:3000/contato (GET)
3. Por último uma rota com o mesmo path da anterior mas que responda ao método POST -- http://localhost:3000/contato (POST)

## Criar um servidor web com o KoaJS

Primeiro precisamos criar um servidor web para depois conseguirmos criar uma resposta para as três rotas. Por favor, abra o terminal e execute o comando abaixo que é responsável por entrar no seu Desktop (área de trabalho), criar uma pasta **app** e em seguida entrar na pasta:

\[code language="shell"\] ~$ cd ~/Desktop && mkdir app && cd app \[/code\]

Dentro da pasta **app**, crie um arquivo com o nome de **server.js**, dentro desse arquivo adicionaremos o código responsável por criar o nosso servidor http com o KoaJS:

\[code language="javascript"\] const Koa = require('koa') const app = new Koa() const port = 3000

app.use(ctx => ctx.body = 'Olás')

app.listen(port, () => { console.log(\`Servidor criado em: http://localhost:${port}\`) console.log('Para derrubar o servidor: ctrl + c') }) \[/code\]

O código é bem pequeno e direto. Se você está vendo pela primeira vez a **const** e a **() => {}**, recomendo você dar uma olhada no [curso avançado de JavaScript na Alura](https://www.alura.com.br/carreira-engenheiro-javascript) do [Flávio Almeida](https://twitter.com/flaviohalmeida).

Agora só precisamos rodar o comando abaixo para colocar o servidor que acabamos de criar com o KoaJS:

\[code language="shell"\] ~/Desktop/app$ node server.js \[/code\]

Infelizmente, acabamos recebendo uma mensagem de erro. Algo como isso: \[code language="shell"\] module.js:472 throw err; ^

Error: Cannot find module 'koa' at Function.Module.\_resolveFilename (module.js:470:15) at Function.Module.\_load (module.js:418:25) at Module.require (module.js:498:17) at require (internal/module.js:20:19) at Object.<anonymous> (/Users/marcobruno/github/server-http-with-koajs/app/server.js:1:75) at Module.\_compile (module.js:571:32) at Object.Module.\_extensions..js (module.js:580:10) at Module.load (module.js:488:32) at tryModuleLoad (module.js:447:12) at Function.Module.\_load (module.js:439:3) \[/code\]

Recebemos esse erro porque não definimos o KoaJS como dependência do nosso projeto. Precisamos voltar no terminal e rodar o comando abaixo para resolver esse problema:

\[code language="shell"\] ~/Desktop/app$ npm install --save koa \[/code\]

Nesse ponto vai levar alguns segundos, dependendo da sua internet pode levar alguns minutos :-) Agora que o KoaJS está instalado e definido como dependência do nosso projeto, podemos rodar o comando para subir o nosso servidor web em cima da plataforma NodeJS:

\[code language="shell"\] ~/Desktop/app$ node server.js \[/code\]

Se tudo deu certo você receberá a seguinte saída no terminal:

\[code language="shell"\] Servidor criado em: http://localhost:3000 Para derrubar o servidor: ctrl + c \[/code\]

Agora vá até seu browser (navegador) favorito que com certeza é o **Firefox :-)** e acesse a URL **http://localhost:3000**. Se tudo estiver OK você terá o texto **Olás** como resposta no browser. Acesse outro path, por exemplo **http://localhost:3000/contato**. Infelizmente teremos sempre a mesma resposta. Chegou a hora de criarmos respostas diferentes para as 3 rotas que comentamos no começo do post.

## Como criar rotas no KoaJS?

Dentro do parâmetro **ctx** que recebemos no callback do **app.use** tem um json com o nome de **request**, é dele que vamos tirar a informação para saber qual foi o path da request.

Se quisermos responder um formulário de contato quando o usuário fizer uma requisição para o path **/contato**. Vamos adicionar um **if** dentro do callback que o **app.use** tem, este **if** validará se o path da request é **/contato**, caso seja, responderemos no body da contexto o formulário de contato com um título. Abra o arquivo **server.js** e altere o código:

\[code language="javascript"\] const Koa = require('koa') const app = new Koa() const port = 3000

app.use(ctx => { if(ctx.request.url == '/contato') { ctx.body = (\` <h1>Contato</h1>

<form action="/contato" method="POST"> <label for="email">Email:</label> <input type="email" name="email" id="email">

<label for="mensagem">Mensagem:</label> <textarea name="mensagem" id="mensagem"></textarea>

<input type="submit" value="Enviar"> </form> \`) } })

app.listen(port, () => { console.log(\`Servidor criado em: http://localhost:${port}\`) console.log('Para derrubar o servidor: ctrl + c') }) \[/code\]

Show! Já conseguimos implementar uma rota, só estão faltando duas. Vamos preparar nosso código para responder ao mesmo path (**/contato**) só que agora com o método POST. Para responder a métodos diferente mas com o mesmo path será necessário adicionar mais uma condição ao **if** que acabamos de adicionar, além de criar um segundo **if** para a requisição feita por POST para o mesmo path (**/contato**):

\[code language="javascript"\] const Koa = require('koa') const app = new Koa() const port = 3000

app.use(ctx => { if(ctx.request.url == '/contato' && ctx.request.method == 'GET') { ctx.body = (\` <h1>Contato</h1>

<form action="/contato" method="POST"> <label for="email">Email:</label> <input type="email" name="email" id="email">

<label for="mensagem">Mensagem:</label> <textarea name="mensagem" id="mensagem"></textarea>

<input type="submit" value="Enviar"> </form> \`) } if(ctx.request.url == '/contato' && ctx.request.method == 'POST') { ctx.body = '<h1>Precisamos aprender a pegar os valores que o usuário digitou!</h1>' } })

app.listen(port, () => { console.log(\`Servidor criado em: http://localhost:${port}\`) console.log('Para derrubar o servidor: ctrl + c') }) \[/code\]

Dando uma olhada no código, ele não está muito elegante e bem complicado para ler, na verdade ele está bem parecido com o código que fizemos no [post que utilizamos a API HTTP do NodeJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-nodejs). Para não ficarmos com um código tão complicado para manter podemos instalar o module **koa-router** para cuidar das rotas para gente, dessa forma podemos deixar de utilizar os **if**s da vida.

Por favor, abra o terminal e instale o **koa-router** e deixe ele como dependência do projeto:

\[code language="shell"\] ~/Desktop/app$ npm install --save koa-router \[/code\]

Obs. Cuidado para não instalar o module errado chamado koa-route.

Agora que temos o module instalado precisamos mudar o código e tirar os **if**s chatos. Abra o **server.js** e deixe o código dele assim... Ah! Já que vamos mudar o código para deixar ele mais feliz, bora aproveitar esse momento para adicionar também a rota da home:

\[code language="javascript"\] const Koa = require('koa') const Router = require('koa-router')

const app = new Koa() const router = new Router() const port = 3000

router .get('/', (ctx, next) => { ctx.body = \`<h1>Você está na Home!</h1>\` }) .get('/contato', (ctx, next) => { ctx.body = (\` <h1>Contato</h1>

<form action="/contato" method="POST"> <label for="email">Email:</label> <input type="email" name="email" id="email">

<label for="mensagem">Mensagem:</label> <textarea name="mensagem" id="mensagem"></textarea>

<input type="submit" value="Enviar"> </form> \`) }) .post('/contato', (ctx, next) => { ctx.body = '<h1>Precisamos aprender a pegar os valores que o usuário digitou!</h1>' })

app.use(router.routes())

app.listen(port, () => { console.log(\`Servidor criado em: http://localhost:${port}\`) console.log('Para derrubar o servidor: ctrl + c') }) \[/code\]

Agora é só derrubar o servidor e subir ele novamente para ver se tudo está funcionando de boas e feliz:

\[code language="shell"\] ~/Desktop/app$ node server.js \[/code\]

Vá até o browser pela última vez e verifique se as três rotas estão funcionando normalmente :-) Esse foi o meu último post dessa série, logo mais eu volto com mais post sobre NodeJS e os famosos frameworks escritos em JavaScript. Se tiver qualquer dúvida, sugestões ou observações, por favor não deixe de falar, seja pelo comentário no post ou me mandando mensagem pelas redes sociais, você vai conseguir me achar em qualquer lugar por [@MarcoBrunoBR](https://twitter.com/marcobrunobr).
