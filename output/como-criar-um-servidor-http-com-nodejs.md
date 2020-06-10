---
title: "Como criar um servidor HTTP com NodeJS"
date: "2017-03-07"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Nesse primeiro post criaremos um **servidor HTTP** conhecido também por **servidor web** em JavaScript puro que utilizará a API do NodeJS chamada http. Teremos um pequeno projeto para deixar o post mais feliz e um pouco mais próximo de como abordo esse assunto no [curso de NodeJS presencial que dou Caelum](https://www.caelum.com.br/curso-nodejs-express/), a única diferença desse post para o curso é que durante as aulas utilizamos um framework chamado ExpressJS para criar o servidor HTTP e responder as rotas.

## Qual será o nosso projeto?

Vamos criar um servidor HTTP para responder a 4 rotas de um site de um evento focado em desenvolvedores FrontEnd, com uma estrutura de links parecida com o site do [FrontInSampa](http://www.frontinsampa.com.br) que vai acontecer agora no dia 1 de Julho 2017. Essa são as 4 rotas que vamos criar:

1. http://localhost:3000/ **(GET)**
2. http://localhost:3000/inscreva-se **(GET)**
3. http://localhost:3000/local **(GET)**
4. http://localhost:3000/contato **(GET)**

Agora que já sabemos qual é o nosso projeto, precisamos trabalhar para preparar o nosso ambiente de desenvolvimento com o NodeJS de plataforma.

Para instalar o NodeJS é tudo bem simples, basta você entrar nesse link: [https://nodejs.org/en/download](https://nodejs.org/en/download); em seguida fazer o download para o seu respectivo sistema operacional e seguir o processo de instalação. Se tiver qualquer dúvida durante a instalação, só fazer um comentário ou me adicionar em qualquer rede social da vida que eu te ajudo. Você vai conseguir me achar nas redes sociais por MarcoBrunoBR.

## Criando um Servidor HTTP em NodeJS

Show! Agora podemos começar a criar o nosso **servidor HTTP** utilizando o NodeJS como plataforma. Começaremos criando um arquivo chamado _server.js_ dentro de uma pasta com o nome de _app_, por favor crie essa pasta no seu Desktop (Área de trabalho).

Inicialmente nosso arquivo **server.js** ficará assim:

const http = require('http')
const port = 3000
const ip = 'localhost'

const server = http.createServer((req, res) => {
  console.log('Recebendo uma request!')
  res.end('<h1>Aqui fica o que vamos enviar para o navegador como resposta!</h1>')
})

server.listen(port, ip, () => {
  console.log(\`Servidor rodando em http://${ip}:${port}\`)
  console.log('Para derrubar o servidor: ctrl + c');
})

Link desse código no GitHub: [https://github.com/MarcoBrunoBR/server-http-with-nodejs/tree/24700745eea7f3722b9335e58916f8d7682d103d](https://github.com/MarcoBrunoBR/server-http-with-nodejs/tree/24700745eea7f3722b9335e58916f8d7682d103d).

Show! Agora temos um arquivo JS com os comandos necessários para criar o nosso servidor HTTP, no nosso próximo passo precisamos fazer com que a nossa plataforma NodeJS execute nosso código (_server.js_). Para conseguirmos fazer isso, abra o seu terminal e dentro dele execute os seguintes comandos:

~$ cd ~/Desktop/app
~/Desktop/app$ node server.js

Na primeira linha, nós estamos acessando a pasta do nosso projeto com o comando **cd** seguido do caminho de onde criamos a pasta **app**. O **~** é um atalho para ir até a pasta do usuário que você está logado no seu sistema operacional. Já na segunda linha nós estamos falando para o NodeJS executar o nosso arquivo **server.js**, após ter executado esse comando você deverá receber a seguinte saída no seu terminal:

Servidor rodando em http://localhost:3000
Para derrubar o servidor: ctrl + c

Pronto! Agora nós temos o nosso servidor HTTP de pé e já podemos utilizá-lo. Para ver se tudo está certo, abra o seu navegador e acesse o [http://localhost:3000](http://localhost:3000), se tudo estiver certo você verá no seu navegador exatamente o texto que passamos para o seguinte linha do nosso **server.js**:

res.end('<h1>Aqui fica o que vamos enviar para o navegador como resposta!</h1>')

Nosso servidor está funcionando mas está com a mesma resposta para qualquer path(caminho), para testar isso, vá até o seu navegador e entre em [http://localhost:3000/contato](http://localhost:3000/contato), a resposta será a mesma. Então vamos resolver esse problema criando uma resposta única para cada uma das [4 rotas que comentamos no começo do post](#rotas).

## Respondendo diferente para cada rota

Toda vez que o nosso servidor HTTP recebe uma requisição a função de callback que passamos para o método _createServer_ é executada, por isso vamos colocar as condições para respostas diferentes conforme o endereço da requisição. Vamos começar criando uma resposta para a nossa home (http://localhost:3000/):

### server.js

const http = require('http')
const port = 3000
const ip = 'localhost'

const server = http.createServer((req, res) => {
  if (req.url == '/') {
    res.end('<h1>Home</h1>')
  }

  res.end('<h1>URL sem resposta definida!</h1>')
})

server.listen(port, ip, () => {
  console.log(\`Servidor rodando em http://${ip}:${port}\`)
  console.log('Para derrubar o servidor: ctrl + c');
})

Link do código no GitHub: [https://github.com/MarcoBrunoBR/server-http-with-nodejs/tree/70636dfd82f9329c9b1c3128d3a48be3226a829d](https://github.com/MarcoBrunoBR/server-http-with-nodejs/tree/70636dfd82f9329c9b1c3128d3a48be3226a829d)

Após você alterar o código é necessário reiniciar o servidor. Para fazermos isso você precisa ir até o terminal e teclar **ctrl + c**, dessa forma derrubamos o servidor que estava no ar. Agora precisamos subi-lo novamente com o nosso novo código, para fazer isso digite no terminal:

### Terminal, dentro da pasta app

~/Desktop/app$ node server.js

Obs. Lembre-se que você precisa estar na pasta do projeto que chamamos de **app** e criamos no nosso **Desktop**.

Entre novamente no browser e veja o que acontece quando você entrar na URL [http:localhost:3000](http://localhost:3000)? Se tudo deu certo até aqui você verá no seu navegador a palavra Home. :-)

O que acontece se você tenta entrar na URL [http://localhost:3000/inscreve-se](http://localhost:3000/inscreve-se)? Acabou aparecendo o texto **URL sem resposta definida!** dentro de uma tag h1. Tem algo interessante acontecendo no nosso código, vamos dar uma olhada nessa parte:

const server = http.createServer((req, res) => {
  if (req.url == '/') {
    res.end('<h1>Home</h1>')
  }

  res.end('<h1>URL sem resposta definida!</h1>')
})

Já vimos que toda vez que o nosso servidor recebe uma requisição HTTP ele executa o callback que passamos para o createServer, outra coisa que é bem interessante é que quando o NodeJS cai em um **res.end** tudo que está a seguir dele continua a ser executado e a resposta já foi envida, isso não é bom e nem ruim é apenas algo que precisamos lembrar quando estamos desenvolvendo.

Agora que já sabemos fazer respostas diferentes para as nossas requisições, podemos implementar para uma resposta para 3 rotas restantes:

### server.js

const http = require('http')
const port = 3000
const ip = 'localhost'

const server = http.createServer((req, res) => {
  if (req.url == '/') {
    res.end('<h1>Home</h1>')
  }

  if (req.url == '/inscreva-se') {
    res.end('<h1>Inscreva-se</h1>')
  }

  if (req.url == '/local') {
    res.end('<h1>Local</h1>')
  }

  if (req.url == '/contato') {
    res.end('<h1>Contato</h1>')
  }

  res.end('<h1>URL sem resposta definida!</h1>')
})

server.listen(port, ip, () => {
  console.log(\`Servidor rodando em http://${ip}:${port}\`)
  console.log('Para derrubar o servidor: ctrl + c');
})

Link do código no GitHub: [https://github.com/MarcoBrunoBR/server-http-with-nodejs/tree/7c651957b589071efbe8cb942c26557996b49bf6](https://github.com/MarcoBrunoBR/server-http-with-nodejs/tree/7c651957b589071efbe8cb942c26557996b49bf6)

É isso, conseguimos criar uma resposta para as 4 rotas que nos propomos no começo do post. Apesar do código estar bem legível, eu não faria com **if**, nesse caso eu utilizaria um Array:

### server.js

const http = require('http')
const port = 3000
const ip = 'localhost'

const server = http.createServer((req, res) => {
  const responses = \[\]
  responses\['/'\] = '<h1>Home</h1>'
  responses\['/inscreva-se'\] = '<h1>Inscreva-se</h1>'
  responses\['/local'\] = '<h1>Local</h1>'
  responses\['/contato'\] = '<h1>Contato</h1>'
  responses\['/naoExiste'\] = '<h1>URL sem resposta definida!</h1>'

  res.end(responses\[req.url\] || responses\['/naoExiste'\])
})

server.listen(port, ip, () => {
  console.log(\`Servidor rodando em http://${ip}:${port}\`)
  console.log('Para derrubar o servidor: ctrl + c');
})

Link do código no GitHub: [https://github.com/MarcoBrunoBR/server-http-with-nodejs/tree/48158fadfe2a9993f38f04e04fc8c75461e5e30a](https://blog.caelum.com.br/encontrando-melhores-formas-de-trabalhar-com-slack/)

Nosso código ficou com uma quantidade menor de linhas mas com uma legibilidade questionável. :-) O que você faria diferente na hora de criar o seu servidor HTTP?

Tanto o código com if ou array, são códigos complicados de se manter. Por esse motivo e outros que a comunidade de JavaScript começou a criar frameworks para cuidar das rotas. Nos próximos posts vamos ver como criar rotas com os frameworks:

1. [Restify](https://medium.com/collabcode/como-criar-um-servidor-http-com-o-restify-56bf09927153)
2. [ExpressJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-expressjs/)
3. [HapiJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-hapijs/)
4. [KoaJS](https://blog.caelum.com.br/como-criar-um-servidor-http-com-koajs/)

Além do curso presencial da Caelum, temos cursos online de [Node JS](https://www.alura.com.br/cursos-online-programacao/node-js) na Alura.
