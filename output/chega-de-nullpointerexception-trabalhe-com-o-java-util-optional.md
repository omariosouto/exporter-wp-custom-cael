---
title: "Chega de NullPointerException, trabalhe com o java.util.Optional!"
date: "2014-07-30"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já vimos aqui no blog as [principais novidades do Java 8](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/), detalhes da [nova API de datas](https://blog.caelum.com.br/conheca-a-nova-api-de-datas-do-java-8/) e como [aplicar algumas dessas novidades em nosso dia a dia](https://blog.caelum.com.br/streams-e-datas-para-os-desafios-do-dia-a-dia-no-java-8/). Mas há muito o que explorar nas diversas introduções dessa nova versão da linguagem.

Quem nunca escreveu um código parecido com esse?

\[code language="java"\] List<Matricula> matriculas = turma.getMatriculas();

for (Matricula matricula : matriculas) { System.out.println(matricula.getCurso().getNome()); } \[/code\]

Tudo parece certo, mas esse código pode ser bastante problemático. A simples ausência de algum desses valores pode resultar no tão frequente `NullPointerException`. Claro, você pode contornar esse problema de diversas formas, como por exemplo inicializar suas listas ou sempre verificar que os valores são diferentes de `null` antes de fazer qualquer operação com eles.

Nosso código pode ficar parecido com:

\[code language="java"\] List<Matricula> matriculas = turma.getMatriculas();

for (Matricula matricula : matriculas) { if (matricula.getCurso() != null) { System.out.println(matricula.getCurso().getNome()); } } \[/code\]

Nada mal, mas e os outros atributos? A quantidade de `if`s perdidos pelo nosso código para fazer esse tipo de verificação só tende a aumentar, isso faz com que ele fique cada vez mais ilegível e difícil de manter. E esse nem é o pior dos problemas, esquecer de fazer uma verificação como essa é um risco muito grande... em algum momento isso vai acontecer e você receberá um `NullPointerException`.

Como resolver o problema? A mais nova versão do Java nos prove uma forma muito mais interessante de representar esses atributos que podem ou não estar presentes em nosso código, utilizando o `java.util.Optional`.

Você pode pensar em um `Optional` como uma classe que pode ou não conter um valor não nulo. Repare como fica o código da classe `Matricula` declarando o atributo `Curso` como `Optional`:

\[code language="java"\] public class Matricula {

private Optional<Curso> curso = Optional.empty();

// outros atributos, getters e setters } \[/code\]

Utilizamos o factory method `empty` para criar e inicializar o atributo com um `Optional` vazio. Há outras formas de se criar um `Optional`, você pode por exemplo utilizar seu método `of`:

\[code language="java"\] Optional<Curso> cursoOpcional = Optional.of(algumCurso); \[/code\]

Mas é importante saber que caso o valor da variável `algumCurso` seja `null`, esse código irá lançar um `NullPointerException`. Para evitar isso podemos utilizar o método `ofNullable`:

\[code language="java"\] Optional<Curso> cursoOpcional = Optional.ofNullable(algumCurso); \[/code\]

Agora que o método `getCurso` retorna um `Optional`, precisamos chamar seu método `get` para recuperar o curso que esse `Optional` está guardando:

\[code language="java"\] for (Matricula matricula : matriculas) { if (matricula.getCurso() != null) { System.out.println(matricula.getCurso().get().getNome()); } } \[/code\]

Mas dessa forma, se o curso não existir vamos receber um **java.util.NoSuchElementException: No value present**. Como prevenir isso? Uma das possíveis formas seria verificando se o valor desse `Optional` está presente, algo como:

\[code language="java"\] for (Matricula matricula : matriculas) { Optional<Curso> cursoOpcional = matricula.getCurso(); if(cursoOpcional.isPresent()) { System.out.println(cursoOpcional.get().getNome()); } } \[/code\]

Mas isso ainda deixa nosso código muito verboso! Que tal fazer algo como:

\[code language="java"\] for (Matricula matricula : matriculas) { matricula.getCurso() .ifPresent(c -> System.out.println(c.getNome())); } \[/code\]

Bem mais interessante, não acha? Esse é um dos vários métodos funcionais que essa classe possui. O método `ifPresent` recebe um `Consumer` que pode ser traduzido em uma expressão lambda `c -> System.out.println(c.getNome())`. Mas note que você não precisa se preocupar com nenhum desses detalhes, não importa o nome da interface funcional por traz desse código, não importa o nome de seu único método, tudo que importa nesse momento é a expressão: tenho um curso e quero imprimir seu nome.

Se você está se perguntando, podemos sim fazer o for da maneira nova! Utilizando o já conhecido `forEach`:

\[code language="java"\] matriculas.forEach(m -> { m.getCurso() .ifPresent(c -> System.out.println(c.getNome())); }); \[/code\]

Outro método interessante da classe `Optional` é o `orElseThrow`. Caso neste contexto o curso seja obrigatório, podemos fazer algo como:

\[code language="java"\] matriculas.forEach(m -> { Curso curso = m.getCurso() .orElseThrow(IllegalArgumentException::new); }); \[/code\]

Quer mais? Considere que a classe `Curso` possui uma descrição (`String`). É comum escrever um código parecido com esse para mostrar a descrição ou alguma outra mensagem caso ela não esteja presente:

\[code language="java"\] matriculas.forEach(m -> { m.getCurso().ifPresent(c -> { if (c.getDescricao() != null){ System.out.println(c.getDescricao()); } else { System.out.println("sem descrição"); } }); }); \[/code\]

Mas agora com `Optional` podemos escrever da seguinte forma:

\[code language="java"\] matriculas.forEach(m -> { m.getCurso().ifPresent(c -> { System.out.println(c.getDescricao().orElse("sem descrição")); }); }); \[/code\]

O método `orElse` nos retorna o curso caso presente, ou pelo contrário o valor passado como argumento.

Para imprimir apenas quando a descrição existir e não for vazia, poderíamos escrever um código parecido com:

\[code language="java"\] matriculas.forEach(m -> { m.getCurso() .ifPresent(c -> { Optional<String> descricao = c.getDescricao(); if (descricao.isPresent() && !descricao.get().isEmpty()) { System.out.println(descricao.get()); } }); }); \[/code\]

Resolvemos o problema, mas o código não está nem um pouco legível!

Para evitar esse encadeamento desnecessário, podemos utilizar o método `map` para fazer uma projeção do curso para sua descrição. Isso mesmo, assim como o `Stream`, um `Optional` também possui o método `map`! Vamos ver o resultado:

\[code language="java"\] matriculas.forEach(m -> { Optional<Optional<String>> map = m.getCurso().map(Curso::getDescricao); }); \[/code\]

Pois é... o `map` vai nos retornar um `Optional>`. Ainda não é o que estamos esperando. Se você já conhece a API de `Stream`s ou programa com alguma linguagem funcional, já deve ter pensado em uma solução. Sim, o `Optional` também possui o método `flatMap`! Utilizando esse método podemos achatar o `Optional>` para um `Optional` com a descrição:

\[code language="java"\] matriculas.forEach(m -> { Optional<String> flatMap = m.getCurso().flatMap(Curso::getDescricao); }); \[/code\]

Estamos quase lá. Para imprimir a descrição apenas quando ela não for vazia, podemos utilizar o método `filter` seguido pelo `ifPresent` que já conhecemos:

\[code language="java"\] matriculas.forEach(m -> { m.getCurso() .flatMap(Curso::getDescricao) .filter(d -> !d.isEmpty()) .ifPresent(System.out::println); }); \[/code\]

Está pronto! Muito mais interessante que a solução anterior, não acha?

Há muito mais o que aprender sobre as novas APIs da linguagem. Você pode ver mais das novidades [em nosso livro](http://www.casadocodigo.com.br/products/livro-java8) e no mais [novo curso online do Alura](http://www.alura.com.br/).
