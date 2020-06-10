---
title: "Builders no Scala com named parameters"
date: "2011-05-19"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![](https://blog.caelum.com.br/wp-content/uploads/2011/05/legobuilder.jpg "scala builder") Durante a escrita dos testes das nossas aplicações, é muito comum instanciarmos objetos bem parecidos em diversos momentos. Por exemplo, quando precisamos de um usuario logado para efetuar as operações do nosso sistema, ou quando precisamos criar um novo usuário para testar algo relativo ao seu endereço. Em vários de nossos testes acabamos com um código que popula algum dos atributos de uma entidade:

\[scala\] User user = new User() user.setLogin("algumLogin") user.setSenha("algumaSenha") user.setEmail("email@email.com.br") \[/scala\]

Fazer uma vez ou outra não incomoda muito, mas quando esse código começa a se repetir, de vez em quando tendo que váriar os valores para criação do objeto, os testes podem ficar bastante sujos com a repetição dessas instruções.

Para minimizar o problema podemos utilizar os builders, classes cuja única responsabilidade é facilitar a criação de um objeto. A idéia é dar a possibilidade de escrever um código com interface mais fluente durante a escrita do teste. Por exemplo, para criar um usuário com nome, login e senha poderiamos ter o seguinte código:

\[scala\] new UserBuilder().comLogin(...).comSenha("").comEmail("") \[/scala\]

Alguns vão considerar que esses simples uso de interface fluente podem ser considerados como umapequena DSL, um estilo de código que tenta se aproximar ao máximo de um contexto especifico. Um caso classico é a API de criteria do Hibernate. No atual projeto que participo na Caelum caímos no mesmo problema, mas como ele está sendo desenvolvido em Scala, usamos a mesma abordagem, aproveitando do poder da linguagem. A idéia é que para criar um novo usuario possamos escrever o seguinte código:

\[scala\] UserBuilder(login="login", senha="senha", email="algumemail@email.com.br") \[/scala\]

Ou, caso apenas precisemos de um usuário com nome e email padrão, simplesmente não passamos nada. Por exemplo:

\[scala\] val u = UserBuilder() \[/scala\]

A sacada é usar um pouco das features da linguagem para que possamos construir nossos builders sem muito esforço. A primeira é o sintax sugar que a linguagem oferece para não precisarmos do `new` no momento de instanciar um objeto.  Para isso basta criarmos um `object` com um método `apply`:

\[scala\] object UserBuilder { def apply(login:String, password:String, email:String) = new User(login, password, email) } \[/scala\]

Toda classe que é definida sendo um `object` serve como uma classe de métodos utilitários, muito parecido com os helpers que vemos por aí. O método `apply` é o que faz a jogada de não precisarmos do `new`. Aqui já ganhamos uma facilidade da linguagem, ele nos permite que referenciemos os nomes dos parâmetros no momento da passagem deles. A criação de um usuário com nome e email ficaria assim:

\[scala\] UserBuilder(login="login", senha="senha", email="algumemail@email.com.br") \[/scala\]

Para finalizar, queremos deixar [valores default nos parâmetros](http://www.scala-lang.org/sid/1), para quando não for necessária nenhuma customização. Basta atribuir alguns valores na definição dos parâmetros:

\[scala\] object UserBuilder { def apply(login:String = "Fulano", password:String = "pass", email:String = "fulano@provedor.com") = new User(login, password, email) } \[/scala\]

Além de suportar maneiras diferentes de programar, por exemplo, dando a possibilidade de utilização do paradigma funcional, Scala também oferece muita melhoria na estrutura da linguagem, o que faz com que ela venha sendo cada vez mais comentada pela comunidade. No caso do uso para testes, poderíamos colocar diversas chamadas a esses builders dentro de um [ObjectMother](http://martinfowler.com/bliki/ObjectMother.html).

Indo mais além, usamos aqui na Caelum [nossa DSL em cima da JPA-Hibernate](https://github.com/asouza/hibernate-query-dsl), para facilitar nossas consultas ao banco de dados, possibilitando escrever:

\[scala\] val list = session.from\[User\].where("age" <= 25).asList\[User\] \[/scala\]
