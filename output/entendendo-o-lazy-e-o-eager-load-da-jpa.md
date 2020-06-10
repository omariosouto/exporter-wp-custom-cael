---
title: "Entendendo o Lazy e o Eager Load da JPA"
date: "2014-12-09"
author: "Felipe Oliveira"
authorEmail: "felipe.oliveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um dos pontos mais importantes para se analisar em um software é a performance e otimizar as queries SQL ao maximo, pode resultar em um ganho considerável de performance. Levando isso em consideração vamos ver como funciona o Lazy e o Eager Load da JPA (Java Persistence API), pois o uso deles de maneira incorreta ocasiona uma perda enorme na performance de uma aplicação, forçando o banco de dados a querys desnecessárias.

Quando modelamos um sistema usando orientação à objetos criamos vários relacionamentos que podem ser `@OneToOne`, `@ManyToOne`, `@OneToMany` ou `@ManyToMany` e para o banco de dados cada relacionamento é uma nova tabela para consultar.

Suponha que temos duas entidades, `Aluno` e `Matricula`: \[code language="java"\] @Entity public class Aluno { @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id; private String nome; @OneToOne private Matricula matricula;

// getters e setters omitidos } \[/code\]

\[code language="java"\] @Entity public class Matricula { @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id; @Column(unique = true, nullable = false) private String codigo;

// getters e setters omitidos } \[/code\]

Por padrão, quando o relacionamento está anotado com `@OneToOne` ou `@ManyToOne` ele é carregado de modo Eager ou seja, quando fizemos qualquer tipo de busca na entidade como por exemplo um `find(Aluno.class, 1)` ele será carregado junto com a entidade e nesse caso a JPA executará uma query só.

No exemplo acima colocamos anotação `@OneToOne` no atributo `Matricula`, faz sentido? Não, pois geralmente um aluno tem varias matriculas, então vamos alterar a anotação do relacionamento da classe `Aluno` para `@OneToMany` e o tipo para `List`:

\[code language="java"\] @Entity public class Aluno { @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id; private String nome; @OneToMany private List<Matricula> matriculas;

// getters e setters omitidos } \[/code\]

\[code language="java"\] @Entity public class Matricula { @Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id; @Column(unique = true, nullable = false) private String codigo;

// getters e setters omitidos } \[/code\]

Por padrão quando o relacionamento está anotado com `@OneToMany` ou `@ManyToMany` ele é carregado de modo Lazy ou seja, quando fizemos qualquer tipo de busca na entidade como por exemplo um `find(Aluno.class, 1)` ele não será carregado junto com a entidade, somente quando executarmos o comando `getMatriculas()` o relacionamento será carregado.

Por o modo Lazy só carregar as informações quando executamos um getter fazer um `aluno.getMatriculas().getCodigo()` dentro de um `for` para pegar o código de todas as matriculas do aluno pode trazer problema de performance à aplicação, pois a JPA executará várias queries.

\[code language="java"\] for(Aluno aluno : alunos) { for(Matricula matricula : aluno.getMatriculas(){ System.out.println(matricula.getCodigo()); } } \[/code\]

O código de dentro do `for` será executado o tamanho da lista vezes, por exemplo se o tamanho da lista for N, o código será executado N vezes então a JPA executará N queries no banco de dados, podemos contar também a query para carregar a entidade. Esse problema é conhecido como \[N+1\] queries, um dos [problemas a serem evitados no uso da JPA e do Hibernate](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/).

Além do problema citado acima temos que tomar cuidado com a `LazyInitializationException`. No post [Enfrentando a LazyInitializationException no Hibernate](https://blog.caelum.com.br/enfrentando-a-lazyinitializationexception-no-hibernate/) o Paulo Silveira explica como lidar com esse problema.

Como vimos neste post o carregamento das entidades de modo incorreto pode ocasionar perda de performance, portanto lembre-se de considerar a melhor estratégia para o carregamento dos seus relacionamentos.
