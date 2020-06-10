---
title: "Bye bye Grunt.js, hello Gulp.js!"
date: "2014-01-14"
author: "leonardo.souza"
authorEmail: "leonardodisouza@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

No ano passado, um enxurrada de projetos baseados em JavaScript apareceu no mercado. Grande parte disto deve-se ao fato da linguagem não estar restritamente ligada apenas ao ambiente dos navegadores, e sem dúvidas a plataforma do **Node.js** contribuiu demais para a expansão de todo esse ecossistema.

No balanço da onda, surgiram alternativas para ferramentas tradicionais de build (Maven, Ant e afins) e entre estas a que mais se destacou foi sem dúvidas o **Grunt.js** - e você pode acompanhar o beabá desse cara num [excelente post publicado pelo Sérgio Lopes aqui no blog da Caelum](https://blog.caelum.com.br/automacao-de-build-de-front-end-com-grunt-js/ "Automação de build de front-end com Grunt.js").

O Grunt.js é uma ferramenta fantástica, mas requer do desenvolvedor alguns conhecimentos que nem todo mundo está verdadeiramente por dentro, como por exemplo o perfeito entendimento da sintaxe literal para construção de objetos que é a base da organização do **Grunt**.

[![Screen Shot 2014-01-11 at 12.33.56 AM](https://blog.caelum.com.br/wp-content/uploads/2014/01/Screen-Shot-2014-01-11-at-12.33.56-AM-300x170.png)](https://blog.caelum.com.br/wp-content/uploads/2014/01/Screen-Shot-2014-01-11-at-12.33.56-AM.png)

A idéia do **Gulp.js** é abstrair esse conhecimento e trabalhar de uma forma mais simplificada, onde você vai precisar conhecer meia dúzia de métodos e ele por trás faz a abstração dos detalhes necessários para a geração do build.

## Começando com o Gulp.js

Assim como o Grunt, o Gulp é uma ferramenta de automação de tarefas feita em JavaScript e rodando em cima do Node.js. Como o core da execução é o Node, precisamos começar nossos trabalhos definindo o arquivo de vai gerenciar os módulos gulp que você utilizará no seu projeto. Para isto, você deve criar na raiz do mesmo o arquivo package.json.

A forma mais fácil de fazer isso, é utilizando a seguinte instrução no terminal: \[code\] $ npm init \[/code\]

Esse comando questionará o desenvolvedor sobre uma série de detalhes do projeto (nome, versão, descrição, repositório no git, palavras chaves, autor, licença, etc). Você pode fornecer os detalhes ou ir pressionando Enter e deixar que gerenciador faça as definições baseado em algumas sugestões para as chaves indicadas. No final, o gerenciador exibirá uma amostra do JSON que foi definido e solicitará a sua confirmação para gravação do **package.json**:

\[code\] { "name": "gulpteste", "version": "0.0.0", "description": "", "main": "index.js", "scripts": { "test": "echo \\"Error: no test specified\\" && exit 1" }, "author": "", "license": "ISC" } \[/code\]

Ai basta digitar um "yes" e ser feliz!

## Instalando os módulos

Essa é uma parte bem simples, pois baseada nas tarefas que você gostaria de automatizar, será necessário instalar os módulos do **Gulp**. No nosso exemplo, faremos um procedimento simples, de minificação de arquivos em JavaScript utilizando o **Uglify**. Então além dos módulos que farão o gulp funcionar (**gulp e gulp-util**), você precisará instalar o módulo da minificação (**gulp-uglify**) e de quebra instalaremos também um módulo para monitorar alterações nos arquivos .js e rodará a tarefa da minificação (**gulp-watch**)

\[code\] $ npm install gulp --save-dev $ npm install gulp-util --save-dev $ npm install gulp-uglify --save-dev $ npm install gulp-watch --save-dev \[/code\]

Note que para cada módulo instalado, utilizamos o parâmetro **\--save-dev**. Isto modificará nosso arquivo package.json adicionando ao mesmo os módulos que a partir de agora são dependências para rodarmos as tarefas do **Gulp**. Se você for curioso, pode expiar o conteúdo do **package.json**, ele deve estar assim:

\[code\] { "name": "gulpteste", "version": "0.0.0", "description": "", "main": "index.js", "scripts": { "test": "echo \\"Error: no test specified\\" && exit 1" }, "author": "", "license": "ISC", "devDependencies": { "gulp": "~3.3.0", "gulp-util": "~2.2.9", "gulp-uglify": "~0.1.0", "gulp-watch": "~0.3.3" } } \[/code\]

A grande vantagem de instalar os módulos desta maneira, é a fácil reinstalação de todas as dependências. Imagine que inadvertidamente alguém apagou a pasta **node\_modules** do seu projeto. Ou mesmo, imagine que você gostaria de compartilhar este projeto com outro desenvolvedor. Não há a mínima necessidade de versionar a pasta **node\_modules**. Basta ter o **package.json** atualizado e uma única instrução fará que com que tudo volte ao normal. A título de teste, exclua agora sua pasta **node\_modules**... da até uma tristeza né, afinal você terá que reinstalar tudo novamente. A boa notícia é que agora tudo será bem mais fácil, bastando rodar um:

\[code\] $ npm install \[/code\]

Pronto! Tudo voltou a ordem que estava anteriormente.

## Configurando suas tarefas

Para rodar o Gulp.js, você vai precisar configurar as tarefas que serão executadas. A título de exemplo, vamos criar 2 pastas, uma chamada **src** e outra chamada **build**, na raiz do projeto.

Dentro da pasta **src**, vamos criar uma pasta **js** onde ficarão todos os arquivos originais de desenvolvimento.

Com a automatização, será criada automaticamente uma pasta **js** dentro de **build**, com os arquivos js que estão na pasta **src**, porém estes últimos estarão minificados pelo **Uglify**. Para essa mágica toda acontecer, vamos criar na raiz o arquivo de configuração das tarefas, o **gulpfile.js**.

Abra o arquivo e mãos a obra. O primeiro passo é instanciar os módulos que vamos utilizar:

\[code\] // instanciando módulos var gulp = require('gulp'); var gutil = require('gulp-util'); var uglify = require('gulp-uglify'); var watch = require('gulp-watch'); \[/code\]

A primeira tarefa que vamos definir, é que vai minificar os arquivos da pasta **src/js** para pasta **build/js**. Para isto, utilizaremos o método **gulp.task()**, que recebe 2 parâmetros. O primeiro é o **nome da nova tarefa** e o segundo uma **função callback** com os steps que devem ser executados a partir desta:

\[code\] gulp.task('scripts', function() { // corpo da tarefa }); \[/code\]

Para configurar a minificação, precisamos definir 3 ações:

1 - recuperar os arquivos fonte na pasta de origem; 2 - aplicar a minificação (uglify); 3 - colocar o resultado na pasta de destino.

O código ficará assim:

\[code\] gulp.task('scripts', function() { // corpo da tarefa return gulp .src(\['src/js/\*\*/\*.js'\]) .pipe(uglify()) .pipe(gulp.dest('build/js')); }); \[/code\]

Pronto! Para rodá-la, podemos ir ao terminal e digitar:

\[code\] $ gulp scripts \[/code\]

E conferir se os arquivos foram minificados em **build/js**.

Para deixar a coisa mais legal, vamos configurar a tarefa que vai monitorar alterações na pasta **src/js** e rodar a tarefa 'scripts', responsável pela minificação. Começamos da mesma forma:

\[code\] gulp.task('watch', function() { // corpo da tarefa }); \[/code\]

Dentro da nova task, vamos utilizar o método **gulp.watch()** que recebe 2 parâmetros. No primeiro, definimos a **pasta onde estão os nossos arquivos fonte** e um **função callback** que será executada toda vez que um desses arquivos for modificado:

\[code\] gulp.task('watch', function() { // corpo da tarefa gulp.watch('src/js/\*\*/\*.js', function(event) {}); }); \[/code\]

Agora no callback, o método **gutil.log()** exibirá informações do arquivo alterado, e através do método **gulp.run()** definimos qual tarefa será executada. Isso acontecerá toda vez que um arquivo presente em **src/js** for alterado:

\[code\] gulp.task('watch', function() { // corpo da tarefa gulp.watch('src/js/\*\*/\*.js', function(event) { gutil.log('File '+event.path+' was '+event.type+', running tasks...'); gulp.run('scripts'); }); }); \[/code\]

Vamos conferir, no terminal execute:

\[code\] $ gulp watch \[/code\]

Ai basta alterar qualquer arquivo .js da pasta fonte e ver o resultado na pasta **build/js**.

## Indo além

Agora que temos uma boa noção como tudo funciona, basta repetir o ciclo. Para cada nova tarefa que for automatizar, siga estes passos:

1 - instale o novo módulo usando npm install; 2 - instancie o novo módulo no gulpfile.js; 3 - defina a nova task e o que a mesma executará; 4 - teste e divirta-se!

Conheça mais sobre o projeto, visitando o [site oficial](http://gulpjs.com/). Dê uma olhada no [link plugins](http://gratimax.github.io/search-gulp-plugins/) , lá você vai descobrir uma série de módulos para utilizar com o Gulp.js

Se você quiser saber mais sobre o processo de automatização de tarefas de front-end (utilizando Grunt.js), não deixe deixe de conhecer [nosso treinamento de Programação JavaScript](http://www.caelum.com.br/curso-javascript-jquery/) . Te vejo por lá!
