---
title: "Muitos parâmetros? Named Parameters em Ruby e Builders em Java"
date: "2012-12-03"
author: "Hugo Roque"
authorEmail: "hugolnx@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Pode ser difícil ler uma invocação de método ou de construtor quando temos muitos parâmetros. Isso piora se tivermos parâmetros do mesmo tipo. Um exemplo em Ruby:

\[code language="ruby"\] Titular.new("Hugo Roque", "Josemar Nunes", "Antonio") \[/code\]

O que esse construtor recebe como parâmetros? O que esses nomes representam? Lendo simplesmente dessa forma, só podemos chutar. Há muitas formas de atenuar esse problema. Uma das soluções seria [utilizar tinytypes](https://blog.caelum.com.br/pequenos-objetos-imutaveis-e-tiny-types/), criando classes, como `Dependente`, que possuem um ou pouquíssimos atributos, servindo mais como uma forma de tipificar e explicitar com o que trabalhamos, evitando a "programação orientada a strings". Em Java é comum utilizarmos o [design pattern]( http://www.caelum.com.br/curso/online/design-patterns/) Builder, evitando construtores gigantes e complexos. Dessa forma o código acima seria algo como:

\[code language="java"\] Titular hugo = new TitularBuilder() .nome("Hugo Roque") .dependentes("Josemar Nunes", "Antonia Roque") .build(); \[/code\]

Em Ruby, é comum passarmos hashes como argumento, inclusive muitas gems que usamos no dia a dia utilizam essa técnica. Se estivermos usando o ActiveRecord e precisarmos procurar os 5 primeiros usuários por ordem de criação, podemos utilizar o seguinte código:

\[code language="ruby"\] Usuario.find(:all, order: "created\_at DESC", offset: 1) \[/code\]

Já no caso do nosso Titular, a chamada seria feita da seguinte maneira:

\[code language="ruby"\] hugo = Titular.new(nome: "Hugo Roque", dependentes: \["Josemar Nunes", "Antonia Roque"\]) \[/code\]

Que já melhora bastante a leitura na chamada do método, mas piora a legibilidade do código que define o método:

\[code language="ruby"\] class Titular attr\_reader :nome, :dependentes

def initialize(options={}) @nome = options\[:nome\] @dependentes = options\[:dependentes\] end end \[/code\]

Observe que não está claro na assinatura do método as informações que ele precisa, teremos que olhar a implementação para descobrir o que receberemos na hash. Além da expressividade, podemos deixar de passar um parâmetro e nada irá nos alertar, todos os parâmetros se tornam opcionais. O [Ruby 2.0](http://www.youtube.com/watch?v=t9LMOydfc4k)promete uma forma nativa de nomear os parâmetros, facilitando tanto a leitura quanto a escrita das invocações. Veja uma possível invocação que poderemos fazer no futuro:

\[code language="ruby"\] class Titular def initialize(nome: "padrao", dependentes: \[\]) @nome = nome @dependentes = dependentes end end hugo = Titular.new(nome: "Hugo Roque", dependentes: \["Josemar Nunes", "Antonia Roque"\]) \[/code\]

Isso trará muitas vantagens para a forma como desenvolveremos, porém a versão 2.0 do Ruby tem previsão de lançamento para [fevereiro de 2013](http://www.rubyinside.com/ruby-2-0-release-schedule-announced-roll-on-2013-5536.html). Enquanto essa novidade não chega, algumas soluções provisórias foram desenvolvidas, eu mesmo implementei a [gem named-parameter](https://github.com/hugolnx/named_parameter)que disponibiliza uma forma de simular esses parâmetros nomeados de forma limpa e simples. Através da gem, invocamos o método como se estivéssemos recebendo uma hash, mas definimos da seguinte maneira:

\[code language="ruby"\] class Titular extend NamedParameter

named def initialize(nome, dependentes) @nome = nome @dependentes = dependentes end end \[/code\]

E você, como evita uma grande quantidade de parâmetros?
