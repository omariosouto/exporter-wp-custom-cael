---
title: "Testes unitários com JMock 2"
date: "2008-03-17"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Podemos definir teste unitário de uma classe como um teste em que verificamos uma funcionalidade da classe em questão passando o mínimo possível por outras classes do sistema, ou que sejam dependências do sistema.

Por mais desacoplada que seja nossa classe, se ela tiver um mínimo de complexidade, ela vai precisar de funcionalidades de outras classe, ou seja, ela vai ter _dependências_. E essas dependências sempre nos atrapalham na hora de fazermos os testes unitários da classe.

Por exemplo, se uma das dependências da nossa classe é a interface `HttpServletRequest`, o que vamos passar pra ela na hora do teste? `null`, e levar uma `NullPointerException`? Desenterrar a biblioteca e passar uma implementação de verdade? Claro que não.

Em testes unitários não estamos interessados no comportamento real das dependências da classe, mas em como a classe em questão se comporta diante das possíveis respostas das dependências, ou então se a classe modificou as dependências da maneira esperada.

Então é comum passarmos **implementações falsas (mock objects)** das dependências da classe, retornando valores pertinentes para conseguirmos testá-la satisfatoriamente. Esses mocks costumam poluir seu código de testes com várias classes que só são usadas em poucos testes. E o problema fica pior quando a interface da dependência possui muitos métodos mas só usamos um ou dois deles (é o caso do `HttpServletRequest`). É nessa hora que o [**JMock**](http://www.jmock.org) vem para facilitar a nossa vida.

O JMock é uma biblioteca que auxilia o [**Test Driven Development**](http://en.wikipedia.org/wiki/Test-driven_development) através dos mock objects. É uma biblioteca que vai criar implementações de mentira específicas para o seu teste, de uma maneira rápida e simples, sem ter que se preocupar com os métodos que não vamos usar no teste, sem ao menos ficar criando classes "a toa". Com o JMock podemos definir o comportamento necessário do objeto de mentira, para criarmos a situação pedida pelo teste.

Para termos uma idéia melhor vamos ver um exemplo prático de como usar o JMock. Vamos supor que temos uma lógica de login no nosso sistema e queremos testá-la. As dependências dessa lógica são recebidas no construtor:

\[java\] public class LoginLogic { private final HttpSession session; private final Authenticator auth; public LoginLogic (HttpSession session, Authenticator auth) { this.session = session; this.auth = auth; } public void login(User user) { //... } } \[/java\]

Vamos testar o método login, que tem duas situações possíveis: se o usuário é válido, entra na sessão, senão não entra. Para testar precisamos de um teste que vai passar um usuário válido, e garantir que o usuário entrou na sessão; e um teste que vai passar um usuário inválido e garantir que o usuário não entrou na sessão.

Para criar esses testes precisamos de uma instância da classe `LoginLogic`, logo precisamos nos preocupar com as dependências da classe: o `HttpSession`, que é uma interface do java servlet, e o `Authenticator`, que é uma interface do nosso projeto contendo um método chamado `isValid(User)` que recebe um usuário e vê se ele é válido.

Começando nosso teste: \[java\] LoginLogic logic = new LoginLogic(/\*um httpSession\*/, /\*um authenticator\*/); logic.login(/\*um user válido\*/); assertTrue(/\*o usuário está na sessão\*/); \[/java\]

Precisamos passar para nossa lógica um objeto que implementa `HttpSession`, e um que implementa `Authenticator`. Poderíamos criar implementações falsas dessas interfaces, mas não queremos poluir nosso código de testes com classes "inúteis". Vamos, então, deixar o JMock fazer esse trabalho sujo para nós.

Para começar a criar essas implementações falsas, precisamos de uma fábrica de objetos falsos (mocks) do JMock: \[java\] Mockery mockery = new Mockery(); \[/java\]

É comum criarmos essa fábrica no começo de cada teste, ou no `setUp` da sua classe de testes (se você usa o **JUnit**). Com a fábrica em mãos, podemos começar a mockar as nossas interfaces. É bem simples: \[java\] HttpSession session = mockery.mock(HttpSession.class); Authenticator auth = mockery.mock(Authenticator.class) \[/java\]

E já podemos passar esses mocks para nosso `LoginLogic`. Nosso teste ficaria então: \[java\] final Mockery mockery = new Mockery(); final HttpSession session = mockery.mock(HttpSession.class); final Authenticator auth = mockery.mock(Authenticator.class);

LoginLogic logic = new LoginLogic(session, auth); logic.login(/\*um user válido\*/); assertTrue(/\*o usuário está na sessão\*/); \[/java\]

Se fizermos simplesmente isso, o teste não vai fazer nada. Precisamos dizer para nossos mocks como vai ser o comportamento deles quando forem acessados. A maneira de fazer isso no JMock 2.4 é bem interessante, utiliza uma sintaxe um pouco incomum, mas quando você se acostuma com ela fica bastante legível: \[java\] mockery.checking(new Expectations() {{ //comportamento dos mocks aqui }}); \[/java\]

Repare no truque: uma classe anônima, com um pré construtor (note os dois grupos de chaves). Esse bloco vai ser chamado pelo método checking, e vai adicionar o comportamento pedido aos mocks. Fazemos isso de uma maneira fluente bem interessante. Por exemplo, se quisermos garantir que o usuário foi colocado na sessão após o teste, fazemos:

\[java\] one(session).setAttribute("user", with(any(User.class))); \[/java\]

O que essa linha quer dizer é o seguinte: eu quero que o método `setAttribute` seja chamado **uma** vez, com os parâmetros "user" e com qualquer objeto do tipo `User`.

Se esse método nunca for chamado com esses parâmetros ou for chamado mais de uma vez, o teste vai falhar. Na verdade, tudo que é possível acontecer com os mocks tem que estar descrito em um **expectations** (pode ter mais de um no teste). Se pro teste não é importante o que acontece com um dos objetos mockados, podemos ignorá-los, escrevendo:

\[java\] ignoring(mockObject); \[/java\]

Fazendo isso, todo método chamado desse método vai retornar valores padrão (0, "", null ou, se for possível um outro mockObject marcado como ignoring). Mas para que tudo isso aconteça, precisamos colocar no final do nosso teste a seguinte linha:

\[java\] mockery.assertIsSatisfied(); \[/java\]

Bom, ainda não terminamos o nosso teste, precisamos fazer com que o usuário seja válido, ou seja, precisamos garantir que o método isValid() do authenticator retorne true. Fazemos isso da seguinte maneira, dentro do expectations:

\[java\] one(auth).isValid(with(any(User.class))); will(returnValue(true)); \[/java\]

Fica bastante legível: o método `isValid` do mock auth será chamado uma vez, com qualquer `User` como parâmetro, e vai retornar o valor `true`.

O nosso teste completo ficaria, então:

\[java\] final Mockery mockery = new Mockery(); final HttpSession session = mockery.mock(HttpSession.class); final Authenticator auth = mockery.mock(Authenticator.class)

mockery.checking(new Expectations() {{ one(session).setAttribute("user", with(any(User.class))); one(auth).isValid(with(any(User.class))); will(returnValue(true)); }});

LoginLogic logic = new LoginLogic(session, auth); logic.login(new User());

mockery.assertIsSatisfied(); \[/java\]

Note que poderíamos garantir que o user passado para os métodos foi o mesmo que passamos para a lógica, apenas colocando ele como parâmetro para o método. E para fazer o teste do usuário inválido só precisamos mudar o conteúdo do expectations, substituindo por:

\[java\] never(session).setAttribute("user", with(any(User.class))); one(auth).isValid(with(any(User.class))); will(returnValue(false)); \[/java\]

Ou seja, o `isValid` vai retornar `false`, e o método `setAttibute` nunca vai ser chamado. Se isso acontecer, o teste passa.

O roteiro do expectations não precisa estar na ordem em que ele vai acontecer no seu método (por exemplo o `isValid` provavelmente vai ser chamado antes do `setAttribute`), mas tem um jeito de você [garantir a ordem dos comandos](http://www.jmock.org/sequences.html).

Por padrão o JMock só é capaz de mockar interfaces, mas você pode configurá-lo para [mockar classes](http://www.jmock.org/mocking-classes.html).

Você pode, ainda, usar o JMock para fazer testes de integração. Como o roteiro do teste pode ficar bastante complexo, você pode utilizar [uma máquina de estados](http://www.jmock.org/states.html) para adicionar um pouco mais de poder ao roteiro (por exemplo garantir que uma chamada de método sempre ocorra depois de uma outra).

Uma chamada genérica num expectations, então, seria: \[java\] invocation-count (mock-object).method(argument-constraints); inSequence(sequence-name); when(state-machine.is(state-name)); will(action); then(state-machine.is(new-state-name)); \[/java\]

Se você quiser usar todo o poder do JMock, dê uma olhada na [sua documentação](http://www.jmock.org/cookbook.html). E, claro, não abuse de mocks colocando-os em todas as situações, senão seu teste unitário pode perder o valor de testar uma pequena unidade.
