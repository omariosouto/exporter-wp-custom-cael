---
title: "Automação de build de front-end com Grunt.js"
date: "2013-06-25"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[Ferramentas de build](https://blog.caelum.com.br/uma-introducao-a-scripts-de-build/) é o que não falta no mercado atual - desde o [Maven](https://blog.caelum.com.br/processo-de-build-com-o-maven/) e o Ant pra Java, o Gradle ou o clássico Make. Mas **o Grunt é diferente**. Foi feito em JavaScript e com grande foco em **automatizar tarefas de front-end**.

Se você, por exemplo, for seguir as [boas práticas de performance para sites](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/), já deve se preocupar com **minificar CSS e JavaScript** ou ainda **juntar arquivos** pra diminuir o número de requests e até fazer **CSS sprites**. Ou talvez você esteja usando algum pré-processador de CSS como o [LESS](https://blog.caelum.com.br/css-facil-flexivel-e-dinamico-com-less/), SASS ou Stylus.

A questão é: você vai precisar rodar um minificador, um concatenador de arquivos, um pré-processador e provavelmente muitas outras pequenas ferramentas. Rodar tudo isso individualmente é insano e queremos automatizar todo esse processo. É aí que entra o Grunt.js.

## O Grunt.js

É uma ferramenta automação de tarefas de build feita totalmente em JavaScript. Ela roda no **Node.js**, que é um interpretador JavaScript que você pode instalar no seu computador. No final do artigo mostro o processo de instalação pra quando for rodar a primeira vez.

O central no Grunt é um arquivo que descreve as tarefas a serem executadas no build do projeto. E esse mesmo arquivo é escrito em JavaScript, então nada de XML ou sintaxes estranhas como em outras ferramentas. Esse arquivo é o **Gruntfile.js** que você cria na raiz do seu projeto.

A estrutura básica dele é essa:

\[javascript\] module.exports = function(grunt) { grunt.initConfig({ // configurações das tasks });

// carrega plugins grunt.loadNpmTasks('nome-do-plugin'); }; \[/javascript\]

Repare que é uma função JS que chama outras configurações. A `initConfig` é onde vamos colocar as regras das tarefas que queremos executar (onde indicamos, por exemplo, quais arquivos minificar). Repare que ele recebe um objeto JS, como num JSON. No final, chamamos a `loadNpmTasks` pra registrar um plugin do Grunt que vai trazer alguma funcionalidade.

## Minficando JS com Uglify

Vamos configurar a minificação de JavaScript usando o [UglifyJS](https://github.com/mishoo/UglifyJS). Registramos o plugin — chamado `grunt-contrib-uglify` usando `grunt.loadNpmTasks('grunt-contrib-uglify');`.

A configuração em si é bem simples: basta listar os arquivos a serem minficados (origem e destino):

\[javascript\] module.exports = function(grunt) { grunt.initConfig({ uglify: { 'build/home.js': 'src/home.js', 'build/main.js': 'src/main.js' } });

// carrega plugins grunt.loadNpmTasks('grunt-contrib-uglify'); }; \[/javascript\]

No exemplo, eu supus que você cria seus arquivos numa pasta **src/** e quer jogar as versões finais numa pasta **build/** (claro que você pode usar outros nomes). Daria também pra minificar o próprio arquivo sem copiá-lo pra uma pasta diferente, mas dificilmente você vai querer minificar seu próprio arquivo fonte.

Por fim, pra executar essa tarefa, basta rodar `grunt uglify` no terminal.

## Pré-processando CSS

Agora é muito fácil adicionar outros plugins e executar outras tarefas. Por exemplo, usando o **[grunt-contrib-less](https://github.com/gruntjs/grunt-contrib-less)**, podemos compilar um arquivo LESS pra CSS:

\[javascript\] less: { 'build/estilo.css': 'src/estilo.less' } \[/javascript\]

Pra rodar, basta executar `grunt less` no terminal. Pra compilar SASS ou Stylus, é praticamente a mesma coisa, só muda o nome do plugin.

Mais: se quiser concatenar os arquivos CSS num só, além de compilar todos os LESS, dá pra fazer isso listando os arquivos:

\[javascript\] less: { 'build/estilo.css': \['src/header.less', 'src/main.less', 'src/footer.less'\] } \[/javascript\]

Ou usar padrões nos nomes dos arquivos pra ficar mais fácil ainda:

\[javascript\] less: { 'build/estilo.css': \['src/\*.less'\] } \[/javascript\]

E isso vale, claro, pras outras tarefas também, como a do uglify que vimos antes. E estamos só vendo o início do Grunt. Dá pra fazer muito mais coisas nessas configurações, pra facilitar tudo mais ainda.

Aliás, um recurso que podemos fazer é simplificar a execução das tarefas pra não precisarmos chamar uma por uma na hora de rodar o comando. Podemos definir novas tarefas com `grunt.registerTask()`. Passamos o nome da nova task e uma lista de tasks a serem executadas:

\[javascript\] grunt.registerTask('default', \['uglify', 'less'\]); \[/javascript\]

A nova task **default** pode ser executada na linha de comando com `grunt default` e já vai rodar tanto o less quanto o uglify pra gente. Melhor ainda, esse nome _default_ é especial então podemos executar tudo só rodando `grunt` no terminal.

[O código final do exemplo está aqui](https://gist.github.com/sergiolopes/5752835).

## Mais tarefas

Há muitos outros plugins úteis no grunt. Pra compiladores de CSS, temos o [LESS](https://github.com/gruntjs/grunt-contrib-less), [Stylus](https://github.com/gruntjs/grunt-contrib-stylus) e [SASS](https://github.com/gruntjs/grunt-contrib-sass). Pra JS, temos também CoffeeScript. Pra minificar, temos o [uglify](https://github.com/gruntjs/grunt-contrib-uglify), o [cssmin](https://github.com/gruntjs/grunt-contrib-cssmin) e o [htmlmin](https://github.com/gruntjs/grunt-contrib-htmlmin). Temos ainda tarefas básicas como copiar arquivos ([copy](https://github.com/gruntjs/grunt-contrib-copy)), concatenação simples ([concat](https://github.com/gruntjs/grunt-contrib-concat)) e [clean](https://github.com/gruntjs/grunt-contrib-clean). E ainda o [watch com livereload](https://github.com/gruntjs/grunt-contrib-watch) para seu navegador atualizar automaticamente quando um arquivo for mudado.

Isso só falando de alguns dos plugins oficiais. Há uma [lista imensa de plugins](http://gruntjs.com/plugins) da comunidade pra fazer as mais variadas tarefas. Como o poderoso [grunticon](https://github.com/filamentgroup/grunticon) que gera uma sprite de arquivos SVG com fallback para PNGs.

## A instalação do Grunt.js

O Grunt é baseado no [Node.js](http://nodejs.org/) então você deve primeiro instalá-lo. No site já há um [botão de download](http://nodejs.org/) logo de cara ou você pode usar o instalador de pacotes do seu sistema operacional (apt-get no Linux, homebrew no Mac etc).

Depois que você instalou o Node, pode instalar as ferramentas de linha de comando do Grunt apenas executando no terminal: `npm install -g grunt-cli`

Próximo passo é configurar nosso projeto como um artefato do node para que possamos instalar os plugins e outras dependências. É bem fácil. Crie um arquivo **package.json** na raiz do seu projeto onde colocaremos configurações do projeto — o mínimo é o nome do projeto e um número de versão:

\[code\] { "name": "grunt-demo", "version": "0.0.1" } \[/code\]

Agora, a instalação do Grunt é feita de acordo com o projeto fazendo: `npm install grunt --save-dev`.

Cada plugin do grunt que você for usar, vai instalar da mesma forma:

\[code\] npm install grunt-contrib-uglify --save-dev npm install grunt-contrib-less --save-dev \[/code\]

Aí é só usar esses plugins no seu **Gruntfile.js** como vimos antes.

## Mais

Vimos uma introdução do grunt, suas ideias e alguns poucos recursos. O [site oficial](http://gruntjs.com/) deles tem muito mais documentação. Em português, há os excelentes artigos do [Zeno Rocha](http://zenorocha.com/automatizando-tarefas-js-com-grunt/) e do [Felipe Fialho](http://www.felipefialho.com/blog/2013/grunt-voce-deveria-estar-usando/).

O Grunt é uma ferramenta poderosa para automatizar tarefas de build. Tem bastante foco em tarefas de front-end, mas pode ser usado pra todo tipo de build. Aqui na Caelum mesmo, usamos para buildar a parte front-end de um projeto maior com Java no backend.
