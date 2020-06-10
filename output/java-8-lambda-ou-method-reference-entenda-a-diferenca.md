---
title: "Java 8: Lambda ou method reference? Entenda a diferença"
date: "2016-12-21"
author: "lucas.romao"
authorEmail: "lucas.romao.silva@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O Java 8 trouxe algumas novas funcionalidades e dentre elas a possibilidade de usarmos _lambdas_ e _method references_. Caso ainda não esteja familiarizado com esses recursos, talvez se interesse pelo nosso post de [o mínimo que você deve saber sobre Java 8](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/). As duas _features_ são relacionadas e nos ajudam a reduzir a quantidade de código escrito, com uma abordagem um pouco mais funcional.

Para dar um exemplo de uso de cada uma delas, vamos supor que temos uma lista de nomes e que gostaríamos de imprimir seu conteúdo. Antes do _lambda_ e _method reference_, o código ficaria dessa forma:

\[code language="java"\] List<String> nomes = Arrays.asList(“Lucas”, “Rodrigo”, “Paulo”); for(String nome : nomes) { System.out.println(nome); } \[/code\]

Que nos resultaria na saída:

\[code\] Lucas Rodrigo Paulo \[/code\]

Esse mesmo código, com uso da expressão _lambda_ e o método default `forEach`, poderia ficar assim, em uma única linha:

\[code language="java"\] nomes.forEach(nome -> System.out.println(nome)); \[/code\]

E daria para fazer o mesmo com o _method reference_, deixando ainda mais simples:

\[code language="java"\] nomes.forEach(System.out::println); \[/code\]

Em todos os casos teríamos a mesma saída:

\[code\] Lucas Rodrigo Paulo \[/code\]

Mas qual a real diferença entre essas abordagens? Qual das duas versões você prefere usar no seu dia a dia?

### Existe uma opção melhor para todos os casos?

Ao perceber que as _features_ fazem coisas parecidas é normal se perguntar qual delas é a melhor. A resposta seria depende, **nenhuma delas é a melhor solução para todos os casos**. Entender a diferença entre elas é fundamental para saber quando é o melhor momento de usar cada uma.

### Podemos usar _method reference_ sempre?

É importante ressaltar que nem sempre é possível substituir um _lambda_ por um _method reference_. Para conseguir fazer uma chamada a um _method reference_ é necessário que a invocação de método da direita receba os mesmos parâmetros da esquerda do _lambda_.

No exemplo abaixo temos a nossa lista de nomes e queremos criar uma nova lista apenas com apenas as primeiras 3 letras de cada nome.

\[code language="java" highlight="4"\] List<String> nomes = Arrays.asList(“Lucas”, “Rodrigo”, “Alura”);

List<String> nomesReduzidos = nomes .map(nome -> nome.substring(0, 3)) .collect(Collectors.toList()); \[/code\]

Nesse caso não seria possível usar o _method reference_, uma vez que para chamar o método `substring` seria necessário passar um parâmetro externo, os números 0 e 3, que definem o início e fim da nova _string_.

### Vantagens de se usar _Method Reference_

Apesar de não poder ser usado em todos os casos onde se usa a _lambda_, _method references_ possuem algumas vantagens bem legais. A primeira delas está no fato de usar menos símbolos, o que facilita bastante a legibilidade do código.

Há uma outra vantagem, que é a facilidade de se entender o que está sendo manipulado, uma vez que o tipo do objeto fica sempre muito explícito. Para exemplificarmos: suponha que temos uma classe `Pessoa` e essa classe possui o método `getName`, utilizando _lambda_ para chamar esse método faríamos algo do tipo:

\[code language="java"\] p -> p.getName() \[/code\]

Veja que é necessário entender qual o contexto para poder entender o que é o \*p\*, enquanto com _method reference_, teríamos algo como

\[code language="java"\] Pessoa::getName \[/code\]

Fica bem mais fácil identificar o elemento que está sendo manipulado, percebe? Está explicito.

### Outros usos do _method reference_

Um uso para o _method reference_ que nem sempre é de conhecimento geral é que ele também funciona em métodos que recebem mais de um parâmetro, contanto que a quantidade seja a mesma que a de atributo do objeto que estamos usando.

Imagine que temos um mapa de alunos e sua nota, e gostaríamos de passar isso para um outro mapa.

Usando _lambdas_, nosso código seria:

\[code language="java" highlight="2"\] Map<String, Integer> novoMapaDeNotas = new HashMap<String, Integer>(); notasAlunos.forEach( (nome, nota) -> novoMapaDeNotas.put(nome, nota)); \[/code\]

Veja que o lado esquerdo do lambda recebe dois parâmetros, o nome e a nota. O lado direito também recebe esses parâmetros, nessa mesma ordem. Que tal então fazer dessa forma, com _method reference_:

\[code language="java" highlight="2"\] Map<String, Integer> novoMapaDeNotas = new HashMap<String, Integer>(); notasAlunos.forEach(novoMapaDeNotas::put); \[/code\]

### E quanto a diferença de performance?

Pensando na questão de performance o _lambda_ faz uma chamada de método a mais do que o _method reference_ para executar o método, e as duas abordagens resultam em `bytecode`s diferentes, entretanto para a maioria dos casos essa diferença não chega a afetar performance. Só precisa ser levada em consideração em sistemas críticos onde a performance é essencial, não no `forEach` e manipulações simples do dia a dia.

E você, já tem usado Java 8 e esses recursos em seus projetos de produção? Aqui na Caelum e Alura usamos bastante. Tem até [um curso dando uma visão bem legal](https://www.alura.com.br/curso-online-java8-lambdas) sobre as novidades dessa versão.
