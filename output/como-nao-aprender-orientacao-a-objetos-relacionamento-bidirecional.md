---
title: "Relacionamento bidirecional entre classes"
date: "2007-03-28"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Hoje estava pareando com o Lucas Cavalcanti, em um sistema de alunos para a Universidade de São Paulo. Lucas é o novo estagiário da Caelum, e o primeiro da parceria USP-Caelum, onde colocamos um aluno para trabalhar nos projetos da universidade, e ele é orientado, treinado e coordenado pela empresa.

Começamos um projeto novo, e nas primeiras classes de modelo do Hibernate tinhamos uma relação `OneToOne`. Muitas pessoas ficam em dúvida se devem fazer o relacionamento bidrecional, e isso é um ponto importante a ser considerado não apenas no uso do Hibernate, e sim em qualquer situação.

Para exemplificar, imagine a classe `Carro` e a classe `Motor`, sendo que um `Carro` tem um e apenas um `Motor`, e o `Motor` pertence a um único `Carro`.

Como modelar essas classes? Devemos colocar os atributos correspondentes nas duas classes (relacionamento bidirecional)? Apenas `Carro` tem de saber qual é o seu `Motor`? Ou é o `Motor` quem deve saber sobre o seu `Carro`? Vamos ver o código no caso de optarmos pelo relacionamento bidirecional:

\[java\] Motor m = new Motor(); Carro c = new Carro(); c.setMotor(m); m.setCarro(c); // hummm.... \[/java\]

Repare que se não tivessemos `m.setCarro(c)` cairíamos em um estado inconsistente para o nosso modelo. Poderia ser ainda pior: imagine alterar o `Motor m` do nosso `Carro c` criados anteriormente:

\[java\] Motor outroMotor = new Motor(); c.setMotor(outroMotor); // e o Motor m? continua apontando para o Carro c \[/java\] Agora o antigo `Motor m` ficou referenciando a um `Carro` que não o contem, e o `outroMotor` aponta para `Carro` algum (`null`)! Claro que temos como tentar nos precaver dessa situação, mas sempre teremos um código mais complicado e muito sucetível a deixar o estado de seus objetos incosistente.

Imagine agora se essas classes são entidades do Hibernate. Se elas não estiverem concordando a respeito do relacionamento, que lado vai ser considerado na hora de persistir os dados? Quem manda, o `Carro` ou o `Motor`? Ou deve ser lançada uma exceção? É para isso que temos os relacionamentos marcados como `inverse` (os que são `mappedBy` nas anotações): [esse lado do relacionamento não é considerado para a atualização](http://docs.jboss.org/hibernate/core/3.3/reference/en/html/collections.html#collections-bidirectional).

Isso tudo pode ser muito mais complicado em relacionamentos 1:N e N:M. O conselho é tentar evitar o relacionamento bidirecional, e **nunca** cria-lo sem uma real necessidade, assim como já comentamos sobre [evitar herança](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/) e [evitar getters e setters](https://blog.caelum.com.br/nao-aprender-oo-getters-e-setters/).

[Algumas pessoas](http://www.cs.jyu.fi/~sakkinen/wishes-LMO2005.pdf) sugerem que as novas linguagens OO deviam suportar esse tipo de auto ajuste da outra ponta do relacionamento, usando sintaxes novas. O quão dependente suas classes são uma das outras, e quanto mais ciclos elas formam, geram um número que é usado como [métrica para avaliar a qualidade de um projeto orientado a objetos](http://www.objectmentor.com/resources/articles/oodmetrc.pdf). O plugin do Eclipse [ByeCycle](http://byecycle.sourceforge.net/) mostra o grafo de relacionamento entre suas classes. Se houver um ciclo, é um sinal de perigo.

No fim da noite terminamos algumas entidades do hibernate e algumas queries do DAO, e os respectivos testes unitários através de um banco [H2](http://www.h2database.com/) criado e derrubado [em memória](http://www.h2database.com/html/features.html#connection_modes) para cada teste. Foi extremamente produtivo e interessante.
