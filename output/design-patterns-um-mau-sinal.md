---
title: "Design Patterns: um mau sinal?"
date: "2006-12-18"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma semana atrás estava em Moçambique, próximo a África do Sul, ministrando três treinamentos da Caelum. Conheci muita gente e muitos desenvolvedores, incluindo dois escoceses: [Cameron Smith](http://mozcam.blogspot.com/), formado em ciências políticas (!) e mestre em engenharia de software pela Univerdade de Glasgow, e [Colin Fairless](http://www.flickr.com/photos/colin2012/), bacharel e mestre em Matemática Aplicada pela Imperial College de Londres. Posso dizer que aproveitei muito do enorme conhecimento desses dois.

Durante uma conversa com o Cameron, ele comentou bastante sobre a maneira de ensinar um novo desenvolvedor java. Em especial a dificuldade do _HelloWorld_ e se os programadores são ainda iniciantes. Ele particularmente usa o beanshell no nício, para o estudante ter uma resposta rápida. Depois o assunto foi sobre como ensinar design patterns. Cameron criticou bastante jogar diagramas UML para uma pessoa e mostrar alguns casos de uso, como é feito em [tantos livros](http://www.amazon.com/Design-Patterns-Dummies-Computer-Tech/dp/0471798541/sr=8-1/qid=1166418171/ref=pd_bbs_sr_1/102-3547064-7980133?ie=UTF8&s=books). Tanto ele quanto eu achamos que isso deva ocorrer de uma maneira natural: esperar o aluno refletir sobre um caso particular. Um exemplo seria a necessidade de fazer algum cache ou controle de instância, para você deixar o construtor dessa classe privado e fornecer um método estático para fábrica-lo. Então sim você teria a permissão de explicar que milhares de pessoas passam por esse mesmo problema e que essa solução rápida foi batizada de _factory_. Neste início nada de UML, apenas código deve ser usado.

O amado e odiado Paul Graham diz, em [a revanche dos nerds](http://www.paulgraham.com/icad.html), que quando você encontra um problema e esse começa a se tornar comum, você tem três opções: (a) usar uma linguagem poderosa que lhe permita resolver aquele problema (b) escrever um interpretador para uma linguagem que resolva aquele problema (c) virar um compilador humano. Ele diz que os design patterns caem na categoria (c): é você quem precisa resolver um problema muito comum. Você é o responsável por perceber os casos de uso de determinado pattern e escrever pela milésima vez a mesma solução!

Ele ainda cita [Peter Novig](http://www.norvig.com/) (um dos famosos autores do [AIMA](http://aima.cs.berkeley.edu/) e diretor de pesquisas do Google), em sua [apresentação](http://www.norvig.com/design-patterns/ppframe.htm) sobre como 16 dos [23 design patterns do GoF](http://en.wikipedia.org/wiki/Design_Patterns) são evitados/invisíveis usando linguagens dinâmicas.

Ambos criticam muito o uso de Design Patterns como um template: substitua aqui, ali e ali o nome de sua classe, implemente aquela interface e você já tem tudo quase pronto! Eles procuram separar isso de uma idéia que chamam de _Design Strategies_, fazendo uma analogia de templates versus provérbios: não é uma receita de bolo a ser aplicada, e sim mais um guia de como resolver um problema. A receita de bolo é o mau sinal! O _smell_.

Pensando nisso separei alguns design patterns que com o passar do tempo estão sumindo, devido a uma abordagem diferente de programação, ou até mesmo virando antipatterns.

**Singleton** - Este é bem fácil e já [bloguei sobre os problemas do Singleton](https://blog.caelum.com.br/singletons-e-static-perigo-a-vista/). É raríssimo querermos que uma classe possua uma única instância por modelagem. Normalmente usamos o singleton apenas para fazer lookup a uma instância: quase que uma variável global. Pode ser totalmente substituído com o uso de injeção de dependências.

**Service Locator** - Mesmo problema que o singleton: uma maneira de pegarmos a referência a algum objeto. O uso de injeção de dependências (como `@EJB`, `@Resource` e `@PersistenceContext` no Java EE 5) eliminam a necessidade de aplicar esse pattern.

**ValueObject** - No Java EE é comum criarmos classes que servem apenas para trafegar os valores das nossas entidades verdadeiras. Fazemos isso porque provavelmente nossas entidades são muito pesadas, remotas, ou fortemente acopladas a alguma API específica. Para passar essa informação para nossa camada View antes produzimos ValueObjects (ou _Data Transfer Objects_, para quem prefere). O Hibernate, EJB3 ou mesmo com os `ActionForm`s do Struts eliminam a necessidade de você criar uma classe nova muita parecida com a sua entidade real, já que nessas tecnologias podemos usar POJOs.

**DAO** - Alguns dizem que não deveríamos ter um DAO: nossa própria entidade faria o acesso aos dados. É a idéia do ActiveRecord e de tantos outros frameworks (e para quem se lembra das ferramentas java de ORM do milênio passado, como Torque e OJB). Caso você tenha uma classe `Produto`, você teria um método estático `Produto.list()`, por exemplo. Isto evitaria o _smell_ de criar uma hierarquia de classes paralelas a suas entidades. _Pessoalmente_ hoje em dia eu não gosto da idéia, mas é uma forte vertente.

Obviamente não estou pregando a extinção dos patterns, apenas lembrando que devemos medir muito bem a necessidade de criar mais classes e mais abstrações, em vez de atacar o problema com mais simplicidade, como o Michael Nascimento também já [blogou](http://blog.michaelnascimento.com.br/2006/08/30/vale-a-pena-abstrair/) ([duas vezes](http://blog.michaelnascimento.com.br/2006/09/22/vale-a-pena-abstrair-parte-2/)). Aproveite e conheça mais dos design patterns de maneira aplicada e coerente no [nosso curso online](http://www.caelum.com.br/curso/online/design-patterns/).
