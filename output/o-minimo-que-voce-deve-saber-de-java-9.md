---
title: "O mínimo que você deve saber de Java 9"
date: "2017-09-25"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O título não é uma coincidência, esse post foi criado com o mesmo intuito [daquele que eu escrevi em 2014 junto com o Paulo Silveira](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/), anunciando as principais alterações da nova versão da linguagem Java. Com pequenas e **enormes mudanças**, o JDK 9 já está [disponível para download](http://www.oracle.com/technetwork/java/javase/downloads/jdk9-downloads-3848520.html).

São muitas as novidades e eu vou compartilhar aqui um resumo das principais delas, além de passar vários links e recursos que você pode utilizar para se atualizar.

### Um java interativo

Ao baixar e configurar o Java 9 em seu ambiente você já consegue utilizar uma das grandes introduções da versão, que é a nova ferramenta _REPL (Read-Eval-Print Loop)_.

Ela traz uma forma muito mais interessante de experimentar código, sem toda burocracia e verbosidade das classes com método _main_. Basta abrir o console, digitar `jshell` — que é o nome da ferramenta — e sair escrevendo código Java!

\[code\] turini ~ $ jshell | Welcome to JShell — Version 9 | For an introduction type: /help intro \[/code\]

Eu li a documentação completa da nova especificação da linguagem com o JShell aberto, experimentando cada uma das mudanças e com zero preocupações de setup. Foi uma experiência incrível.

Nesse [outro post eu mostro vários exemplos](https://blog.caelum.com.br/java-9-na-pratica-repl/) com os principais detalhes e possibilidades.

E é muito legal também saber que você pode escrever algum snippet de código nele e depois compartilhar, pra que outras pessoas possam facilmente testar sua implementação — ou mesmo modificá-la e te enviar de volta.

Quer um exemplo? Podemos criar um método com o mesmo código usado no post de Java 8, para retornar a média de tamanho de uma lista de palavras.

Vou abrir o JShell e escrever o código em um método chamado `averageLenght`:

\[code language="java"\] jshell> public double averageLenght(List<String> words) { …> return words.stream() …> .mapToInt(String::length).average() …> .orElse(0); …> } | created method averageLenght(List<String>) \[/code\]

Agora criar uma lista de palavras:

\[code language="java"\] jshell> List<String> words = List.of(“podemos”, “criar”, “listas”, “assim”); \[/code\]

E experimentar minha implementação:

\[code language="java"\] jshell> averageLenght(words) ==> 5.2 \[/code\]

Funciona!

Para salvar esse código e compartilhar, vou usar o comando `/save`.

\[code\] jshell> /save average-lenght \[/code\]

O arquivo _average-lenght_ foi criado. Experimente agora [baixar esse arquivo](https://drive.google.com/file/d/0B91XqLM4xfbYOFJhaUZVY0s4NWM/view?usp=sharing) e depois abrir o seu jshell com ele como a seguir:

\[code\] turini ~ $ jshell average-lenght \[/code\]

A lista de palavras e o método `averageLenght` já vão estar aí, disponíveis para você testar.

Imagina que legal no fórum da Alura ou GUJ, por exemplo, você conseguir enviar o pedacinho de código problemático para que o pessoal abra e te ajude encontrar o problema? Você consegue criar classes, métodos e inclusive usar dependências externas — JARs de bibliotecas etc — nesse ambiente interativo, e tudo que a pessoa precisa é ter o Java 9 instalado.

### Um Java reativo

O JDK 9 também evoluiu a arquitetura do pacote `java.util.concurrent`, com a introdução da _Flow API_ que implementa a [especificação de Reactive Streams](http://www.reactive-streams.org). A API é composta pela classe abstrata `Flow` e suas interfaces internas, que seguem o padrão de publicador e assinante -- _publisher/subscriber pattern_.

\[code\] java.util.concurrent.Flow java.util.concurrent.Flow.Publisher java.util.concurrent.Flow.Subscriber java.util.concurrent.Flow.Processor \[/code\]

> Apesar do nome extremamente familiar, _Reactive Streams_ não tem relação direta com a API de Streams do Java 8. Essa é uma confusão completamente comum, mas nesse caso o termo Streams remete a fluxo, fluxos reativos. Um passo criado a partir do caminho definido pelo [famoso manifesto reativo](https://www.reactivemanifesto.org).

Ele vem pra resolver o famoso problema de processamentos assíncronos em que um componente de origem envia dados sem saber ao certo se isso está em uma quantidade maior do que aquela com a qual o consumidor pode lidar.

![](https://blog.caelum.com.br/wp-content/uploads/2099/01/Screen-Shot-2017-09-24-at-14.43.35.png)

Perceba que na imagem o componente de origem está recebendo e processando várias informações, mas muitas delas estão bloqueadas enquanto as outras estão sendo processadas. Os dados chegam em uma velocidade muito maior do que podem ser atendidos. Com a Flow API você consegue resolver esse e outros problemas avançados em execuções assíncronas com back pressure.

O fluxo seria assim:

![](https://blog.caelum.com.br/wp-content/uploads/2099/01/Screen-Shot-2017-09-24-at-14.43.52.png)

No lugar de a origem empurrar uma quantidade arbitrária de dados para o destino, é o destino que puxa apenas a quantidade de dados que ele certamente poderá atender. Só então, os dados são enviados e na quantidade certa.

No exemplo a seguir estamos criando um publicador e registrando implicitamente um consumidor que apenas imprime as `String`s conforme elas são enviadas:

\[code language="java"\] jshell> SubmissionPublisher<String> publisher = new SubmissionPublisher<>(); jshell> publisher.consume(System.out::println); jshell> List.of("esse", "código", "é", "assíncrono").forEach(publisher::submit); esse código é assíncrono \[/code\]

### Um Java modular

Depois de mais de 20 anos muitos dos problemas da antiga estrutura monolítica da plataforma Java foram resolvidos com a introdução de um novo sistema de módulos e a modularização do próprio JDK. Neste [outro post eu mostro o Jigsaw](https://blog.caelum.com.br/java-9-na-pratica-jigsaw/) e o que muda na estrutura de seus projetos, além de mencionar brevemente como ficou a divisão de suas principais APIs.

No Java 9 tudo é modular, mas **você não precisa sair migrando os seus projetos para usar essa nova versão**. Especialmente por motivos de compatibilidade, projetos que não definem explicitamente o uso do sistema de módulos vão funcionar normalmente — internamente é declarado um `unamed module`, com acesso a todos os módulos da plataforma de forma parecida ao que acontece nas versões anteriores.

Apesar disso, depois de conhecer um pouco mais sobre os benefícios dessa migração eu arriscaria dizer que você vai querer migrar. Uma das grandes vantagens dessa nova estrutura é que, diferente da abordagem atual do _classpath_, as informações do módulo precisam ficar **disponíveis da mesma forma durante as fases de compilação e execução**. Isso garante uma integridade muito maior nos projetos, evitando problemas da abordagem atual do legado classpath — como o famoso JAR hell —  ou, pelo menos, reportando-os muito antes, em tempo de compilação.

Outro ganho enorme é no encapsulamento dos projetos, já que agora **ser público não significa mais ser acessível**. Em um sistema modular você precisa definir explicitamente o que pode ou não ser acessado por fora do módulo, e isso guia a construção de APIs com designs mais lógicos.

No exemplo a seguir o arquivo `module-info.java`, responsável pela definição de um módulo, exemplifica um caso em que o módulo `br.com.caelum.desktop` precisa do módulo base do Java FX para funcionar, e deixar acessível apenas seu pacote de componentes:

\[code language="java"\] module br.com.caelum.desktop { requires javafx.base; exports br.com.caelum.desktop.components; } \[/code\]

Ah, e em projetos modulares você consegue criar imagens de execução customizadas com apenas o pedacinho da JRE que você está usando! Podemos falar que essa é uma extensão aos [compact profiles do JDK 8](https://blog.caelum.com.br/compact-profiles-no-java-8/), mas que funcionam de forma muito mais interessante já que você literalmente só carrega o que precisa. Muitos projetos pequenos podem ter só o `java.base`, no lugar de todos os 94 módulos e suas milhares de classes. Isso tem uma série de vantagens de performance e consumo de memória, já que a aplicação fica com um _footprint_ inicial muito menor.

### Novas APIs

A versão também recebeu diversas novas APIs. Um destaque especial vai para a de _HTTP/2 Client_, que possui uma interface publica completamente repaginada ante ao legado _HttpURLConnection API_ e com suporte para requisições _HTTP/2_ e _WebSockets_.

Um exemplo de código para retornar o conteúdo do corpo de um _request_ seria:

\[code language="java"\] String contentBody = newHttpClient().send(  newBuilder()  .uri(new URI(“https://turini.github.io/livro-java-9/"))  .GET() .build(), asString()) .body(); \[/code\]

O mais interessante dessa API é que ela é o piloto de um novo conceito da linguagem, que são os **módulos em incubação**. A ideia é permitir que APIs passem por um período de testes antes de entrar definitivamente para a linguagem, com objetivo de reduzir a possibilidade de introdução de erros na plataforma, que tem o forte peso da retrocompatibilidade.

Módulos em incubação ainda não fazem parte do Java SE. Eles ficam em um pacote `jdk.incubator` e não são resolvidos por padrão na compilação ou execução de suas aplicações. Para testar esse meu exemplo no jshell, por exemplo, você precisa iniciar a ferramenta com essa opção explicitamente:

\[code\] turini ~ $ jshell --add-modules jdk.incubator.httpclient | Welcome to JShell — Version 9 | For an introduction type: /help intro \[/code\]

O JDK 9 também traz uma nova _API de logging_, que te possibilita criar um provedor padrão de mensagens que poderá ser usado tanto em seu código como no do próprio JDK. E uma API de _Stack-Walking_, que tira proveito dos poderosos recursos da API de Streams para que você consiga passear pela Stack de sua aplicação de forma extremamente eficiente. Com ela podemos facilmente coletar em uma lista todas as classes de um pacote específico, ou alguma outra condição especial de filtro:

\[code language="java"\] StackWalker.getInstance().walk(stream -> stream.filter(frame -> frame.getClassName().contains("br.com.caelum") )) .collect(toList()); \[/code\]

Eu consigo ver um futuro próximo em que as IDEs e ferramentas de _profilling_ tiram bastante proveito dessa API para fornecer alternativas performáticas e poderosas para analise e investigação de problemas de runtime.

### Diversas mudanças nas APIs

Eu mostrei [nesse post](https://blog.caelum.com.br/java-9-na-pratica-melhorias-na-api-de-collections-e-mapa/) que as Collections receberam diversos _factory methods_ trazendo uma forma mais próxima aos _collection literals_ para criação de listas, sets e mapas.

\[code language="java"\] Map.of(1,"Turini", 2,"Paulo", 3,"Guilherme"); List.of(1, 2, 3); Set.of("SP", "BSB", "RJ"); \[/code\]

O Stream também recebeu novos métodos como `dropWhile`, `takeWhile`, `ofNullable` e uma sobrecarga do `iterate` que permite definir uma condição para que a iteração termine. Repare a semelhança com o clássico for com index no exemplo em que imprime números de 1 a 10:

\[code language="java"\] Stream .iterate(1, n -> n<=10, n -> n+1) .forEach(System.out::println); \[/code\]

Além das mudanças no Stream em si, diversas outras APIs receberam métodos que tiram proveito de seus recursos. Um exemplo seria o `Optional`, que agora consegue projetar seus valores diretamente para o Stream:

\[code language="java"\] Stream<Integer> stream = Optional.of(ids).stream(); \[/code\]

Essa transformação é extremamente útil quando você precisa aplicar diversas operações nos valores e tirar proveito da característica lazy da API.

O `java.time` também recebeu uma forma bastante interessante de retornar streams com intervalo de datas:

\[code language="java"\] Stream<LocalDate> dates = LocalDate.datesUntil(jdk10Release); \[/code\]

E o `Scanner` agora possui métodos como o `findAll` onde, dada uma expressão regular, retorna um stream de possíveis resultados.

\[code language="java"\] String input = "esperei 3 anos pelo lançamento do java 9";

List<String> matches = new Scanner(input) .findAll("\\\\d+") .map(MatchResult::group) .collect(toList()); \[/code\]

### Extensão aos recursos da linguagem

Além de novos métodos e APIs, a linguagem recebeu um suporte maior na inferência de tipos e nos recursos existentes. O _try-with-resources_ agora pode usar variáveis efetivamente finais, sem precisar re-instanciar como em sua versão agora antiga.

O código que era assim:

\[code language="java"\] public void read(BufferedReader reader) {

try (BufferedReader reader = reader) { //... } } \[/code\]

Agora pode ser escrito sem a necessidade de criar um novo tipo e da re-atribuição:

\[code language="java"\] public void read(BufferedReader reader) {

try (reader) { //... } } \[/code\]

O uso do operador diamante em classes anônimas e suporte aos métodos privados em interfaces — para reutilizar código dos _default methods_ sem quebrar encapsulamento — são algumas das várias outras possibilidades. Muitas delas estão definidas na proposta [Milling Project Coin](http://openjdk.java.net/jeps/213), que recebeu esse nome por ser um aperfeiçoamento dos recursos introduzidos no _Project Coin_ do JDK 7 — como o próprio operador diamante.

### Performance

As melhorias relacionadas a performance também são incríveis. Um destaque especial foi a adoção do G1 como _Garbage Collector_ padrão, como já era esperado. Ele é um algoritmo bem mais previsível, que executa seu trabalho em diferentes threads e em uma frequência muito maior, compactando o heap de memória durante a execução. O G1 também faz o reaproveitamento de Strings e tem diversos outros benefícios interessantes.

Por falar em Strings, elas agora possuem seu valor representado de uma forma muito mais eficiente em memória. Se você conferir na implementação das versões anteriores da linguagem, perceberá que uma String guarda seu valor interno em um array de caracteres, que mantém dois bytes para cada um deles.

\[code language="java"\] private final char value\[\]; \[/code\]

O problema da abordagem atual é que, na maior parte dos casos, as Strings usam valores em formato _ISO-8859–1/Latin-1_ que precisam de apenas um byte por caractere. Em outras palavras, poderíamos usar metade do espaço!

Bem, é o que estamos fazendo agora com Java 9! Experimente abrir o JShell e ver como esse campo de valor é declarado hoje:

\[code language="java"\] jshell> String.class.getDeclaredField("value") private final byte\[\] value; \[/code\]

Um array de bytes!

Com isso usamos apenas um byte, que será o suficiente na esmagadora maioria dos casos. Para os demais, foi adicionado um campo adicional de 1 byte para indicar qual o encoding que está sendo usado. Isso será feito automaticamente, baseado no conteúdo da String.

Essa proposta é conhecida como [Compact Strings](http://openjdk.java.net/jeps/254).

Ufa! Esse é resumo mínimo do que entrou na nova versão, mas acredite quando eu digo que tem muito mais além do que foi aqui mencionado. A própria Oracle amadureceu bastante suas documentações e trabalho na comunidade, com vários artigos e tutoriais bem interessantes. Vou deixar alguns links aqui pra quem quiser se aprofundar mais.

### Onde estudar mais?

Entre os documentos oficiais dos arquitetos da plataforma, você certamente poderá se interessar em dar uma olhada [nos release notes](http://www.oracle.com/technetwork/java/javase/9all-relnotes-3704433.html), com links inclusive para alguns tutoriais de migração, na [lista](http://openjdk.java.net/projects/jdk9/) com todas as especificações que entraram, além de [alguns videos](https://www.oracle.com/java/java9-screencasts.html) de experts introduzindo as mudanças.

No livro [Java 9: Interativo, reativo e modularizado](https://www.casadocodigo.com.br/products/livro-java9) eu entro a fundo nas principais mudanças colocando tudo em um projeto prático, que no final é modularizado e você sente com as mãos na massa como é o processo de migração e os grandes benefícios da nova arquitetura modular da plataforma.

Também falei um pouco sobre as mudanças [em um episódio da Alura Live](https://www.facebook.com/events/668438993352474/), junto com o Gabriel — nosso host — e o Phil, que trabalha conosco no time de desenvolvimento da Alura.

Logo você também deve ver mais um monte de conteúdo aberto nosso sobre o assunto, entrando a fundo em detalhes específicos das muitas novidades da versão.

E ai, o que achou das mudanças?
