---
title: "Como criar um servidor HTTP com ExpressJS"
date: "2017-03-21"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

<iframe src="https://www.youtube.com/embed/4sa5Ojk727c" allow="autoplay; encrypted-media" allowfullscreen width="560" height="315" frameborder="0" style="position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;"></iframe>

  

No meu primeiro post que resolvi falar sobre JavaScript do lado de servidor, mostrei [como criar um servidor HTTP utilizando a API do NodeJS chamada http](https://blog.caelum.com.br/como-criar-um-servidor-http-com-nodejs/) e lá vimos na prática um dos problemas que motivou a comunidade de JavaScript a desenvolver frameworks para criar um servidor HTTP e gerenciar as rotas.

Já que temos uma ideia do porquê usar um framework, vamos aprender como criar um servidor HTTP e responder a 3 requisições com respostas diferentes. Detalharei um pouco melhor o nosso desafio antes de sairmos fazendo um monte de códigos.

## Desafios que implementaremos com ExpressJS

Vamos criar 3 rotas com a seguinte estrutura:  
Na nossa primeira rota só mostraremos uma página com um texto escrito **Home** dentro de uma tag h1 -- http://localhost:3000/ (GET);  
Na segunda rota teremos a exibição de um formulário com 2 campos (email e mensagem) -- http://localhost:3000/contato (GET);  
Na terceira rota utilizaremos o mesmo path (/contato) mas preparada para responder ao método POST -- http://localhost:3000/contato (POST).

## Criando um servidor web com ExpressJS

Bom, agora que já sabemos o que precisamos implementar vamos criar um arquivo chamado **server.js** que ficará dentro de uma pasta chamada **app**. Para facilitar o acompanhamento do post coloque a pasta app no **Desktop**.

No arquivo **server.js** faremos o código que será responsável por criar o nosso sevidor HTTP:

let express = require('express')
let app = express()
let port = 3000
app.listen(port, () => {
  console.log(\`Servidor rodando em http://localhost:${port}\`)
  console.log('Para derrubar o servidor: ctrl + c');
})

Com o código pronto para criar o novo servidor HTTP, precisamos executá-lo com a nossa plataforma NodeJS. Vamos abrir o terminal e executar alguns comando:

~$ cd ~/Desktop/app
~/Desktop/app$ node server.js

Depois de fazermos o segundo comando no terminal que é responsável por executar o nosso código do arquivo server.js, teremos um mensagem de erro como saída no nosso terminal. Parece que chegou a hora de aprendermos a ler os erros do NodeJS. :-)

## Aprendendo a ler erros do NodeJS

Essa é a mensagem de erro que recebemos como saída:

module.js:471
    throw err;
    ^
Error: Cannot find module 'express'
    at Function.Module.\_resolveFilename (module.js:469:15)
    at Function.Module.\_load (module.js:417:25)
    at Module.require (module.js:497:17)
    at require (internal/module.js:20:19)
    at Object.<anonymous> (/Users/marcobrunobr/gh/posts/server-http-with-expressjs/app/server.js:1:77)
    at Module.\_compile (module.js:570:32)
    at Object.Module.\_extensions..js (module.js:579:10)
    at Module.load (module.js:487:32)
    at tryModuleLoad (module.js:446:12)
    at Function.Module.\_load (module.js:438:3)

**Obs.** Acho que talvez você tenha que usar o scroll horizontal para ver a mensagem de erro completa

Esse foi o primeiro erro que li codando em JavaScript no lado do servidor, logo percebi que tanto JavaScript no navegador ou no servidor não tem mensagens de erros muito amigáveis. Mesmo assim precisamos aprender a ler mensagens de erros. Sendo assim, vamos procurar pistas nessa mensagem de erro para entender o que precisamos fazer para resolver o nosso problema:

Error: Cannot find module 'express'

Show! A quarta linha já ajuda bastante, dá para perceber que nós não temos o module **express** no nosso projeto. Mas em que linha do nosso código tentamos usar ou chamar o express?

    at Object.<anonymous> (/Users/marcobrunobr/gh/posts/server-http-with-expressjs/app/server.js:1:77)

Só na linha 10 da mensagem de erro que temos a informação de qual parte do nosso código está tentando chamar o express. Nesse linha após o nome do arquivo também é informado o número da linha e a coluna (1:77). Portanto, na linha 1 e coluna 77 está o nosso código que disparou o erro.

Dando uma olhada no nosso arquivo **server.js**:

let express = require('express')

Na primeira linha estamos importando um module com o nome express que não instalamos no projeto. Felizmente quando instalamos o NodeJS temos de graça um gerenciador de pacote chamado **npm** (Node Package Manager) e com ele fica muito simples instalar as dependências do nosso projeto.

## Instalando e definindo dependências em um projeto com NodeJS

Antes de instalar e definirmos o **express** como dependência do nosso projeto, precisamos criar um arquivo chamado **package.json** na raiz do nosso projeto que terá algumas informações do nosso projeto como:

- Nome;
- Versão;
- Descrição;
- Respósitorio git remoto;
- Autor;
- Licença;
- Dependências;
- e outras coisas...

Felizmente não precisamos criar o **package.json** na mão, podemos utilizar o **npm**. Para criar o package.json pelo npm, abra o terminal e execute o seguinte comando (isso deve ser feito dentro da pasta app):

~Desktop/app$ npm init

Após executar esse comando o **npm** fará algumas perguntas sobre o projeto, as que você não souber responder pode apenas deixar o campo vazio e pressionar o ENTER. Se tudo der certo a última pergunta do npm será:

Is this ok? (yes)

Se tudo estiver certo é só pressionar o ENTER. Para verificar se tudo está realmente ok, entre dentro da pasta **app** e verifique se foi criado um arquivo com o nome de package.json com as informações que você respondeu. Se não foi criado o arquivo package.json só avisar no comentário ou me procurar nas redes socias da vida por **MarcoBrunoBR** que eu te ajudo.

Agora que temos o nosso **package.json** criado, podemos instalar o **express** e definir ele como dependência. Abra novamente o terminal e execute:

~/Desktop/app$ npm install express --save

Apenas com essa linha de comando que acabamos de executar no terminal fizemos o download do express para o nosso projeto e, por causa do parâmetro **\--save**, ele também foi definido como dependência. Se você abrir o package.json verá que temos uma nova chave chamada **dependencies** com o express como valor.  
Se tudo estiver certo basta irmos até o terminal e executar o comando para a nossa plataforma NodeJS executar o server.js:

~/Desktop/app$ node server.js

**Obs.** Por favor, execute esse comando dentro da pasta app.

Após executar o comando acima você receberá a seguinte saída no termimal:

Servidor rodando em http://localhost:3000
Para derrubar o servidor: ctrl + c

Só pra ver se tudo está funcionando, vá até o navegador e entre em [http://localhost:3000](http://localhost:3000), deverá aparecer no navegador a seguinte mensagem: **Cannot GET /**. Isso acontece porque o express usa essa resposta por padrão para rotas que não foram definidas.

### Criando rotas com o ExpressJS

Agora que temos o nosso servidor express funcionando, só precisamos criar as 3 rotas que nos propomos no começo do post:

1. http://localhost:3000/ (GET)
2. http://localhost:3000/contato (GET)
3. http://localhost:3000/contato (POST)

Vamos abrir o nosso **server.js** e criar uma resposta para a primeira rota. A nossa resposta conterá apenas uma tag h1 com o contéudo _Home_:

let express = require('express')
let app = express()
let port = 3000
app.get('/', (req, res) => {
  res.send('<h1>Home</h1>')
})
app.listen(port, () => {
  console.log(\`Servidor rodando em http://localhost:${port}\`)
  console.log('Para derrubar o servidor: ctrl + c');
})

Link para o código no GitHub: [https://github.com/MarcoBrunoBR/server-http-with-expressjs/tree/27892f741aa33d2eb15841c3185753ff12a6ecee](https://github.com/MarcoBrunoBR/server-http-with-expressjs/tree/27892f741aa33d2eb15841c3185753ff12a6ecee)

Nosso código já está bem mais simples do implementado no post anterior, onde nós [criamos um servidor HTTP e as respostas de rotas com a API http que o NodeJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-nodejs/) fornece para nós. Utilizando o express nós não precisamo adicionar nenhum if.

No nosso próximo passo vamos criar duas rotas com path(caminho) iguais, mas com respostas diferentes dependendo do método. Quando o método for um GET responderemos com um formulário com 2 campos(email e mensagem), quando esse formulário for enviado ele fará uma requisição para o mesmo path mas com o método POST.

const express = require('express')
const app = express()
const port = 3000
app.get('/', (req, res) => {
  res.send('<h1>Home</h1>')
})
app.get('/contato', (req, res) => {
  res.send(\`
    <h1>Contato</h1>
    <form action="/contato" method="POST">
      <label for="email">Email:</label>
      <input type="email" name="email" id="email">
      <label for="mensagem">Mensagem:</label>
      <textarea name="mensagem" id="mensagem"></textarea>
      <input type="submit" value="Enviar">
    </form>
  \`)
})
app.post('/contato', (req, res) => {
  res.send('<h1>Precisamos aprender a pegar os valor que usuário digitou!</h1>')
})
app.listen(port, () => {
  console.log(\`Servidor rodando em http://localhost:${port}\`)
  console.log('Para derrubar o servidor: ctrl + c');
})

Link do código no GitHub: [https://github.com/MarcoBrunoBR/server-http-with-expressjs/tree/1cc890bf402e47d6987b96b0130a2a0d200854f8](https://github.com/MarcoBrunoBR/server-http-with-expressjs/tree/1cc890bf402e47d6987b96b0130a2a0d200854f8).

Para vermos se as rotas que criamos estão funcionando de boas, precisamos reniciar o nosso servidor HTTP. Abra o terminal, pressione **ctrl + c** e depois execute o comando para subir novamente o servidor:

~/Desktop/app$ node server

**Obs.** comando tem que ser executado dentro da pasta app.

Se você recebeu o texto abaixo no terminal, isso já uma boa pista que tudo pode estar ok:

Servidor rodando em http://localhost:3000
Para derrubar o servidor: ctrl + c

Para termos certeza que tudo está funcionando, entre no navegador e acesse a URL do formulário ([http://localhost:3000/contato](http://localhost:3000/contato)), preencha os campos e clique no botão **Enviar**, nesse momento o formulário fará uma requisição para a rota http://localhost:3000/contato com o method POST. Se tudo está certo até aqui você receberá como resposta no navegador a seguinte mensagem: **Precisamos aprender a pegar os valor que usuário digitou!**.

É isso aí conseguimos criar um servidor HTTP com ExpressJS que responde para 3 rotas e com métodos diferentes.  
Se tiver mais alguma coisa que você queira aprender a fazer com o ExpressJS ou NodeJS fala aí no comentário que eu faço um post :-)
