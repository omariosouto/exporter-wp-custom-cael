---
title: "Aprendendo Angular usando Angular CLI"
date: "2018-03-19"
author: "vanessa.tonini"
authorEmail: "vanessa.tonini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muitas pessoas interessadas no curso de [Angular](https://www.caelum.com.br/curso-angular) da Caelum, com alguma frequência perguntam com qual versão trabalhamos no curso, e a resposta que damos é: "a versão mais recente".

Esta resposta deixa muita gente intrigada, se perguntando "como?". Bom este post responde como conseguimos isto, e é bem mais simples do que se imagina, tudo isso deve-se ao uso da ferramenta de linha de comando do Angular durante o curso, ou seja, a [Angular CLI](https://cli.angular.io/).

#### CLI - Command Line Interface

No mundo dos frameworks, independente da linguagem, com alguma frequência encontramos ferramentas que nos auxiliam com o dia a dia no desenvolvimento de software, principalmente quando existem tarefas repetitivas e trabalhosas.

Estas ferramentas geralmente são outros softwares, que podem variar de formato, podendo ser: bibliotecas ou _plugins_ adicionados ao projeto, um "programa" com interface gráfica, ou um programa de linha de comando, que é o caso da Angular CLI.

A CLI do Angular, foi lançada em março de 2017, com a intenção melhorar a experiência da pessoa desenvolvedora de uma maneira geral, facilitando infinitamente o primeiro contato com o framework desde a criação de um novo projeto, com toda a estrutura base montada e funcionando, assim como a criação de componentes, e com o momento do (não mais) trabalhoso _build_ da aplicação.

#### Iniciando com a CLI

O pré-requisito é ter o **[NodeJS](https://nodejs.org)** instalado numa versão atual (que já vem com o **npm** - que também é necessário).

Com isto, basta instalar a CLI globalmente em seu computador com o comando **`npm install -g @angular/cli`**.

#### O comando `ng`

Após instalar a CLI em seu computador, você poderá executar o comando **`ng`** no seu terminal.

Por exemplo, podemos testa-lo consultando sua versão desta maneira: **`ng -v`**.  

![Execução do comando ng](https://blog.caelum.com.br/wp-content/uploads/2018/03/ng-v.png)

Consultando se a CLI foi instalada com o comando `ng -v`

#### Criando um projeto Angular

Para criar toda estrutura de um projeto (que funciona) usando Angular, basta executar: **`ng new nomedoprojeto`**, por exemplo: **`ng new caelum`**, isto cria uma pasta `caelum`, onde dentro dela a CLI se encarrega de criar toda a estrutura de arquivos e baixar as dependências do node.

> Se você quiser criar um projeto simples e pequeno apenas para "estudar", pode usar a flag `--minimal`, o comando ficaria **`ng new nomedoprojeto --minimal`**.
> 
> Isto gera um projeto sem a estrutura de testes automatizados.

O comando `new` sempre baixa a versão mais recente do Angular, porém depende um pouco da versão da própria CLI, cada versão da CLI está ligada a uma versão do Angular.  

![execução do comando ng new linesabout --minimal](https://blog.caelum.com.br/wp-content/uploads/2018/02/ngnew-min.gif)

Criando um projeto chamado linesabout

#### Executando o projeto Angular

Para ver o projeto que acabou de criar, entre na pasta dele pelo terminal, `cd nomedoprojeto` e execute o comando **`ng serve`**. Isto iniciará um servidor local que ficará escutando por modificações nos arquivos do projeto, que estão dentro da pasta `src`.  

![execução do comando ng serve](https://blog.caelum.com.br/wp-content/uploads/2018/03/ngserve.gif)

#### Gerando componentes, modulos, etc...

A CLI conta com um comando para os ajudar na penosa tarefa de criar a estrutura básica de um componente, ou módulo. O generate vai te ajudar gerar o que for necessário para isto. No terminal, esteja na raiz do projeto, e por exemplo, para gerar um componente, usamos o seguinte comando: **`ng generate component nomedocompoente`** . Com este comando vais ser gerado os arquivos do componente e já atualizado o `app.module.ts`, para você já usar seu componente.

Para ver todas as possibilidade do `generate` confira a [documentação](https://github.com/angular/angular-cli/wiki/generate).

#### _Buildando_ a aplicação

Fazer o build de uma aplicação usando um "_framework javascript_" nunca foi tão fácil, se não fosse pela CLI. Em outro terminal, ou no mesmo (parando a execução do servidor local - com **`ctrl c`**) é só executar o comando **`ng build`**. A CLI criará uma pasta `dist`   na raiz do projeto. Esta pasta conterá os arquivos estáticos para você apenas enviar para seu servidor web e ver tudo funcionando lindamente!

Existem algumas [opções/flags](https://github.com/angular/angular-cli/wiki/build) para este comando muito interessantes para otimizar o build do projeto.  

![execução do comando ng build](https://blog.caelum.com.br/wp-content/uploads/2018/02/ng-build.gif)

Executando o comando ng build

Em dezembro de 2017, participei do [FrontInPoa](http://frontin.poa.br/2017/), e lá fiz uma palestra sobre Angular CLI, onde conto mais detalhes, confiram:

<iframe style="display: block; margin: 0 auto;" src="https://www.youtube.com/embed/03-LYh7FQUw" width="560" height="315" frameborder="0" allowfullscreen="allowfullscreen"></iframe>

Chegando ao fim, é possível concluir que a CLI do Angular é uma ferramenta muito importante e seu uso está cada vez mais interessante. Aproveite para baixar e aprender Angular usando ela!
