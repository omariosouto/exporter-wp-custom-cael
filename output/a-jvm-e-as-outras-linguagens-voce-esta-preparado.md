---
title: "A JVM e as outras linguagens: você está preparado?"
date: "2009-06-25"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um outro assunto que tem aparecido com cada vez mais frequência na lista interna da Caelum são as diversas linguagens que rodam sob a JVM. Sejam elas compiladas diratamente para bytecode Java, ou interpretadas através da Java Scripting API adicionada no Java 6.

O Fábio Kung fez no início do ano um acalorado post intitulado [2009 o ano do Ruby on Rails no Brasil](https://blog.caelum.com.br/2009-ano-do-ruby-on-rails-no-brasil/), e podemos ir além: diversas linguagens que não fazem faziam parte do _mainstream_ corporativo vem ganhando muita força por todos os lugares.

Martin Fowler fez um trabalho minucioso em suas considerações ao [uso de Ruby pela ThoughtWorks](http://martinfowler.com/articles/rubyAtThoughtWorks.html) ([traduzido pelo Fábio Akita aqui](http://www.akitaonrails.com/2009/06/15/tradu--o-ruby-na-thoughtworks)). Fowler discorre a respeito das opiniões e sentimentos dos lideres técnicos de cada projeto que optou por Ruby, e poucos deles (5 de 41) disseram que Ruby foi a escolha errada.

Mas será que apenas o Ruby tem ganho toda essa notoriedade e força?

O [Rafael Ferreira](http://blog.rafaelferreira.net/) compartilhou comigo recentemente um [excelente artigo que discute os diferentes paradigmas de programação](http://www.info.ucl.ac.be/~pvr/VanRoyChapter.pdf), citando vantagens e desvantagens, culminando na importância do aprendizado de diferentes linguages, em especial para tirar proveito das que facilitam o desenvolvimento de sistemas com muita concorrência.

O [Renato Lucindo](http://blog.lucindo.com.br/) me mostrou também o quão grande tem sido a repercussão do Scala em grandes ambientes, como é [esse caso da Électricité de France Trading que trocou 300 mil linhas de Java por Scala](http://www.scala-lang.org/node/2200).

Vale também citar a troca de linguagens num dos cursos mais famosos de computação do mundo: [o Structure and Interpretation of Computer Programs](http://mitpress.mit.edu/sicp/), curso que inicia os graduandos de ciência da computação e engenharia elétrica do MIT. Este curso foi sempre famoso por ser ministrado em Scheme, e agora depois de uma série de debates e justificativas, [foi reformulado usando Python](http://blog.snowtide.com/2009/03/24/why-mit-now-uses-python-instead-of-scheme-for-its-undergraduate-cs-program), novamente sem usar uma das linguagens _enterprisey_.

Pedro Matiello, que trabalha aqui com a gente, é o lider de desenvolvimento da biblioteca [python-graph](http://code.google.com/p/python-graph/), que implementa diversos algoritmos para grafos em python e possui [colaboração dos mais variados países](http://code.google.com/p/python-graph/wiki/Credits).

Aqui na Caelum, além de usarmos extensivamente Ruby e Rails em projetos e termos estendido o tempo do nosso [curso RR-11](http://www.caelum.com.br/curso/rr-11-ruby-on-rails/) para 32 horas ja há algum tempo, há um pedaço de um sistema desenvolvido em Scala e ainda temos o curso de [Lógica de Programação](http://www.caelum.com.br/curso/cs-01-logica-de-programacao/) é realizado em grande parte com Groovy.

São muitos meus amigos e colegas de trabalho estudando LISP, Erlang, OCaml, Scala e outras linguagens, sem contar Ruby/Rails e Python/Django. Todas essas linguagens podem de certa forma rodar sobre a JVM. É um lugar-comum (e é uma das dicas do excelente [Pragmatic Programmer](http://www.pragprog.com/ppbook/index.shtml)) dizer que devemos aprender mais linguanges de programação para ampliar nossa visão e formas de ataque a um problema. Já disse Peter Norvig que [é necessário 10 anos para que adquiramos fluência numa linguagem de programação](http://norvig.com/21-days.html), mas sempre há o momento de começar.

E você? Como está seu contato com essas linguagens? Sua empresa está usando algo "novo" em seus projetos?
