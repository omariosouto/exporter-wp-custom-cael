---
title: "Não faça testes, faça specs!"
date: "2017-08-09"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Como foi difícil escrever este artigo, ou melhor, esta série que iremos lançar. Eu sou desenvolvedor Java, demorei anos para entender todo parte do ecossistema que envolve a plataforma, o que cada framework faz, como é sua arquitetura, onde e como deveria ser usado e como googlar determinado problema. Lembro até hoje a alegria que tive ao configurar um projeto Struts + JPA usando [Maven](https://blog.caelum.com.br/processo-de-build-com-o-maven/) (até que enfim tinha entendido esse tal de gerenciador de dependências).

Enfim, você não sabe o quanto eu caminhei para chegar até aqui, para simplesmente ....... ter que reaprender tudo de novo!!! [É isso mesmo amigos, bem-vindo ao maravilhoso reino encantando do JavaScript](http://hipsters.tech/o-reino-encantado-do-javascript-hipsters-38/).

Curto e grosso amigo desenvolvedor: você tem um monte de nomes para aprender desse novo mundo, [muitos nomes](https://blog.caelum.com.br/?s=javascript) (node, npm, gulp, grunt, jasmine, typescript, react, webpack, babel, expressjs, bower, ...). É o tal do mundo [front-end](https://blog.caelum.com.br/category/front-end/)! E pode ter certeza que amanhã haverá mais frameworks / bibliotecas.

Para essa série, chamei Tiago Lage para ser coautor, que já estava nesse mundo antes de ser modinha e tentava, sem sucesso (por culpa minha), explicar-me [features](https://blog.caelum.com.br/as-multiplas-personalidades-do-this-em-javascript/) legais do javascript como [hoisting](https://dev.to/imwiss/understanding-hoisting-in-javascript).

Depois de um tempo batalhando lado a lado com frameworks para desenvolver algo minimamente decente, descobri que o mais importante disso tudo não é o que está no meu src/main/java, **mas sim o que está no [src/test/java](https://blog.caelum.com.br/unidade-integracao-ou-sistema-qual-teste-fazer/).** O seu código está ruim? [Tem testes](http://hipsters.tech/testes-automatizados-hipsters-51/)? Ok, depois alguém refatora. [Mas não deixe de fazer testes!!!](https://www.youtube.com/watch?v=m_6nM1q7nz8) Aliás, a não ser que você [trabalhe no StackOverflow](https://hipsters.tech/tecnologias-na-stackoverflow-hipsters-46/) e tenha usuários working for free legais que forneçam feedback exclusivo!

E como lidar com testes especificações em JavaScript? Bom, durante a série de artigos iremos testar diferentes códigos javascript e frameworks (jQuery, Vue, Angular), obtendo cobertura de código, usando mocks em funções e até mesmo no html. Vamos ver até onde isso vai....

## Entendendo as especificações

Imagine por um momento que você está trabalhando em um projeto que teve a especificação perfeita, ou seja, todos os comportamentos esperados do sistema estão detalhadamente descritos, cada regra de negócio, cada input válido ou inválido, cada tela e mensagem de erro. Imaginou? Agora imagine que o computador possa entender essa especificação "perfeita". Poderíamos dizer então que se o computador pudesse validar todas estas especificações, então nosso sistema está correto, não é mesmo?

Essa modinha tendência começou a se difundir com o Ruby e o framework [RSpec](http://rspec.info/) que prometia trazer o [Behaviour Driven Developmen](https://blog.caelum.com.br/behavior-driven-development-com-junit/)t (Desenvolvimento Guiado por Comportamento) para o Ruby.

Entretanto, como todos sabem, os requisitos nunca são perfeitos e também não são escritos em uma linguagem de máquina. Portanto, o que podemos fazer para que essa estratégia funcione? Bom, podemos pegar os requisitos "reais" (descritos em um documento em linguagem de negócios), aperfeiçoá-los e traduzí-los para uma linguagem de máquina. A boa notícia é que grande parte do ferramental já existe na maioria das linguagens.

Bom, mas o que se ganho com toda essa trabalheira? Ela irá permitir utilizar [Test Driven Development  - TDD](http://tdd.caelum.com.br/) (Desenvolvimento guiado por testes). Desse modo, nosso projeto real começa com todos os testes falhando, à medida que desenvolvemos suas funcionalidades, os testes começam a passar. Assim, quando todos os testes estiverem passando, dizemos que nosso projeto está completo.

## First things first

A primeira atitude que precisamos tomar para começar a fazer testes em JavaScript é escolher um Framework. O mundo Java se divide em Team [JUnit](http://junit.org/junit4/) e Team [TestNG.](http://testng.org/doc/) Há uma boa comparação [aqui](http://www.mkyong.com/unittest/junit-4-vs-testng-comparison/). No mundo JavaScript, a briga maior é entre o Team [Jasmine](https://jasmine.github.io/) e o Team [Mocha.js](https://mochajs.org/) (boa comparação [aqui](https://marcofranssen.nl/jasmine-vs-mocha/) também). Desconsiderando as diferenças óbvias de linguagem, há também uma grande diferença conceitual na forma de abordagem dos testes entre o pessoal do Java e o pessoal do JavaScript. No mundo Java, [em geral](https://blog.caelum.com.br/behavior-driven-development-com-junit/), os frameworks de testes seguem mais a filosofia do teste unitário de unidade, e no mundo JavaScript é mais comum a terminologia do [Behavior Driven Design (BDD)](https://pt.wikipedia.org/wiki/Behavior_Driven_Development).

Segue exemplo de teste em Java (JUnit4). Veja que utilizamos anotações sobre os métodos para definir qual a função dos mesmos no ciclo de vida do nosso teste:

Suite de testes - AllTests:

[https://github.com/tiagolpadua/MinhaCalculadoraJava](https://github.com/tiagolpadua/MinhaCalculadoraJava)

\[code language="java"\] import org.junit.runner.RunWith; import org.junit.runners.Suite; import org.junit.runners.Suite.SuiteClasses;

@RunWith(Suite.class) @SuiteClasses({ CalculadoraTest.class }) public class AllTests { } \[/code\]

Casos de teste - CalculadoraTest:

\[code language="java"\] import static org.junit.Assert.assertEquals; import org.junit.After; import org.junit.AfterClass; import org.junit.Before; import org.junit.BeforeClass; import org.junit.Test;

public class CalculadoraTest {

@BeforeClass public static void runOnceBeforeClass() { System.out.println("@BeforeClass- executa uma vez antes de todos testes"); }

@AfterClass public static void runOnceAfterClass() { System.out.println("@AfterClass - executa uma vez após todos testes"); }

@Before public void runBeforeTestMethod() { System.out.println("@Before - executa antes de cada teste"); }

@After public void runAfterTestMethod() { System.out.println("@After - executa após cada teste"); }

@Test public void testSoma() { Calculadora calculadora = new Calculadora(); assertEquals(3, calculadora.somar(2, 1)); System.out.println("@Test - testSoma"); }

@Test public void testIncremento() { System.out.println("@Test - testIncremento"); Calculadora calculadora = new Calculadora(); assertEquals(3, calculadora.incrementar(2)); } } \[/code\]

Bom, mas isso aí talvez você já [saiba de cor e salteado](https://www.gramatica.net.br/origem-das-palavras/origem-da-expressao-saber-de-cor/), mas e como é no mundo JavaScript?

O JavaScript não tem o recurso de anotações como o Java, nesse caso, segue o exemplo abaixo em JavaScript utilizando o framework Jasmine, que disponibiliza algumas funções para controlar o ciclo de vida dos testes e [asserções](https://github.com/JamieMason/Jasmine-Matchers) sobre os resultados:

\[code language="js"\] describe('Calculadora', function () {

beforeAll(function () { console.log('beforeAll - executa uma vez antes de todos testes'); });

afterAll(function () { console.log('afterAll - executa uma vez após todos testes'); });

beforeEach(function () { console.log('beforeEach - executa antes de cada teste'); });

afterEach(function () { console.log('afterEach - executa após cada teste'); });

it('Deve somar os números', function () { console.log('teste calc.somar'); var calc = new Calculadora(); expect(calc.somar(2, 1)).toBe(3); });

it('Deve incrementar um número', function () { console.log('teste calc.incrementar'); var calc = new Calculadora(); expect(calc.incrementar(2)).toBe(3); }); }); \[/code\]

No fim as funcionalidades são muito parecidas. Vamos fazer um paralelo dos métodos entre os mundos do Java e do JavaScript pra ficar mais claro:

**Java (JUnit)**

**JavaScript (Jasmine)**

Suite

describe

BeforeClass

beforeAll

AfterClass

afterAll

Before

beforeEach

After

afterEach

test

it

assert

expect

 

## O que vem por aí

Finalizando, este foi o primeiro artigo de uma série bem bacana que estamos elaborando (for dummies and ninjas). Nos próximos artigos falaremos sobre Karma, PhantomJS, Matchers, testes de unidade, integração e sistema, e até mesmo, como testar seu HTML que ainda nem existe. Também abordaremos testes mais complexos envolvendo o conceito de [Mocks e Spys](https://blog.caelum.com.br/facilitando-seus-testes-de-unidade-no-java-um-pouco-de-mockito/).

Por fim, falaremos sobre como medir cobertura de código e como integrar todo esse ferramental no build da sua aplicação em um servidor de integração contínua com a ajuda de [Gulp](https://blog.caelum.com.br/bye-bye-grunt-js-hello-gulp-js/) e [Grunt](https://blog.caelum.com.br/automacao-de-build-de-front-end-com-grunt-js/). Se você é iniciante como eu ou ninja como o Tiago, comenta sua experiência nesse mundo [complicado](https://www.bennadel.com/resources/uploads/2013/feelings_about_angularjs_over_time.png) desafiador JavaScript. Ademais, você também pode acompanhar o código que estamos elaborando para os próximos posts no [meu GitHub](https://github.com/raphaelLacerda/aulas-caelum/tree/master/testes-javascript). Comenta aí!
