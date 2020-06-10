---
title: "Mais uma vez... TDD não é bala de prata!"
date: "2014-04-24"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Essa semana, o DHH, o famoso criador do Ruby on Rails, fez um [post polêmico](http://david.heinemeierhansson.com/2014/tdd-is-dead-long-live-testing.html ) sobre o assunto, entitulado _"TDD is Dead, Long Live Testing",_ que em uma tradução livre, é algo como _"TDD está morto, Vida Longa ao Teste"._ Nele, DHH afirma que não pratica mais TDD, que acredita que a prática mais atrapalha do que ajuda seu dia a dia como desenvolvedor. Para entender melhor os argumentos dele, sugiro que você leia o post original.

É bem difícil discutir sobre opiniões de autores que afirmam categoricamente _"nunca use X"_ ou _"sempre use Y"._ Acho que a experiência já me ensinou (e ensinou você) que **sempre** e **nunca** são palavras que devem ser evitadas ao máximo em engenharia de software.

Qualquer um que desenvolve software sabe que deve buscar por duas coisas: (i) uma boa bateria de testes, afinal, precisamos garantir que o sistema funcione; (ii) um bom projeto de classes, afinal, precisamos de um código de qualidade.

E por que praticamos TDD, afinal? Justamente porque acreditamos que TDD nos ajuda a alcançar esses dois pontos. Para muitos desenvolvedores, escrever os testes antes os ajudam a pensar em mais cenários para o teste. Para outros, o teste é uma excelente folha de rascunho na qual eles pensam melhor sobre o projeto de classes. Entretanto, alguns desenvolvedores conseguem escrever melhor seus testes depois que já escreveram o código. Outros pensam melhor no projeto de classes rabiscando em um papel. **Você, desenvolvedor, deve obviamente escolher a prática que melhor o ajuda a alcançar esses pontos.** 

Eu, por exemplo, faço parte do grupo de desenvolvedores que usam TDD para essas duas coisas. Isso também acontece com muitos desenvolvedores na Caelum. Em meus trabalhos, inclusive, cito muitas maneiras na qual a prática de TDD ajuda. O quê olhar no teste para saber que seu código tem problemas de acoplamento ou coesão, como refatorar sua bateria de testes, e etc. É claro que eu não uso TDD 100% do tempo, eu o alterno com o uso de outras práticas (gosto muito de rabiscar em um papel para pensar no projeto de classes, e com frequência, escrevo testes depois da implementação). Mas ainda sim, muitas vezes ela é a melhor maneira que encontro para me ajudar.

No post, ele também usa o fraco argumento de que TDD o fazia mockar ActiveRecord, e que hoje ele prefere testes que integram (com banco de dados, IO, e etc). Essa discussão existe [no meu livro](http://www.tddnomundoreal.com.br), e o resumo dela é: se o objetivo da sua classe é interagir com um sistema externo (como é o caso de um DAO, ou dos métodos Active Record das entidades no Rails), o melhor teste para ela, é aquele que se comunica com esse sistema; não faz sentido simular o banco de dados para testar um método que só serve para comunicar com o banco.

Não me incomoda o DHH (ou qualquer outro desenvolvedor) dizer que não usa TDD. Mas, do meu ponto de vista, a grande falha no post é que ele não cogitou a possibilidade da prática ser a maneira mais produtiva para alguns desenvolvedores; não para todos, claro, mas sim, para alguns.

**TDD tem que morrer! TDD tem que viver! Tudo depende do quanto você é um desenvolvedor melhor com ele.**
