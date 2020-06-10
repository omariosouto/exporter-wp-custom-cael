---
title: "Criando cenários de teste com Fixture Factory"
date: "2013-10-08"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Escrever testes automatizados é com certeza um desafio, ainda mais para quem está começando. Mas, independente de conhecimento, montar cenários de teste é sempre uma tarefa que requer um certo esforço.

Essa é geralmente a parte mais chata de se escrever um teste. O programador precisa instanciar entidades (às vezes várias delas, pois uma pode ser composta por outro), passar valores específicos para elas, e etc. Em linguagens menos enxutas, como Java, isso costuma gastar algumas linhas de código de teste.

Para facilitar a vida, é comum que programadores apelem para classes especializadas, que ajudam a criar cenários de teste. Existem até padrões de projeto, como é o caso do [Test Data Builder](http://www.natpryce.com/articles/000714.html), que é um Builder (à la GoF) de cenários.

Um framework brasileiro que resolve bem esse problema é o [Fixture-Factory](https://github.com/six2six/fixture-factory). A ideia dele é justamente facilitar a criação de cenários. Como? Você ensina ele a criar objetos e, depois disso, apenas pede instâncias de cenários pra ele.

Veja, por exemplo, o código abaixo. Nele definimos duas _fixtures_ (que é o nome que damos para "exemplos de cenários") com o nome _valido_, e depois pedimos uma instância de Cliente:

\[java\] Fixture.of(Cliente.class).addTemplate("valido", new Rule(){{ add("id", random(Long.class, range(1L, 200L))); add("nome", random("Anderson Parra", "Arthur Hirata")); add("apelido", random("nerd", "geek")); add("email", "${apelido}@gmail.com"); add("aniversario", instant("18 years ago")); add("endereco", fixture(Endereco.class, "valido")); }});

Fixture.of(Endereco.class).addTemplate("valido", new Rule(){{ add("id", random(Long.class, range(1L, 100L))); add("rua", random("Paulista Avenue", "Ibirapuera Avenue")); add("cidade", "São Paulo"); add("estado", "${cidade}"); add("pais", "Brasil"); add("cep", random("06608000", "17720000")); }});

Cliente cliente = Fixture.from(Cliente.class).gimme("valido"); \[/java\]

Repare na quantidade de maneiras diferentes que você pode configurar sua fixture. O nome é randômico, o e-mail é baseado no outro atributo, o endereço vem de outra fixture, e etc. O framework é realmente bem completo, e é constantemente evoluído.

Há um tempo atrás, o Anderson Parra, um dos criadores do framework deu uma palestra aqui na Caelum sobre o assunto. Você pode vê-la aqui:

<iframe src="//player.vimeo.com/video/75310850" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

Se você deseja aprender mais sobre testes automatizados e TDD, pode consultar [meu livro](http://www.tddnomundoreal.com.br), ou os [cursos online do Alura](http://www.alura.com.br/cursos-online-agile), além do nosso [curso de práticas ágeis na Caelum](http://www.caelum.com.br/curso-praticas-ageis/).
