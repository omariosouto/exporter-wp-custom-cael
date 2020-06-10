---
title: "Facilitando seus testes de unidade no Java: um pouco de Mockito"
date: "2011-11-10"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Após um bom tempo de aulas ministradas, encontrei uma linha de aprendizagem que acho interessante para chegar até as consideradas boas práticas. A linha é formada pelos conceitos básicos de Orientação a Objetos, Testes, Injeção de Dependências, Programação orientada a interfaces e Mocks. Obviamente há uma interdependência dos tópicos. Há espaço aí para separação de responsabilidades com aspectos, além de design patterns.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/10/mockito.jpg "mockito")](https://blog.caelum.com.br/wp-content/uploads/2011/10/mockito.jpg)

Não é a toa que mocks sejam complicados para quem está começando, ele é totalmente obscuro (experiência própria), além de que, dependendo das unidades, [é possível testá-las sem mocks](http://www.caelum.com.br/curso/fj-16-laboratorio-java-testes-xml-design-patterns/). Portanto, é mais interessante lidar com outros conceitos, como injeção de dependências, para só depois partir para Mocks.

O artigo não tem como objetivo explicar o conceito de Mocks, mas sim explicar a aplicabilidade do [framework de testes Mockito](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#13) nos seus projetos. Se você está começando agora, uma boa dica é procurar as edições da MundoJ ([em quase toda edição há artigos relacionados a testes e consequentemente mocks](http://mundoj.com.br/46conteudo.shtml)) e depois ler o [artigo do Martin Fowler](http://martinfowler.com/articles/mocksArentStubs.html).

Mesmo sem conhecer Mocks, lanço um desafio. Continue lendo o artigo e ao final sentirá quanto absorveu do conteúdo. Certamente terá dificuldades. Faça o bookmark do post, volte aos seus estudos, pratique e retorne depois. Ao reler o texto, perceberá o quanto evoluiu, resultado direto de seus estudos.

Então vamos ao código. Temos uma classe `FuncionarioDAO` e um método `buscarFuncionario`. No nosso exemplo, para buscar o `Funcionario`, o sistema deve se comunicar com um mainframe por meio de uma interface `Transacao`. Mas perceba aqui que poderia ser uma interface EJB comunicando-se remotamente ou até mesmo uma transação simples JDBC. Essa transação mainframe retorna uma `String` contendo as informações do usuário, separada por colunas. Então o que devemos fazer é uma lógica para montar um `Funcionario` a partir dessa resposta. Começando pelos testes, teríamos o seguinte:

\[java\] class FuncionarioDAOTest {

private FuncionarioDAO funcionarioDAO;

@Mock private Transacao transacao;

@Before public void init(){ MockitoAnnotations.initMocks(this); funcionarioDAO = new FuncionarioDAO(transacao); }

@Test public void quandoUmUsuarioValidoForPesquisado(){ when(transacao.executar("12345")).thenReturn("RAPHAEL   12345 2045 "); Funcionario funci = funcionarioDAO.buscarFuncionario("12345"); Assert.assertEquals("RAPHAEL", funci.getNome()); Assert.assertEquals(2045, funci.getSetor()); Assert.assertEquals("12345", funci.getMatricula()); verify(transacao, atMostOnce()).executar("12345"); }

@Test(expected=UsuarioInexistenteException.class) public void quandoUmUsuarioInexistenteForPesquisado(){ when(transacao.executar("123")).thenThrow(new TransacaoOnlineException()); Funcionario funci = funcionarioDAO.buscarFuncionario("123"); } } \[/java\]

O grande problema é a interface `Transacao`. Como estamos testando a unidade, não queremos que um objeto real se comunique com o mainframe: seria lento e difícil de testar o resultado, além de testar mais de uma unidade. Esse assunto já foi bastante discutido em artigos do blog da Caelum [que falam de testes](https://blog.caelum.com.br/testes-unitarios-com-jmock-2/), [sua influência no design](https://blog.caelum.com.br/tdd-e-sua-influencia-no-acoplamento-e-coesao/), [no acoplamento](https://blog.caelum.com.br/facilitando-a-manutencao-dos-testes-ao-diminuir-o-acoplamento-com-o-codigo/) e na [velocidade do seu projeto](https://blog.caelum.com.br/perdendo-ou-ganhando-tempo-com-testes-de-unidade/). Perceba o uso da annotation [`@Mock`](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#9), ela facilita a criação de mocks, deixando o código mais limpo.

A primeira linha do primeiro teste (`when(transacao.executar("12345")).thenReturn(" ... ")`)define como queremos que o mock se comporte durante a chamada do método `executar`. O Mockito leva uma grande vantagem aqui sobre os outros frameworks por ser extremamente refactoring friendly. Para isso, usamos o método [`when`](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#2). Já na primeira linha do segundo teste podemos emular um erro através do `thenThrow`, ou seja, caso não haja nenhum funcionario, o método deverá lançar uma exceção. Com isso isolamos o teste apenas à unidade em questão, sem que código de outras unidades sejam executados.

Podemos ainda verificar se durante a execução de `buscarFuncionario` o método do nosso mock foi acionado. Para isso usamos o método [verify](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#2). O segundo argumento pode receber alguns outros métodos do Mockito como _[`never`, `atMostOnce`, `alLeastOnce`, `times`()](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#4)_.

Há algumas pessoas que acham desnecessário verificar se o método do objeto mocado foi realmente invocado. Inclusive a própria [documentação do mockito](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#2) comenta a respeito. Porém o uso do `verify` evita que alguém retire essa linha do seu código, algo bem difícil de acontecer, mas por experiência própria, podemos esquecer algum `assert`.

Feito os testes, partimos então para a lógica.

\[java\] class FuncionarioDAO { private Transacao transacao; FuncionarioDAO(Transacao tx) { this.transacao = tx; }

public Funcionario buscarFuncionario(String matricula) { try { String resposta = transacao.executar(matricula); return montarFuncionario(resposta); } catch(TransacaoOnlineException e){ throw new UsuarioInexistenteException(e); } }

private Funcionario montarFuncionario(String resposta) { String nome = resposta.substring(0,10); String matricula = resposta.substring(10,16); String setor = resposta.substring(16,21); return new Funcionario(nome.trim(), matricula.trim(), Integer.parseInt(setor.trim())); } } \[/java\]

Agora, ao rodar os testes, você obterá a [green bar](http://www.google.com.br/imgres?q=junit+green+bar&um=1&hl=pt-BR&sa=N&tbm=isch&tbnid=Qn2qc_RY8jDvZM:&imgrefurl=http://static.springsource.org/spring-webflow/docs/1.0.x/reference/flow-execution.html&docid=RaCtDbrS0gKBfM&imgurl=http://static.springsource.org/spring-webflow/docs/1.0.x/reference/images/junit-greenbar.png&w=314&h=111&ei=l8muTtP4AoWAgwe-tZ3oDw&zoom=1&iact=rc&dur=562&sig=104183618692365740319&page=1&tbnh=66&tbnw=186&start=0&ndsp=31&ved=1t:429,r:4,s:0&tx=106&ty=37&biw=1277&bih=952)!.

Vamos imaginar um cenário que você esteja lidando com um sistema com design mais pobre, [com muito uso de métodos estáticos](https://blog.caelum.com.br/singletons-e-static-perigo-a-vista/), e seu design ficasse assim:

\[java\] class FuncionarioDAO { public Funcionario buscarFuncionario(String matricula){ String resposta = TransacaoServiceLocator.executar(matricula); return montarFuncionario(resposta); } } \[/java\]

Há algumas formas de testar esse código, ou seja, mocar o comportamento estático. A primeira é manipulação de bytecode ([finamente explicado](http://gsd.ime.usp.br/~kon/MAC5715/slides/ManipulacaodeBytecodeJava.pdf) pelo meu amigo [André Breves](http://twitter.com/andrebreves)). Entretanto, não me arriscaria a fazer isso na mão, já existem frameworks para tal finalidade e um deles é o [PowerMock](http://code.google.com/p/powermock/), que se integra facilmente com Mockito e JUnit. É muito útil principalmente quando o framework que você está usando lhe impõe invocações estáticas a ele. Exemplificando, mesmo tendo D.I, inevitavelmente, uma hora ou outra utilizando o [Seam 2.2](http://seamframework.org/), você invocará `Component.getInstance()`.

Mas é sempre bom lembrar [que fazer um design mais orientado a interfaces e desacoplado de implementações concretas](http://martinfowler.com/articles/injection.html) lhe dará uma maior flexibilidade tanto na programação do sistema quanto na construção de testes. [A discussão sobre utilização de métodos estáticos já está batida na comunidade](https://blog.caelum.com.br/singletons-e-static-perigo-a-vista/), vários argumentos, dentre eles a programação mais estruturada e menos O.O.

A segunda seria encapsular o `TransacaoServiceLocator` em uma outra classe e mockar essa classe, sem ter de fazer malabarismos.

A terceira forma seria extrair a chamada para um método e na classe `FuncionarioDAOTest`, na criação de `FuncionarioDAO`, poderíamos criar uma classe anônima e fazer o override do método `buscarTransacao`, retornando um mock.

\[java\] public Funcionario buscarFuncionario(String matricula){ Transacao tx =  buscarTransacao(); String resposta = tx.executar(matricula) ; return montarFuncionario(resposta); }

public Transacao buscarTransacao() { return TransacaoServiceLocator.buscarTransacaoMainFrame(); } \[/java\]

Mas tudo isso é muito complicado, como o mockito te ajuda? Aí vêm os [Spy Objects](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#2), a próposito, [brilhantemente explicado aqui](http://eclipsesource.com/blogs/2011/10/13/effective-mockito-part-3/). Em suma, Spy Objects são objetos reais até que se prove o contrário. E provamos o contrário quando definimos algum comportamento para ele. Exemplificando:

\[java\] class FuncionarioDAOTest{

private FuncionarioDAO funcionarioDAO;

@Mock private Transacao transacao;

@Before public void init(){ MockitoAnnotations.initMocks(this); funcionarioDAO = spy(new FuncionarioDAO(transacao)); doReturn(transacao).when(funcionarioDAO).buscarTransacao(); } \[/java\]

Agora definimos que todos os métodos de `FuncionarioDAO` serão invocados normalmente, com exceção do método `buscarTransacao`, que teve o comportamento alterado. [É importante reparar que eu usei `doReturn` ao invés do `when`](http://docs.mockito.googlecode.com/hg/latest/org/mockito/Mockito.html#13).

Concluindo, o importante mesmo é saber lidar com as várias ferramentas que existem, tirando o maior proveito delas para construção de testes. Por exemplo, o meu framework não faz injeção de dependências por construtores, então já que eu não tenho construtores para receber os atributos, nos meus testes eu faço a injeção usando o [Mirror](http://projetos.vidageek.net/mirror/mirror/), uma DSL que ajuda na manipulação da API reflection. Hoje mesmo já fui apresentando a uma outra ferramenta, [fixture-factory](https://github.com/aparra/fixture-factory). Indubitavelmente, conhecer o poder que cada ferramenta pode lhe oferecer e principalmente saber quando as utilizar são [duas importantes tarefas de um arquiteto de software](https://blog.caelum.com.br/entao-voce-quer-ser-um-arquiteto-java/).

E quais ferramentas você utiliza para fazer os seus testes?

Créditos especiais ao companheiro de trabalho [Taciano Tres](http://twitter.com/tacianot),que me ajudou com ótimas referências e sempre me obriga a estar atualizado!
