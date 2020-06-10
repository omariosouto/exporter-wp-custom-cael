---
title: "Pequenos objetos imutáveis e Tiny Types"
date: "2009-07-21"
author: "jonas"
authorEmail: "jonas@vidageek.net"
main_guide: "main_guide"
main_category: "geral"
---

Uma das grandes preocupações que temos quando estamos desenvolvendo aqui na Caelum e nas nossas consultorias é como manter o código o mais expressivo possível. Expressividade está muito ligada a uma manutenabilidade maior do código, porque código mais fácil de entender costuma ter menos bugs. Uma das técnicas que usamos pra atingir esse objetivo são os **Tiny Types**.

Dêem uma olhada no código do seguinte método:

\[java\] public Projeto criaProjeto(String nomeProjeto, String descricaoProjeto) { // Executa a lógica de criação do projeto } \[/java\]

Digamos que esse seja um [factory method](http://en.wikipedia.org/wiki/Factory_method_pattern) para criação de `Projeto`. Ele seria usado da seguinte forma:

\[java\] new FabricaDeProjeto().criaProjeto("nome", "descrição"); \[/java\]

Mas nada impede que esse método seja chamado dessa forma:

\[java\] new FabricaDeProjeto().criaProjeto("descrição", "nome"); \[/java\]

Notaram a diferença sutil na ordem dos parâmetros? Isso vai acontecer? Provável. Imaginem o tempo gasto com debug para corrigir esse problema? Não seria melhor aproveitarmos a [tipagem explícita](http://en.wikipedia.org/wiki/Type_system#Explicit_or_implicit_declaration_and_inference) do Java para pegarmos esse problema em tempo de compilação? Ficaria assim:

\[java\] public Projeto criaProjeto(Nome nomeProjeto, Descricao descricaoProjeto) { // executa a lógica de criação do projeto } \[/java\]

Ganhamos checagem em tempo de compilação. Mas apenas isso? Vamos olhar como esse método seria usado:

\[java\] new FabricaDeProjeto() .criaProjeto(new Nome("nome"), new Descricao("descrição")); \[/java\]

O código ficou mais expressivo. Fica bem claro que estamos passando o `Nome` do projeto e não algo genérico. Com isso, fica muito mais difícil confundir o que passar em cada parâmetro. Estamos fazendo uso do sistema de tipos explicitos do java para evitar problemas. Além disso existem mais vantagens, embora um pouco mais sutis.

Com esse novo design do código, temos uma melhor divisão de responsabilidade. Por exemplo, não queremos permitir que o nome do projeto possua números. Como faríamos isso na primeira forma? Colocaríamos a lógica de validação dentro do método `criaProjeto`. Agora não precisamos fazer isso. Podemos colocar a lógica de validação dentro do objeto `Nome`, que é o objeto responsável por tudo relativo à `Nome`. Colocamos a funcionalidade no lugar onde ela deve ficar. Dividimos melhor a responsabilidade porque podemos adicionar métodos em `Nome`.

Mas temos um pouco mais de trabalho pra fazer isso, correto? Será que o código não vai ficar lento porque estamos criando vários objetos apenas para encapsular `String`s? Não. A forma como o [Garbage Colector](http://en.wikipedia.org/wiki/Garbage_collection_(computer_science)) trabalha, [coletando os objetos que devem ser mantidos em memória](http://java.dzone.com/articles/gc-goodstuff-collector) (e não o contrário) simplesmente não vai ser afetada pela criação dos novos objetos, que possuem vida bem curta.

Podemos ir ainda mais longe. Podemos fazer com que esses pequenos objetos sejam [imutáveis](http://en.wikipedia.org/wiki/Immutable_object). Se eles forem imutáveis, teremos diversos ganhos. Por exemplo, quem estiver manipulando esses pequenos objetos não precisará se preocupar com [concorrência](http://en.wikipedia.org/wiki/Concurrency_(computer_science)). Para objetos imutáveis, não faz diferença se eles estão em um ambiente concorrente ou não: são thread safe por natureza. Esse ganho é tão importante, que existem linguagens de programação onde você não pode criar "objetos" mutáveis, como [Erlang](http://erlang.org/). Além da thread safety, a outra principal vantagem é não termos de nos preocupar com que código de outras pessoas modifiquem nossos objetos, sofrendo efeitos colaterais por causa da invocação de um método e passagem deste objeto como parâmetro. Também não há como nossos objetos ficarem fora de um estado consistente.

Esses pequenos objetos imutáveis podem facilitar muito o desenvolvimento, prevenindo problemas e evitando que por preguiça separemos de forma errada as responsabilidades dos objetos. No começo pode parecer que estamos criando um monte de objetos a mais, mas na verdade não é bem assim. Como esses objetos pequenos possuem responsabilidades bem definidas, fica muito fácil reaproveitar. Imaginem quantos lugares precisam ter descrição? Usaremos o mesmo pequeno tipo. O gasto inicial que temos é bem recompensante conforme o tempo passa.
