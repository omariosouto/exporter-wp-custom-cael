---
title: "Experimente o Yeoman em seu workflow de projetos front-end"
date: "2013-07-31"
author: "flavio.almeida"
authorEmail: "flavio.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![yeoman](https://blog.caelum.com.br/wp-content/uploads/2020/07/yeoman-300x71.jpeg)](https://blog.caelum.com.br/wp-content/uploads/2020/07/yeoman.jpeg)

[Yeoman](http://yeoman.io) (yo) é um stack com foco em front-end que automatiza seu build e gerencia suas dependências. Ele não reinventa a roda e usa [Grunt](https://blog.caelum.com.br/automacao-de-build-de-front-end-com-grunt-js/) e [Bower](http://bower.io/) como _builder_ e _package manager_ respectivamente. Ele inclusive realiza _scaffolding_.

Yeoman ainda:

- Compila [CoffeScript](http://coffeescript.org/) automaticamente
- Verifica JavaScript automaticamente com [JSHint](http://www.jshint.com/)
- Minifica e faz o merge de seus scripts
- Otimiza suas imagens
- Faz [LiveReload](http://livereload.com/) de sua página com um [preview server](https://github.com/yeoman/yeoman/wiki/yeoman-server) local
- Realiza [testes](http://karma-runner.github.io/) unitários

## Instalação

Você precisa ter o [Node.js](http://nodejs.org/) instalado em sua máquina antes de começar a utilizar o Yeoman. Com este pré-requisito, sua instalação é direta:

**npm install -g yo**

Grunt e o Bower também serão instalados.

## Yo: criando um projeto

Yeoman possui vários geradores que podem ser baixados através do [gerenciador de pacotes](https://npmjs.org/) do Node.js. O exemplo abaixo instalará um gerador para projetos com [AngularJS](http://angularjs.org/):

**npm install -g generator-angular**

Antes de executar o yo, crie uma pasta com o nome do seu projeto. Esta pasta preferencialmente deve começar com a letra maiúscula, já que o gerador do angular adotará o nome da pasta como nome do seu projeto (ng-app).

Agora é só deixar que o Yeoman monte a estrutura do projeto com o comando:

**yo angular --minsafe**

O parâmetro _\--minsafe_ indica que o projeto suportará minificação de javascript e css, inclusive _merge_.

Durante a criação do projeto você ainda pode incluir bibliotecas respondendo sim ao assistente yo, por exemplo, a biblioteca do [twitter bootstrap](http://twitter.github.io/bootstrap/). Como estamos usando um gerador de projeto focado no AngularJS, algunas perguntas específicas deste framework também são apresentadas.

## Arquivos gerados

O projeto gerado possui algumas pastas e arquivos como **bower.json**, inclusive o **Grunt.js** e o **package.json**, fundamentais para o funcionamento do Grunt.

\[caption id="attachment\_5683" align="alignnone" width="145"\][![Estrutura do projeto](https://blog.caelum.com.br/wp-content/uploads/2020/07/yeoman-estrutura-projeto-angular.png)](https://blog.caelum.com.br/wp-content/uploads/2020/07/yeoman-estrutura-projeto-angular.png) Estrutura gerada pelo Yeoman\[/caption\]

No arquivo _bower.json_ ficam as declarações das dependências do seu projeto, muitas delas adicionadas automaticamente pelo yo, por exemplo, AngularJS e [jQuery](http://jquery.com/).

No arquivo _Grunt.js_ há uma série de tasks já configuradas e prontas para serem usadas.

## Bower: resolvendo dependências

Faltou alguma dependência em seu projeto? Onde buscar? O bower realiza essa tarefa para você:

**bower install respond**

No exemplo acima, o bower baixa e torna disponível para nosso projeto a biblioteca [respond.js](https://github.com/scottjehl/Respond) na pasta **app / bower\_components / respond** adicionando-a no arquivo bower.json.

O bower ainda permite instalar uma versão específica da biblioteca. Se o jQuery 1.9.1 for necessário, basta executar a comando:

**bower install jquery#1.9.1**

## Grunt: construindo e testando seu projeto

Você já pode rodar seu projeto através do comando **grunt server**. Esta tarefa criará um servidor local e abrirá seu navegador automaticamente exibindo uma tela de boas-vindas gerada pelo _scaffold_ do yo.

Você pode testar o _liveReload_ modificando a página index.html e ver que seu browser recebe a atualização instantaneamente sem necessidade de refresh manual, inclusive páginas em dispositivos móveis como tablet e celulares.

Quando a aplicação estiver pronta para deploy, o comando **grunt** se encarregará de executar todos os testes unitários criados por você e ainda minificará e _juntará_ seus arquivos js e css. Qualquer imagem do seu projeto também será otimizada para distribuição. O resultado final estará dentro da pasta **dist**.

Você pode conhecer um pouco mais [nesse post sobre Grunt aqui no blog](https://blog.caelum.com.br/automacao-de-build-de-front-end-com-grunt-js/) ou no [site oficial do Grunt](http://gruntjs.com/).

## Mais scaffolding com Yo

Se quisermos criar um novo controlador do AngularJS através do yo, basta executar o comando:

**yo angular:controller produto**

Ele não criará apenas o controlador, mas também o seu teste. Eles ficarão nas pastas **app / scripts / controllers / produto.js** e **test / spec / controllers / produto.js** respectivamente.

Cada gerador baixado pelo Yeoman possui seu conjunto de comandos.

## Conclusão

A preocupação com a padronização, a construção e o gerenciamento de dependências não é exclusividade de programadores back-end. A complexidade de páginas web demanda cada vez mais conhecimento do programador front-end.

O Yeoman é uma ferramenta que sugere um workflow já consagrado e que pode ser incorporado em seus projetos.

A Caelum não fica de fora, e oferece uma [formação front-end](http://www.caelum.com.br/cursos-web-front-end/) pra você dominar as boas práticas de projetos com Web Design.
