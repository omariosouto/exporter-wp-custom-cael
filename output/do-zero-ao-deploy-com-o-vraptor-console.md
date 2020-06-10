---
title: "Do zero ao deploy com o vraptor-console"
date: "2013-06-11"
author: "francisco.sokol"
authorEmail: "francisco.sokol@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um ponto importante na escolha de um framework web é as ferramentas de suporte ao desenvolvimento que o projeto disponibiliza ao seus usuários. Essas ferramentas facilitam o dia a dia do desenvolvedor, agilizando as tarefas comuns que se repetem entre projetos distintos.

O [VRaptor](http://vraptor.caelum.com.br/pt/) é conhecido pela sua flexibilidade, curva de aprendizado, entre outras qualidades. Com o intuito de facilitar o desenvolvimento de aplicações com ele, criamos o [vraptor-console](https://github.com/caelum/vraptor-console), uma ferramenta de linha de comando que permite automatizar tarefas comuns, da criação de um novo projeto ao deploy da aplicação.

**Instalação**

[Baixe o vraptor-console](http://code.google.com/p/vraptor-console/downloads/list)  e extraia o tar.gz em seu computador. Depois, coloque o diretório gerado no path:

\[code\] $ tar xzf vraptor-console-0.7.0-SNAPSHOT.tar.gz -C ~/programas/ $ export PATH=$PATH:~/programas/vraptor-console/ \[/code\]

Para conferir se está tudo certo, execute o vraptor-console no terminal e confira se funciona:

\[code\] $ vraptor-console \[/code\]

A primeira execução pode demorar um pouco, pois o vraptor-console baixará o jetty, que servirá de base para subir a nossa aplicação. Se tudo ocorrer corretamente, o vraptor-console estará esperando comandos na entrada padrão. Execute o comando exit, ou digite ctrl+c para sair.

**Criando sua aplicação**

Para criar uma nova aplicação, execute no terminal: `vraptor-console new br.com.caelum loja`. Esse comando irá criar uma nova aplicação no diretório loja/ dentro da sua pasta atual.

Repare que o vraptor-console já criou a estrutura de diretórios de uma aplicação web e adicionou um controller simples do VRaptor, com um método que poderá ser acessado na home da nossa loja. Também foi criado um arquivo pom.xml simples, com as dependências básicas e algumas configurações necessárias. Esse diretório já pode ser importado para o Eclipse (ctrl+3 "Import (Existing Projects Into Workspace)").

**Usando o vraptor-console em desenvolvimento**

Apesar da criação do projeto ser um passo bem simples, o foco do vraptor-console será o desenvolvimento no dia a dia. Entre no diretório de sua aplicação e inicie o vraptor-console:

\[code\] $ cd loja $ vraptor-console \[/code\]

Para subir seu projeto, execute o comando "run" que compila e inicia a aplicação na porta 8080. Ao final, acesse http://localhost:8080/ e confira a aplicação no ar. Qualquer alteração em jsp's é carregada automaticamente pelo jetty: experimente modificar a mensagem na home.jsp e recarregar a página no navegador. As alterações em suas classes java necessitam que o servidor web seja reiniciado, para isso, execute o comando "restart".

Você pode se conectar em debug pelo eclipse. Crie uma nova "Debug Configuration". Vá em Run > Debug Configurations > Duplo clique em "Remote Java Application" e troque a configuração da parta 8000 para 1044. Execute essa nova configuração, mude para a perspectiva de debug e repare que o eclipse está conectado à JVM do vraptor-console. Experimente criar um break point no seu controller e recarregar a página para usar as funcionalidades de debug do eclipse. Repare que o hot code swapping funciona como em debug de aplicações normais, de forma que o corpo dos métodos podem ser modificados durante a execução e você não precisa reiniciar o servidor.

O vraptor-console também possui uris para executar algumas tarefas fora do ambiente de produção. Acessando http://localhost:8080/vraptor/restart, você pode reiniciar o servidor e em http://localhost:8080/vraptor/tests/unit você executa os testes de unidade e confere os resultados. Essas uris são úteis para executar tais tarefas remotamente, como por um servidor de integração contínua ou em ambiente de homologação, por exemplo.

**Deploy no heroku**

Para fazer um deploy no heroku, você deve usar o git para gerenciar as versões do seu projeto. Portanto, dentro do diretório da nossa aplicação, inicie o repositório e faça o primeiro commit:

\[code\] $ git init . $ git add . $ git commit -m "primeiro commit" \[/code\]

[Abra sua conta no heroku](https://get.heroku.com/) se você ainda não tem e instale o [heroku toolbelt](https://toolbelt.heroku.com/) para acessar o heroku pela linha de comando.

Agora, podemos criar a aplicação no heroku:

\[code\] $ heroku login $ heroku create nome-da-app \[/code\]

Para fazer o deploy no heroku é preciso usar um [buildpack](https://devcenter.heroku.com/articles/buildpacks "buildpack") customizado para o vraptor-console. Para configurar sua aplicação para usar esse buildpack faça:

\[code\] $ heroku plugins:install https://github.com/heroku/heroku-buildpacks $ heroku buildpacks:set csokol/vraptor-buildpack -a nome-da-app \[/code\]

Agora, vamos fazer o deploy da nossa aplicação. Um simples push no repositório do heroku irá desencadear o build da nossa aplicação e subir o servidor:

\[code\] $ git push heroku master \[/code\]

Pronto! Alguns instantes após o final do push, você poderá acessar sua aplicação em `http://<nome-da-app>.herokuapp.com/`

O vraptor-console foi criado com a intenção de ajudar o desenvolvedor no dia-a-dia, enquanto modifica seu código e testa sua aplicação. Em conjunto [com a nova versão do JRebel](http://zeroturnaround.com/blog/jrebel-5-2-2-released-vraptor-spring-support-improvements-numerous-fixes/), o vraptor-console permite minimizar o tempo de espera entre testes manuais durante o desenvolvimento. Recentemente também foi feito o release do [forge-vraptor-plugin](https://github.com/rsaraiva/forge-vraptor-plugin) que pode ser facilmente extendido para suportar as ações de desenvolvimento do vraptor-console.
