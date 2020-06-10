---
title: "O que a quantidade de asserts em um teste nos diz sobre o código?"
date: "2013-08-27"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Todo teste pode ser separado em três: a parte onde você monta o cenário, a parte onde você executa a ação que quer testar, e a parte que você valida que a ação aconteceu da maneira esperada. Por exemplo, veja o código abaixo:

\[java\] @Test public void deveCalcularNF() { // 1o: cenário NotaFiscal nf = new NotaFiscal("Mauricio", 2000.0);

// 2o: ação double imposto = nf.calculaImposto();

// 3o: validação assertEquals(250.0, imposto, 0.00001); } \[/java\]

A quantidade de "asserts" pode ser ilimitada. Podemos ter 1 (como no código acima), 2, 3, ou quantos mais precisarmos. Entretanto, quanto mais código em um método de teste, pior sua legibilidade. Portanto, é fácil perceber que, quanto mais asserts em um teste, mais difícil é lê-lo e entendê-lo.

Mas uma pergunta cuja resposta pode ser interessante é: será que o desenvolvedor foi "forçado" a escrever muitos asserts porque o código de produção que está sendo testado (em nosso caso, o `calculaImposto()`) não está bem feito?

Após executarmos um experimento controlado em alguns projetos de código aberto da Apache, concluímos que: **não, a baixa qualidade do código de produção não implica em um maior número de asserts por teste**.

Mas, durante o experimento, percebemos que contar a quantidade de asserts pode não ser uma boa ideia. Às vezes o desenvolvedor faz uso de mais de um assert porque o objeto foi modificado em mais de um atributo. Por exemplo:

\[code language="java"\] assertEquals(200.0, nf.getImpostoA(), 0.0001); assertEquals(250.0, nf.getImpostoB(), 0.0001); \[/code\]

Um outro desenvolvedor poderia ter implementado o teste, usando um _assertEquals()_ apenas, e comparando o objeto inteiro:

\[code language="java"\] assertEquals(nfEsperada, nf); \[/code\]

Ou seja, aqui é mais ou menos questão de gosto do desenvolvedor. Nesse momento, levantamos uma outra hipótese: talvez contar a quantidade de asserts não faça sentido, mas sim contar a quantidade de objetos diferentes que recebem asserts em um mesmo teste. Por exemplo, veja o código abaixo, onde temos 3 asserts, mas em 2 objetos diferentes:

\[code language="java"\] assertEquals(100.0, nf1.getImpostoA()); assertEquals(100.0, nf1.getImpostoB()); assertEquals(250.0, nf2.getImpostoA()); \[/code\]

Se verificarmos a relação entre a "quantidade de objetos que receberam asserts em um teste" e "qualidade do código de produção", descobrimos que: **sim, um código de produção problemático pode implicar em mais de um objeto recebendo assert no teste!**

Isso é interessante! Quando o código de produção é muito complicado, os desenvolvedores muitas vezes preferem escrever menos testes, mais complexos, e testar diferentes entradas. Isso serve como um ótimo feedback para você desenvolvedor: se você está fazendo asserções em mais de um objeto, olhe seu código de produção; ele pode ter problemas!

[Este trabalho](http://www.aniche.com.br/wp-content/uploads/2013/04/csmr2013-asserts.pdf) foi apresentado no [CSMR 2013](http://csmr2013.disi.unige.it/), em Gênova, e é uma pequena parte da minha pesquisa de doutorado. Muito do que aprendi está no [meu livro sobre TDD](http://www.tddnomundoreal.com.br) e nos [cursos de testes automatizados online](http://www.caelum.com.br/curso/online/testes-automatizados/).
