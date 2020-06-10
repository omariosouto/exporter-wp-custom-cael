---
title: "Java 9 na prática: melhorias na API de Collections e mapa"
date: "2016-06-27"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nos posts anteriores [falamos sobre o REPL](https://blog.caelum.com.br/java-9-na-pratica-repl/) e a polêmica [proposta de inferência de variáveis locais](http://blog.alura.com.br/java-9-na-pratica-inferencia-de-tipos/) do Java 9. Pra esse terceiro escolhi mostrar uma proposta que já está em andamento e que, sem dúvida alguma, será muito usada em nosso dia a dia.

[JEP 269: Convenience Factory Methods for Collections](http://openjdk.java.net/jeps/269).

> Definir APIs da biblioteca para tornar conveniente criar instâncias de coleções e mapas com um pequeno número de elementos, aliviando a dor de não termos coleções literais no Java.

Se você já instalou o REPL, pode testar todos os exemplos de código desse post diretamente por ele.

### Entendendo a motivação da proposta

Não é segredo que criar coleções em Java é um processo verboso e chato nem sempre simples. Garantir que elas não sejam modificadas no futuro implica em um pouco mais de código e verbosidade. Quer um exemplo? Uma mapa de IDs e nomes:

\[code language="java" highlight="5"\] Map<Integer, String> users = new HashMap<>(); users.put(1, "Turini"); users.put(2, "Paulo"); users.put(3, "Guilherme"); return Collections.unmodifiableMap(users); \[/code\]

Criamos uma instância da implementação mais comum `HashMap`, armazenamos em uma variável local, invocamos o método `put` para cada conjunto de chave e valor e por fim, usamos o `Collections#unmodifiableMap` pra retornar uma versão não modificável deste mapa. Nada muito complicado, mas foram 5 linhas de código e vários passos para alcançar esse objetivo simples.

### Menos é mais? Nem sempre

Acredite quando eu digo que o problema não está na quantidade de linhas, daria pra fazer isso tudo direto no return com o não muito amado recurso de _double brace initialization_:

\[code language="java"\] return Collections.unmodifiableMap(new HashMap<>() {{ put(1, "Turini"); put(2, "Paulo"); put(3, "Guilherme"); }}); \[/code\]

Se você achou esse código horrível, temos algo em comum. Além de não ser muito recomendado por envolver criação de classes anônimas, o código continua bastante verboso e agora com uma sintaxe esdrúxula. **Nem sempre menos linhas de código implicam em um código melhor**.

### Os frameworks ajudam bastante

Claro que existem diversas alternativas como o [Guava](https://github.com/google/guava) que vem nos ajudando bastante com esse trabalho. Em resumo, ele oferece um conjunto de classes utilitárias que complementam as funcionalidades das nossas Collections além de diversos outros recursos interessantes. Usamos bastante aqui nos projetos da Caelum, Alura e Casa do Código. Você encontra, inclusive, diversos [exemplos de uso no código fonte do VRaptor 4](https://github.com/caelum/vraptor4/search?utf8=%E2%9C%93&q=google.common&type=Code).

O exemplo do mapa anterior ficaria assim:

\[code language="java"\] return ImmutableMap.of(1,"Turini", 2,"Paulo", 3,"Guilherme"); \[/code\]

Legal, não é? Deu uma boa limpada na sintaxe, aproximando um pouco mais das famosas _collections literals_ de linguagens como o Python, Groovy e Scala. Em Groovy, por exemplo, o código seria dessa forma:

\[code language="ruby"\] def users = \[1:"Turini", 2:"Paulo", 3:"Guilherme"\] \[/code\]

Por sinal, o Brian Goetz, arquiteto da Oracle, vem falando sobre a possibilidade das _collection literals_ em Java desde antes do Java 8. Ele inclusive [já esboçou uma JEP de pesquisa](http://openjdk.java.net/jeps/186) com algumas possibilidades. As features da proposta que vou mostrar neste post é um dos frutos dessa idéia inicial.

### JEP 269: Convenience Factory Methods for Collections

Com a introdução dessa proposta do Java 9 finalmente poderemos criar o mapa assim:

\[code language="java"\] return Map.of(1,"Turini", 2,"Paulo", 3,"Guilherme"); \[/code\]

Bem parecido com o estilo mais enxuto do Guava. E ainda existem algumas variações de sintaxe para quando você tem mapas um pouco maiores, que precisam ser construídos dinamicamente e etc. Repare:

\[code language="java"\] Map.ofEntries( entry(1, "Turini"), entry(2, "Paulo"), entry(3, "Guilherme"), entry(4, "Sérgio") ); \[/code\]

O `entry` que usamos aqui é um import estático de um outro _factory method_ criado na interface `Map`:

\[code language="java"\] Map.Entry<K,V> entry = Map.entry( K k, V v ); \[/code\]

O ganho foi grande, e vai bem além dos Mapas.

### Criando listas e sets com os novos métodos

Quantas vezes você usou o `Arrays.asList(...)` essa semana? A boa notícia é que finalmente podemos fazer algo parecido diretamente pela interface `List`:

\[code language="java"\] List.of("Turini", "Paulo", "Guilherme"); \[/code\]

E dá pra fazer o mesmo com a interface `Set`. O exemplo de motivação que foi usado na página da proposta é bem legal, mostrando bastante o ganho em legibilidade e verbosidade da nova abordagem comparada com a atual. Essa é uma das formas bastante usada atualmente, sem ajuda de frameworks:

\[code language="java"\] Set<String> set = Collections.unmodifiableSet( new HashSet<>(Arrays.asList("a", "b", "c")) ); \[/code\]

Veja que o **unmodifiableSet** é criado a partir de um novo `HashSet`, que por sua vez é criado com a cópia de uma outra Collection. Da forma atual basta fazer um `Set.of(...)` e o trabalho estará feito:

\[code language="java"\] Set<String> set = Set.of("a", "b", "c"); \[/code\]

### Lembrando que elas não podem ser modificadas

Vale lembrar que o objetivo dessa proposta é permitir uma forma fácil de criar **instâncias não modificáveis** das collections e mapas.

> Fornecer factory methods estáticos nas interfaces das coleções e mapas que vão criar instâncias compactas e não-modificáveis.

Pra testar, basta criar uma lista usando o novo método:

\[code language="java" highlight="1,2,3,4,5,6"\] jshell> List.of("não", "posso", "ser", "editada"); $1 ==> \[não, posso, ser, editada\] \[/code\]

E depois experimente adicionar um novo elemento:

\[code language="java" highlight="1,2,3,4,5,6"\] jshell> $1.add("posso?"); | java.lang.UnsupportedOperationException thrown | at Collections$UnmodifiableCollection.add (Collections.java:1056) | at (#2:1) \[/code\]

Uma `UnsupportedOperationException` será lançada. Se você percorrer os elementos da lista verá que estão todos lá, da mesma forma que foi criada:

\[code language="java" highlight="1,2,3,4,5,6"\] jshell> $1.forEach(System.out::println); não posso ser editada \[/code\]

### Outras novidades

Em breve quero escrever sobre [a proposta do jigsaw](http://openjdk.java.net/projects/jigsaw/), que permite a modularização da plataforma e o JDK. Passei o final de semana brincando com ele e vi que já está em uma etapa bem interessante. Se tiver curioso sobre algum outro assunto, não deixe de me contar nos comentários.

Ah, e falando em Collections, na Alura agora temos [um novo curso do Paulo](https://www.alura.com.br/curso-online-java-collections) mostrando tudo que você precisa pra dominar as principais classes dessa fundamental API. Também falamos bastante disso em no [curso presencial de Java e Orientação a Objetos](https://www.caelum.com.br/curso-java-orientacao-objetos/) da Caelum.

E você, o que achou dessa novidade? Tem gostado das propostas da linguagem?
