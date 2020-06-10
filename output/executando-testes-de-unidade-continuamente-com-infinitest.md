---
title: "Executando testes de unidade continuamente com Infinitest"
date: "2016-07-25"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Testes automatizados são muito importantes. Quando [bem feitos](https://blog.caelum.com.br/facilitando-a-manutencao-dos-testes-ao-diminuir-o-acoplamento-com-o-codigo/) e em número suficiente, passamos a ter uma garantia de que nosso código faz o que deve (e continua fazendo depois de alterações).

Além disso, há uma [forte influência](https://blog.caelum.com.br/testes-sao-mais-do-que-regressao-os-beneficios-no-design/) na direção de uma boa modelagem, ainda mais quando usamos [TDD](https://blog.caelum.com.br/tdd-e-sua-influencia-no-acoplamento-e-coesao/). Ao aplicarmos o ciclo de [Falhar-Passar-Refatorar](http://tdd.caelum.com.br/) do TDD, passamos menos tempo debugando e encontramos bugs o mais cedo o possível.

Porém, para colhermos as vantagens dessas técnicas, precisamos **lembrar de executar nossa suíte de testes DEPOIS DE QUALQUER ALTERAÇÃO**. Esperarmos até o [servidor de integração contínua](https://blog.caelum.com.br/integracao-continua-de-projeto-java-com-jenkins/) avisar-nos é tarde demais. E como costumamos dizer aqui na Caelum: _se o programador precisar de lembrar, pode ter certeza que ele vai esquecer_.

Quando demoramos para executar nossos testes, mais erros podem acumular-se. Ao descobrirmos as falhas nos testes, o contexto do que estávamos fazendo não estará mais tão claro. É provável que passemos mais tempo debugando e "caçando" a raiz dos defeitos.

## Testando a cada alteração

Para evitar que o programador precise lembrar de executar a suíte de testes sempre que alterar algum código, foram criadas as **ferramentas de teste contínuo**. Apenas aqueles testes que foram afetados pelo código modificado serão executados, de maneira a minimizar o tempo de execução dos testes. Em Ruby há ferramentas como o [watchr](https://github.com/mynyml/watchr), em Python temos o [sniffer](https://github.com/jeffh/sniffer) e em Java temos o [Infinitest](https://infinitest.github.io/).

O Infinitest tem plugins para o Eclipse e IntelliJ. Observe a execução em um projeto no Eclipse:

[![Infinitest executa testes automaticamente](https://blog.caelum.com.br/wp-content/uploads/2016/07/infinitest.gif)](https://blog.caelum.com.br/wp-content/uploads/2016/07/infinitest.gif)

Perceba que ao alterar a classe `GeradorDeRecibo`, somando 1 ao total, o canto inferior da tela passa a ficar vermelho, sinalizando que algum teste quebrou. É exibido também o número de testes executados (_1 test cases ran_). Só um teste foi executado, apesar de existirem outros no projeto.

A classe `GeradorDeReciboTest` é marcada com um erro exatamente na linha onde ocorreu a falha no teste. O detalhe do erro mostra que era esperado um `2.0` como total, mas veio `3.0`.

## Dica: execute apenas os testes de unidade com o Infinitest

Logo a recompilação do código pelo Eclipse, o Infinitest analisa as mudanças e executa apenas os testes afetados. Porém, existem diferentes níveis de testes: [unidade, integração e sistema](https://blog.caelum.com.br/unidade-integracao-ou-sistema-qual-teste-fazer/). Testes de unidade são rápidos: uma suíte completa é executada em alguns segundos. Já os de integração e sistema são mais lentos, porque acabam testando mais coisas.

Por isso, é ideal configurar o Infinitest para executar apenas os testes de unidade. Para isso, crie um arquivo `infinitest.filters` na raiz do seu projeto e coloque expressões regulares que peguem o nome das classes que você quer excluir.

Por exemplo, se você quiser excluir todos os testes de DAOs, exclua o pacote com: `br\.com\.caelum\.dao\..*`.

Se desejar excluir todas as classes terminadas com ITest (padrão usado pelo plugin `failsafe` do Maven), coloque: `.*ITest`.

## Dica: é bom mesmo ao refatorar

Ao criar novos testes (e novas funcionalidades), tenho a tendência a ignorar os testes vermelhos. Talvez o ideal seja deixar seus [testes quebrados o mínimo possível](https://www.infoq.com/presentations/The-Limited-Red-Society). Mas confesso que não tenho essa disciplina.

Agora, **ao refatorar, seus testes nunca deveriam quebrar**. Tudo deve ficar sempre verde. Nesses casos, executar continuamente seus testes de unidade vale muito a pena!
