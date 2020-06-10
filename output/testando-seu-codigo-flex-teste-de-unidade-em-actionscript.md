---
title: "Flex: Teste de unidade em Actionscript"
date: "2010-04-05"
author: "david.paniz"
authorEmail: "davidpaniz@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

A comunidade Flex brasileira está cada vez maior, hoje temos 3 grandes grupos: [Flex-Brasil](http://br.groups.yahoo.com/group/flex-brasil/), [Flexdev](http://groups.google.com/group/flexdev) e o fórum [RIA - Flex, JavaFX e outros](http://guj.com.br/forums/show/24.java) no GUJ. Embora essas comunidades estejam cada vez maiores e mais ativas, ainda é raro encontrar discussões sobre testes. Justamente por ser um assunto não muito discutido, e dada a **extrema importância de testes em todos os nossos tiers**, vale fazer uma introdução ao [FlexUnit](http://flexunit.org/).

O Actionscript tem uma grande influência da linguagem java, e no seu framework de testes não é diferente. O **FlexUnit** tem muitas características vindas do [JUnit](http://www.junit.org/) na sua concepção.

Assim como no JUnit, no FlexUnit (versão 4) declaramos nossos testes através de metadados (equivalente as anotações do java). Temos o `Before` e o `After`, que serão executados antes e depois de todos os métodos de teste. Os metadados `BeforeClass` e `AfterClass` que serão executados uma única vez para todos os testes da classe, e finalmente o metadado `Test` que define que aquele método é um teste.

Lembrando que a utilização de metadados em Actionscript é um pouco diferente do java, sendo `[Test]` em vez de `@Test`, como vemos no trecho de código abaixo:

\[actionscript3\] \[Test\] public function doisMaisDoisDeveSerIgualAQuatro():void { Assert.assertEquals(4, 2 + 2); } \[/actionscript3\]

O FlexUnit, assim como o Cairgorm, o LiveCyle e algumas outras ferramentas para Flex, é um projeto opensource mantido pela Adobe e pode ser encontrado no [Adobe Labs](http://labs.adobe.com/). Se você já está usando o [Flash Builder 4](http://www.adobe.com/products/flashbuilder/), o FlexUnit já vem instalado e configurado.

Para exemplificar melhor imagine que puxamos de um serviço uma lista de `Nota`s de uma determinada turma e queremos gerar um gráfico mostrando qual é a **menor** nota, a **maior** nota e a **média** de notas da turma e para agrupar esses dados criamos a classe `ResultadoDeTurma`:

\[actionscript3\] public class Nota { public var nota:Number; public var nome:String; } \[/actionscript3\]

\[actionscript3\] public class ResultadoDeTurma { public var menorNota:Number; public var maiorNota:Number; public var media:Number; } \[/actionscript3\]

A classe responsável pelo agrupamento de dados é a `AgrupadorDeTurma` onde temos o seguinte código: \[actionscript3\] public class AgrupadorDeTurma { public function agrupa(notas:ArrayCollection):ResultadoDeTurma { var menor:Number = Number.MAX\_VALUE; var maior:Number = 0; var total:Number = 0; for each(var nota:Nota in notas) { if (nota.nota < menor){ menor = nota.nota; } else { if(nota.nota > maior){ maior = nota.nota; } } total += nota.nota; } return new ResultadoDeTurma(menor, maior, (total/notas.length)); } } \[/actionscript3\]

Agora vamos criar o nosso teste.

[![Criando um teste](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.00.30-PM-300x152.png "Criando um teste")](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.00.30-PM.png)

Nesse wizard criamos a classe `AgrupadorDeTurmaTeste`. O Flash Builder criará 2 arquivos, a classe de teste e um outro na raiz do projeto chamado `FlexUnitCompilerApplication.mxml`. Devemos executar esse arquivo para que ele crie um executor de testes como vemos na imagem abaixo.

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.04.10-PM-300x227.png "FlexUnit Compiler")](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.04.10-PM.png)

Ao término desse passo teremos o arquivo `FlexUnitApplication.mxml` que é usado para executar nossos testes. Agora que toda nossa "infra" está pronta, vamos escrever nosso primeiro método de teste. Para isso basta adicionar o método abaixo no arquivo `AgrupadorDeTurmaTeste.as`. \[actionscript3\] \[Test\] public function testandoComArrayComTresElementos():void { var notas:ArrayCollection = new ArrayCollection(\[ new Nota(9, "David Paniz"), new Nota(10, "Paulo Silveira"), new Nota(8, "Guilherme Silveira") \]); var resultado:ResultadoDeTurma = new AgrupadorDeTurma().agrupa(notas); assertEquals(10, resultado.maiorNota); assertEquals(8, resultado.menorNota); assertEquals(9, resultado.media); } \[/actionscript3\] Para executar os testes basta rodar o arquivo `FlexUnitApplication`.

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.20.57-PM-300x187.png "Execução dos testes no browser")](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.20.57-PM.png)

E no Eclipse temos o resultado:

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.21.24-PM-300x44.png "Execução no eclipse")](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.21.24-PM.png)

Quando o teste passa, o código está correto. Mas será que esse código realmente está correto? Será que só este teste basta para eu ter toda essa garantia? Vejamos, vou escrever outro teste para o mesmo método:

\[actionscript3\] \[Test\] public function testandoComArrayComUmElementos():void { var notas:ArrayCollection = new ArrayCollection(\[ new Nota(8, "Guilherme Silveira") \]); var resultado:ResultadoDeTurma = new AgrupadorDeTurma().agrupa(notas); assertEquals(8, resultado.maiorNota); assertEquals(8, resultado.menorNota); assertEquals(8, resultado.media); } \[/actionscript3\]

Ao executarmos nossos testes novamente temos o seguinte resultado:

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.32.25-PM-300x110.png "Resultado do segundo teste")](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-4.32.25-PM.png)

Acabamos de encontrar uma falha no nosso teste. Dentro da iteração de notas, não havíamos previsto que e mesma nota poderia ser a maior e a menor ao mesmo tempo. Para solucionar precisamos remover o `else` do `if`: \[actionscript3\] public function agrupa(notas:ArrayCollection):ResultadoDeTurma { var menor:Number = Number.MAX\_VALUE; var maior:Number = 0; var total:Number = 0; for each(var nota:Nota in notas) { if (nota.nota < menor){ menor = nota.nota; } if (nota.nota > maior){ maior = nota.nota; } total += nota.nota; } return new ResultadoDeTurma(menor, maior, (total/notas.length)); } \[/actionscript3\] E agora ao executarmos os testes temos o seguinte resultado:

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-6.10.37-PM-300x63.png "Testes corrigidos")](https://blog.caelum.com.br/wp-content/uploads/2010/04/Screen-shot-2010-04-05-at-6.10.37-PM.png)

Na verdade ainda temos outros possíveis problemas nesse código que poderiam ser testados, como por exemplo receber um array vazio como parâmetro. Tanto esse quanto o erro anterior teriam sido mais facilmente detectados caso usássemos TDD, criando nossos testes antes mesmo do código.

Outras ferramentas para conhecer sobre testes em Flex são o [Flexcover](http://code.google.com/p/flexcover/), [mock4as](http://code.google.com/p/mock4as/), [Flex Monkey](http://www.gorillalogic.com/flexmonkey) e o [Flash Selenium](http://code.google.com/p/flash-selenium/).
