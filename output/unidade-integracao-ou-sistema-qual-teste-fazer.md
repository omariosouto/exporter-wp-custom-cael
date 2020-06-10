---
title: "Unidade, integração ou sistema? Qual teste fazer?"
date: "2014-07-01"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Uma confusão bastante comum na comunidade de desenvolvimento é justamente sobre qual nome dar para o tipo de teste. **Esse é um teste de unidade, integração ou sistema?** Apesar de parecer uma discussão boba, é importante que desenvolvedores usem os mesmos termos para se comunicar; isso facilita e acelera o entendimento.

Um **teste de unidade** é aquele que testa uma única unidade do sistema. Ele a testa de maneira isolada, geralmente simulando as prováveis dependências que aquela unidade tem. Em sistemas orientados a objetos, é comum que a unidade seja uma classe. Ou seja, quando queremos escrever testes de unidade para a classe _Pedido_, essa bateria de testes testará o funcionamento da classe _Pedido_, isolada, sem interações com outras classes.

Um **teste de integração** é aquele que testa a integração entre duas partes do seu sistema. Os testes que você escreve para a sua classe _PedidoDao_, por exemplo, onde seu teste vai até o banco de dados, é um teste de integração. Afinal, você está testando a integração do seu sistema com o sistema externo, que é o banco de dados. Testes que garantem que suas classes comunicam-se bem com serviços web, escrevem arquivos texto, ou mesmo mandam mensagens via socket são considerados testes de integração.

Já um **teste de sistema** garante que o sistema funciona como um todo. Este nível de teste está interessado se o sistema funciona como um todo, com todas as unidades trabalhando juntas. Ele é comumente chamado de teste de caixa preta, já que o sistema é testado "com tudo ligado": banco de dados, serviços web, batch jobs, e etc. Os **testes de aceitação**, famosos com a onda ágil, são, no fim, testes de sistema. Testes de aceitação são aqueles onde as equipes ágeis dizem se uma determinada funcionalidade está "aceita" ou não.

Independente do nível do teste, todos eles tem vantagens e desvantagens. Um teste de unidade, por exemplo, é bastante fácil de ser e roda muito fácil; mas não é um teste que simula bem o mundo real. Por outro lado, um teste de sistema faz uma simulação bastante real, mas é muito mais difícil de ser escrito, dá mais trabalho de manutenção e leva mais tempo para executar.

Mas qual nível de teste usar então? A ideia é que você escolha o nível de teste certo para aquele problema. Uma classe de negócio pode ser testada de maneira isolada; já um DAO precisa ser testado junto a um banco de dados. **Lembre-se: o teste deve dar feedback rico; um teste que nunca quebra não serve de nada.**

_Por curiosidade, é comum que as pessoas digam testes unitários ao invés de testes de unidade. Escrevi um e-mail para a ABL (Associação Brasileira de Letras) alguns anos atrás e a resposta foi que o termo correto seria "testes de unidade", mas como o termo "testes unitários" é bastante comum na área, ele também pode ser usado. Eu, particularmente, prefiro testes de unidade._

A discussão sobre testes e seus níveis vai longe. Discutimos muito isso em [nossos livros](http://www.casadocodigo.com.br), nos [cursos online de testes automatizados](http://www.alura.com.br/trilha/testes) do Alura e nos [nossos cursos](https://www.caelum.com.br/cursos-agile/). E você, usa alguma nomenclatura diferente nos seus testes?
