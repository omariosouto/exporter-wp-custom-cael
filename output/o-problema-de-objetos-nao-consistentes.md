---
title: "OO na prática: o problema de objetos não consistentes"
date: "2015-06-02"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Programar orientado a objetos é sempre um desafio. E o desafio está justamente na dificuldade de criarmos classes que são fáceis de serem usadas, mantidas e reutilizadas.

Uma classe fácil de ser usada, dentre várias coisas, é aquela onde **as pré-condições são simples**. Por pré-condições, entenda tudo aquilo que você precisa fazer antes de conseguir invocar o método que quer. Por exemplo, para invocar _b()_, você é obrigado a invocar _a()_ antes, passando um número positivo pra ele.

Veja o trecho de código abaixo, extraído do código-fonte do [Alura](http://www.alura.com.br), nossa plataforma de ensino à distância. Quando o aluno entra em uma trilha (_Path_), ele vê cada um dos cursos dessa trilha, bem como seu andamento. Nossa equipe optou por criar a classe _PathPosition_, que contém um _Path_ e uma lista de _Position_s, que contém um curso e a porcentagem de andamento desse curso.

Não se preocupe com os detalhes do código, pense apenas no alto nível, afinal, a parte boa de um projeto de classes OO é justamente que você não precisa saber sobre os detalhes de implementação de cada classe. Você precisa apenas entender quais são os comportamentos que ela provê.

\[code language="java"\] public class PathPosition { private final List<Position> positions; private final Path path;

public PathPosition(Path path) { this.path = path; positions = path.getCoursesEvenPrivate().stream(). map(PositionWithoutEnroll::new). collect(toList()); }

public int getTotalPositions() { return positions.size(); }

public boolean isCompleted() { return positions.stream().allMatch(Position::hasFinished); }

// continua aqui } \[/code\]

Sendo assim, é fácil usar a classe. Basta instanciá-la e invocar seus comportamentos (_isCompleted()_ para saber se terminou a trilha e _getTotalPositions()_ para saber quantos posições tem nessa trilha):

\[code language="java"\] Path trilhaDeJava = trilhas.pega("java"); PathPosition p = new PathPosition(trilhaDeJava);

boolean terminou = p.isCompleted(); \[/code\]

Apesar de parecer tudo certo, acredite, o código acima não funciona. Se você conseguisse olhar o código dessa classe por completo, perceberia que as instâncias dessa classe podem ser inconsistente. **Uma instância inconsistente é aquela que está em um estado inválido**, ou seja, contém atributos com valores não válidos (nulos, números negativos onde só deveriam ser positivos, e etc). E isso, óbvio, faz com que a classe possa responder de maneira incorreta.

A lista de _Position,_ existente na classe _PathPosition,_ só fica correta depois do programador invocar várias vezes o método _merge()_. Esse é a pré-condição da classe:

\[code language="java"\] public void merge(PositionWithEnroll position) { // faz alguma coisa aqui na lista de Position } \[/code\]

Ou seja, para que ela esteja pronta para uso, precisamos invocar o método _merge()_:

\[code language="java"\] Path trilhaDeJava = trilhas.pega("java"); PathPosition p = new PathPosition(trilhaDeJava);

// até aqui, "p" é inconsistente! // não podemos confiar no isCompleted() boolean mentirinha = p.isCompleted();

for(PositionWithEnroll enroll : posicoes) { p.merge(enroll); }

// agora sim, pode usar "p" // o isCompleted agora falará a verdade boolean verdadinha = p.isCompleted(); \[/code\]

Mas, se você tem uma instancia de _PathPosition_ na mão, como saber se ele está pronto pra uso ou não? **Esse é o problema de objetos inconsistentes: você nunca sabe se ele contém dados válidos e se você pode usar toda a classe.**

Lembre-se que o usuário deve ser capaz de invocar qualquer método a qualquer momento. Portanto, que você for desenhar sua classe, proíba-a de estar em um estado inconsistente. Para isso, **use e abuse de construtores:** Se sua classe precisa de alguns dados desde o começo, peça-os no construtor. Uma classe CPF sem um número de CPF é inconsistente. Ou seja, peça o número no construtor. Também **valide os dados passados:** Se o usuário passar um valor negativo onde não deveria, sua classe deve recusar esse valor.

Nesse caso em particular, nossa solução foi criar um _PathPositionBuilder_, que cria uma instância de _PathPosition_, já consistente. Mudamos também o modificador de visibilidade do construtor para que ninguém consiga instanciar um _PathPosition_ diretamente.

Novamente, faça com que suas classes sejam sempre consistentes. Isso facilita e muito a sua utilização. Discuto mais sobre orientação a objetos no meu novo livro ["Orientação a Objetos e SOLID para Ninjas: Projetando Classes Flexíveis"](http://www.casadocodigo.com.br/products/livro-oo-solid), que publiquei pela [Casa do Código](http://www.casadocodigo.com.br). Também falo sobre isso no [curso online do Alura sobre SOLID](https://www.alura.com.br/cursos-online-java-avancado#orientacao-a-objetos-avancada-e-principios-solid).
