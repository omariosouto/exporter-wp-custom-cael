---
title: "Testes de mutantes: quem vigia os vigilantes?"
date: "2016-08-17"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

> _Quis custodiet ipsos custodes? - Decimus Junius Juvelanis_

A frase acima, do poeta romano Juvelanis, é geralmente traduzida como: "_Quem vigia os vigilantes?_". O [contexto original](https://en.wikipedia.org/wiki/Quis_custodiet_ipsos_custodes%3F) era de conflitos conjugais, mas comumente é usada para questionar o abuso de poder e corrupção no Estado.

Mas o que isso tem a ver com desenvolvimento de software? Os vigilantes do nosso código são os testes automatizados. Mas **quem testa os testes automatizados?** Ou, de maneira mais clara: como saber se nossos testes estão realmente detectando falhas no nosso código?

## Um pequeno exemplo

Assuma que há uma classe `CalculadoraDeImposto` que, para `Produto`s com preço maior que `500`, calcula um imposto de `10%`. Produtos com preço inferior são isentos.

\[code language="java"\] public class CalculadoraDeImposto { private static final BigDecimal ALIQUOTA = new BigDecimal("0.10");

public BigDecimal calcula(Produto m) { if(m.getPreco().compareTo(new BigDecimal("500")) > 0) { return ALIQUOTA.multiply(m.getPreco()); } return BigDecimal.ZERO; } } \[/code\]

Criamos dois testes de unidade automatizados: um teste para um produto não isento e outro para um isento.

\[code language="java"\] public class CalculadoraDeImpostoTest { private CalculadoraDeImposto calculadoraCPMF;

@Before public void setUp(){ calculadoraCPMF = new CalculadoraDeImposto(); }

@Test public void precoAcimaDoLimite() { Produto iphone = new Produto(new BigDecimal("1000")); calculadoraCPMF.calcula(iphone); }

@Test public void produtoIsento() { Produto pendrive = new Produto(new BigDecimal("200")); calculadoraCPMF.calcula(pendrive); } } \[/code\]

Será que esses testes são suficientes? Estão testando todos os cenários relevantes? Capturam possíveis falhas ao modificarmos o código de produção?

## Cobertura descoberta

Uma ideia é rodar todos os testes automatizados, inspecionando a execução e marcando quais partes do código de produção são invocadas. Assim, podemos **listar os trechos que NÃO foram "exercitados" pela suíte de testes**.

Bibliotecas que implementam esse tipo de ideia são chamadas de **ferramentas de cobertura de código**. No mundo Java, temos ferramentas bastante usadas como [JaCoCo](http://www.jacoco.org/jacoco/), [Cobertura](http://cobertura.github.io/cobertura/) e [JCov](https://wiki.openjdk.java.net/display/CodeTools/jcov).

A ferramenta JaCoCo possui o [ECL Emma](http://www.eclemma.org/), um plugin do Eclipse, e oferece várias [medidas de cobertura](http://www.eclemma.org/jacoco/trunk/doc/counters.html): a % de classes (type coverage), a % de métodos (method coverage), a % de linhas de código (line coverage), a % de `if`s e `switch`s (branch coverage), e a % de instruções no byte code (instruction coverage) invocadas pelos testes. Além disso, há uma medida baseada na [complexidade ciclomática](https://blog.caelum.com.br/medindo-a-complexidade-do-seu-codigo/) (complexity coverage).

O que estamos verificando é apenas se algum teste invoca cada trecho do código de produção. Mas será o bastante para verificarmos a qualidade dos nossos testes?

Vejamos. Para nosso exemplo acima, a cobertura relatada pelo ECL Emma é **100%** para todas essas medidas de cobertura. Poxa, mandamos bem nos testes!

Mas, peraí... Repare novamente nos dois testes de unidade acima: **não há nenhum assert**!

## Mutantes: a evolução

Outra ideia é **inserir pequenos defeitos** propositalmente no nosso código de produção e executar a suíte de testes, observando se algum teste falha.

Cada defeito inserido é chamado de **mutação** e cada nova versão do código de produção que possui uma mutação é chamada de **mutante**. Se a algum teste falhar, o mutante é "morto". Mas se nenhum teste falhar, o mutante permanece "vivo". A % de mutantes mortos, que foram detectados por alguma falha nos testes, é uma medida da qualidade dos seus testes.

As bibliotecas que implementam essa ideia são chamadas de **ferramentas de testes de mutantes**. No mundo Java, temos ferramentas como [Jester](http://jester.sourceforge.net/), [Jumble](http://jumble.sourceforge.net/) e [muJava](https://github.com/jeffoffutt/muJava) e [PIT](http://pitest.org/), a mais ativamente desenvolvida.

O PIT possui uma [lista de **operadores de mutação**](http://pitest.org/quickstart/mutators/), que são templates de defeitos possíveis que podem ser inseridos no código de produção. Alguns exemplos:

- **Changed conditional boundary**: mudar `if(a < b)` para `if(a <= b)`
- **Negated conditional**: inverter `if(a == b)` para `if(a != b)`
- **Removed conditional**: Forçar `if(true)`
- **Removed call to method**: retirar chamada de algum método
- **Mutated return**: trocar retorno por `null`

Ao todo, há `25` operadores de mutação mas por padrão são executados apenas `7` desses. É possível configurar quais serão usados.

Esses operadores de mutação são aplicados ao código de produção através de ferramentas de manipulação de bytecode e a API de instrumentação do Java, gerando novas versões do código (os mutantes). Os testes que são afetados por um novo mutante são selecionados usando ferramentas de cobertura de código. Então, os testes são executados e são verificados os mutantes sobreviventes, que não ocasionaram falha em nenhum teste.

O PIT pode ser disparado pela linha de comando e também pelo Ant, Maven e Gradle. Há também plugins para Eclipse e IntelliJ. Além disso, existe um plugin que gera relatórios para o Sonar.

Executando nossos testes anteriores a partir do PIT, foram gerados 14 mutantes para as classes `CalculadoraDeImposto` e `Produto`. Desses, apenas 5 foram detectados (mortos) pelos testes e 9 sobreviveram. Chegamos a **`36%` de cobertura de mutantes**.

Veja o relatório de mutantes do PIT exibido no Eclipse:

![Relatório do PIT no Eclipse](https://blog.caelum.com.br/wp-content/uploads/2036/07/mutantes-2.png)

Há também um relatório que mostra os mutantes gerados a partir de cada linha do código de produção, destacando os mortos e sobreviventes. Abaixo, perceba que foram gerados vários mutantes para a classe `CalculadorDeImposto`. Para a linha `10`, que possui um `if`, foram gerados `7` mutantes, com `5` sobreviventes:

![SUmário dos mutantes para a classe CalculadoraDeImposto](https://blog.caelum.com.br/wp-content/uploads/2016/08/mutantes-sumario.png)

Para aumentarmos a cobertura de mutantes, teríamos de fazer asserts e, provavelmente, mais testes.

## Nem tudo é perfeito

Executei o PIT para alguns _projetos reais_ em uma máquina Core i7 com 16 GB de RAM.

O **projeto A**, um pequeno conversor de formatos de arquivos, possui apenas `30` classes de código de produção, com `352` linhas ao todo. Tem `126` testes de unidade, que foram executados pelo JUnit em `0,17`s. O ECL Emma calculou `81,2%` de line coverage e `80,6%` de branch coverage, levando `0,176`s.

Foram calculados 77% de cobertura de mutantes, gerados utilizando os `7` operadores de mutação padrão do PIT. A espera foi de `11`s ou **`62`x** os testes de cobertura. Com todos os `25` operadores de mutação do PIT, o resultado foi `75`%. A demora foi de `21`s, superando em **`119`x** o ECL Emma!

Já o **projeto B**, uma aplicação Web de médio porte com `689` classes de produção, totaliza `16435` linhas de código. Há, no mesmo _source folder_, `829` testes de unidade e de DAO que levaram `24,4`s para serem executados pelo JUnit. Os `39,1`% de line coverage e `30,8`% de branch coverage foram calculados pelo ECL Emma em `30`s.

Os `7` operadores de mutação padrão do PIT levaram a uma cobertura de mutantes de `14`%, tomando `33`min `39`s. São **`67`x** o tempo do ECL Emma. Quando utilizados todos os `25` operadores do PIT, o valor da cobertura de mutantes passou a `24`%. O interessante é que mais dos novos mutantes foram pegos pelos testes, aumentando a porcentagem. O tempo gasto foi de `2`h `43`min `2`s ou **`326`x** o tempo do ECL Emma!

Um outro **projeto C**, uma aplicação Web de maior porte, com `948` classes e `27552` linhas de código, tomou `12`min e `3`s para que o JUnit executasse seus `5074` testes de unidade e de DAOs. O ECL Emma calculou uma line coverage de `63,4`% e branch coverage de `57,7`% em `13`min `39`s. Pela proporção os mutantes padrão do PIT levariam de `14`h a `16`h e todos os mutantes, em torno de `3` dias! Confesso que não tive paciência de executar o PIT nesse projeto...

Perceba o sério problema: testes de mutantes **demoram muito**! Parece haver uma [explosão combinatorial](https://en.wikipedia.org/wiki/Combinatorial_explosion) à medida que temos mais operadores de mutação a serem aplicados, mais código de produção a ser mutado, mais testes de unidade/integração a serem executados.

A ideia de testes de mutantes [remete à decada de 70](https://en.wikipedia.org/wiki/Mutation_testing#Historical_overview), mas a complexidade computacional do problema fez com que só agora ferramentas reais possam ser usadas na prática. Mas, se for utilizá-las, prepare-se para [esperar](https://xkcd.com/303/)!
