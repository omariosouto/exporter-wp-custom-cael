---
title: "Como preparar uma aplicação React para o deploy em um servidor Node"
date: "2019-03-18"
author: "marcio.souto"
authorEmail: "mario.souto@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

## Introdução

Olá, eu me chamo Mario Souto, trabalho como desenvolvedor e instrutor no Grupo Caelum Alura e nas horas vagas faço consultoria e algumas doideras.

Eu trabalho bastante com React em diversos projetos e no começo eu lembro que tinha diversas dúvidas sobre como eu poderia publicar minha aplicação, quais os passos essenciais que devem ser feitos para que minha app possa ser publicada e é exatamente isso que iremos abordar, o setup necessário para preparar nossa app.

## Preparando o ambiente

Para esse post, eu deixei já previamente montado um [boilerplate inicial que possui o React Router configurado e você pode acessá-lo clicando aqui](https://github.com/omariosouto/preparando-app-react-para-deploy). Após baixa-lo, rodar um `npm install`, vamos para o próximo passo.

## Preparando a aplicação para ser deployada

Agora, com nosso template inicial devidamente instalado, podemos rodar um `npm start` na pasta do projeto e visualiza-lo no navegador:

![Imagem de uma página home](https://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0.png)

As duas páginas que existem são a Home em / e Contato em /contato, em um processo para deployar essa app, deveriamos parar o processo que está rodando do `npm start` e rodar o comando `npm run build` que gera a versão de prod do nosso projeto:

![Imagem do terminal do Visual Studio Code com um projeto React rodando](https://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-1.png)

> Usando o create-react-app, o build gerado já é extremamente otimizado, caso você tenha um build customizado, super recomendo [seguir as dicas na documentação do React para configurar o seu build separado](https://reactjs.org/docs/optimizing-performance.html) com webpack, rollup ou alguma outra ferramenta de build.

Após isso, se olharmos na nossa estrutura de diretórios é possível ver que uma pasta /build foi gerada com todos os arquivos que nossa app possui incluindo um index.html:

![Imagem do projeto buildado no Visual Studio Code](https://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-2.png)

Porém se tentarmos abrir esse html no browser podemos ver que não é possível acessar nossa aplicação recém buildada:

![Projeto carregado via um servidor node com todos os redirects apontados para o index.html](https://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-3.png)

Isso acontece porque os projetos React foram feitos para rodar com os arquivos sendo enviados via um servidor http e não via file://, para resolver isso vamos criar um setup utilizando o express (por ser algo mais próximo do desenvolvedor que já trabalha com JavaScript) que envie o nosso arquivo index.html sempre que houver um request em nossa aplicação, dado que estamos trabalhando com uma SPA e só temos uma única página e o Front End decide qual rota irá ser carregada.

Para resolver isso, vamos fazer o seguinte:

1 - Adiconar o express ao nosso site: `npm install express`

2 - Após o express ser instalado, devemos criar na raíz do projeto um arquivo server.js e dentro dele vamos fazer o setup básico do express:

```javascript


const express = require('express')

const app = express()

const baseDir = `${__dirname}/build/`

app.get('*', (req,res) => res.sendFile('index.html' , { root : baseDir }))

const port = 4000

app.listen(port, () => console.log(`Servidor subiu com sucesso em http://localhost:${port}`))

```

3 - Nossa configuração já está quase correta, porém se acessarmos http://localhost:4000, veremos que mesmo os requests para os arquivos .css e .js estão vindo com o conteúdo do index.html

![Todos os requests estão respondendo com nosso arquivo index.html, inclusive os CSSs, JavaScripts e imagens](https://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-4.png)

Isso acontece pelo fato de que na linha `app.get('*', (req,res) =>; res.sendFile('index.html' , { root : baseDir }))` instruímos que qualquer request do tipo GET deve enviar o nosso html, para resolver isso, devemos adicionar uma linha que crie rotas no backend para todos os assets da nossa aplicação e após isso deixarmos o código do app.get que redireciona qualquer outro request para o nosso index.html:

```javascript

app.use(express.static(`${baseDir}`))

app.get('*', (req,res) => res.sendFile('index.html' , { root : baseDir }))
```

Após fazer esse ajuste, ao acessarmos a URL da nossa aplicação podemos visualizar que todos os arquivos foram carregados de forma correta:

![Agora nossa aplicação está respondendo corretamente aos requests](https://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-5.png)

Com isso, para colocar nosso site em produção basta mover a pasta do projeto para o servidor, rodar os comandos `npm install, npm run build` e `node ./server.js` e fazer um ajuste ou outro para pegar a porta correta dependendo do que o seu serviço de hospedagem solicitar :)

Em um próximo post, trarei como podemos fazer o deploy de uma aplicação React em lugares como WeDeploy, Umbler ou mesmo Digital Ocean, deixe nos comentários o que você gostaria de ver e nos vemos no próximo post /o

Espero que tenha gostado do post, em breve trarei mais dicas, não deixe de [me seguir nas minhas redes sociais](https://twitter.com/omariosouto) e acompanhar meus outros posts em meu site pessoal [https://mariosouto.com](https://mariosouto.com) até mais \\o
