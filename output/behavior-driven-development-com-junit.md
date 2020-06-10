---
title: "Behavior Driven Development com JUnit"
date: "2009-02-28"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[Behavior Driven Development](http://en.wikipedia.org/wiki/Behavior_Driven_Development) (BDD) é uma maneira de desenvolver software que, além de outras coisas, encoraja a escrita de testes mais parecidos com uma especificação, como sugerido pelo [Dan North](http://dannorth.net/introducing-bdd "Dan North"). A ideia é que seus testes descrevam o que um determinado módulo deveria fazer ou como uma determinada funcionalidade deveria funcionar. Algo como:

> Dado que eu estou na página principal, quando eu tento me logar, então meu nome aparece no canto da tela.

Eu, Cauê Guerra e Cecilia Fernandes estávamos começando um projeto aqui na Caelum e resolvemos usar BDD no desenvolvimento. Começamos, então, a procurar ferramentas que ajudam o desenvolvimento em BDD. Em Ruby existem o [Cucumber](http://cukes.info/ "Cucumber") e o [RSpec](http://rspec.info/ "RSpec"), em Groovy tem o [Easyb](http://easyb.org/ "easyb") e, em Java, o [JBehave](http://jbehave.org/ "JBehave"), apenas para citar os mais famosos. Começamos a testar essas opções, mas logo no começo surgiram alguns problemas:

- **Cucumber + RSpec e Easyb**: ambos eram muito parecidos em suas qualidades e defeitos: utilizam arquivos de texto para a descrição das histórias e são bem fáceis de escrever. No entanto, nenhum dos dois é feito em Java. Poderíamos usar, sem problemas, o JRuby e o próprio Groovy para acessar nosso código Java, mas por se tratar de um projeto open-source não queríamos obrigar futuros contribuidores a conhecer outras linguagens para começar a trabalhar em nosso projeto.Um outro problema foi que, usando o Eclipse como IDE, perderíamos o _code completion_ e a habilidade de rodar os testes pela IDE - seria preciso criar alguma _task ant_ ou um Rakefile para isso, rodando tudo pela linha de comando e tendo que interpretar os resultados obtidos fora da IDE.
- **JBehave**: o JBehave adiciona um pouco de complexidade para escrever os testes (anotações, arquivos de texto não compilados, algumas restrições sobre como as classes devem ser escritas), e achamos que o ganho que teríamos ao usar o JBehave não valeria a pena por causa dessa complexidade. Além disso, teríamos problemas para reaproveitar o código dos _steps_, dificultando a manutenção dos testes. Existem outros problemas, mas vamos deixar isso para outro post.

Daí nos questionamos: será que precisamos mesmo de alguma ferramenta especial para usar BDD? E a resposta que surgiu foi: _Não, podemos fazer tudo usando apenas o [JUnit](http://www.junit.org/ "JUnit")_! O bom e velho JUnit! Mas não bastava só usá-lo. Precisávamos de um jeito de organizar como uma especificação. Então pensamos e chegamos no seguinte padrão de testes:

\[java\] @Test public void nomeDoCenarioDeTestes() { given.algumContexto(); when.euExecutoAlgumaAcaoNoSistema(); then.algumaCondiçãoTemQueSerVerdadeira(); } \[/java\]

Por exemplo, usando o cenário que eu tinha escrito acima: \[java\] @Test public void login() { given.iAmOnTheMainPage(); when.iLoginAs("Lucas"); then.myNameAppearsOnTheScreen("Lucas"); } \[/java\]

Um código simples, que qualquer pessoa que sabe inglês entenderia, e que nos diz o que esperamos que aconteça quando alguém se loga no sistema. Mas o que exatamente são esses "_given_", "_when_" e "_then_"? São objetos simples, que não precisam implementar nenhuma interface nem estender classe alguma - vão apenas executar o que lhes foi pedido pelo teste.

Mesmo assim resolvemos usar uma convenção para dar o nome a esses objetos, de acordo com suas responsabilidades:

- **GivenSteps given** => objeto que vai preparar o contexto inicial do teste em questão. Ex: Entrar em uma página, inserir determinados objetos no banco, logar-se com um dado usuário, etc.
- **WhenSteps when** => objeto que vai executar as ações do teste em si, utilizando o contexto definido pelo objeto _given_. É a parte mais importante do teste. Ex: Preencher um formulário, clicar no botao _Enviar_, selecionar um item em alguma comboBox, etc.
- **ThenSteps then** => objeto que verifica se o resultado das ações executadas é o esperado. Ex: O usuário está logado? Apareceu a mensagem "_Inserido com sucesso_"? Deu erro de validação?

Para que os testes ficassem o mais limpo possível e o mais parecidos com um arquivo de texto descrevendo os cenários de uma história, separamos toda a configuração de testes e a criação dos nossos objetos _given_, _when_ e _then_ em uma classe abstrata que vai ser superclasse desses testes - que representam uma **história**. Também podemos colocar a descrição da história como comentário no começo do arquivo. Vejam um exemplo completo de um Cenário de teste, com todas as classes:

\[java\] /\*\* \* In order to use the system \* As a user \* I want to login \*/ public class LoginStory extends Story { @Test public void login() { given.iAmOnTheMainPage(); when.iLoginAs("Lucas"); then.myNameAppearsOnTheScreen("Lucas"); } }

public abstract class Story {

protected GivenSteps given; protected WhenSteps when; protected ThenSteps then;

@Before public void setUp() { Browser browser = createSeleniumDSL(); given = new GivenSteps(browser); when = new WhenSteps(browser); then = new ThenSteps(browser); } } public class GivenSteps {

private final Browser browser;

public GivenSteps(Browser browser) { this.browser = browser; }

public void iAmOnTheMainPage() { browser.open("index.jsp"); } }

public class WhenSteps {

private final Browser browser;

public WhenSteps(Browser browser) { this.browser = browser; }

public void iLoginAs(String login) { browser.form("login") .field("username").type(login) .field("password").type(login) .submit(); } }

public class ThenSteps {

private final Browser browser;

public ThenSteps(Browser browser) { this.browser = browser; }

public void myNameAppearsOnTheScreen(String login) { String div = browser.currentPage() .div("user").innerHTML(); Assert.assertThat(div, containsString(login)); Assert.assertThat(div, containsString("Logout")); } } \[/java\] O arquivo de teste mesmo fica bem limpo, apenas com o roteiro que o teste vai seguir, e o trabalho "sujo" da implementação fica nos _given_, _when_ e _then_, que podem ser reutilizados por qualquer teste. Esses objetos não precisam nem ser diferentes, não há necessidade de ter apenas um de cada na aplicação: você pode organizá-los da maneira que quiser. O importante é que no final o seu arquivo de teste fique limpo e escrito como se fosse uma especificaçao de uma funcionalidade ou a descrição de um cenário. Usando esse padrão fica fácil criar geradores de relatório, que analisam o arquivo da história e geram uma página HTML ou um arquivo de texto com as histórias que passaram e as que falharam.

Para conferir o resultado, você pode baixar o projeto que está usando essas idéias em: [http://github.com/caueguerra/calopsita](https://github.com/caelum/calopsita "https://github.com/caelum/calopsita").
