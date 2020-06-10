---
title: "Java 9 na prática: REPL"
date: "2016-04-06"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Estamos acompanhado de perto as novidades do Java 9 e desde já testando cada uma das muitas propostas ([JEPs](http://openjdk.java.net/projects/jdk9/)) que já foram aceitas e integradas em seus builds mais recentes. O release final deverá acontecer apenas em março de 2017, mas a fase em que todas as features deverão estar completas está próxima: faltam apenas dois meses!

Este será o primeiro post de uma série que te possibilitará acompanhar toda a evolução da nova versão da linguagem até o seu tão esperado lançamento oficial. A decisão de qual recurso mostrar nesse primeiro post não foi fácil, são muitas novidades interessantes, mas convenientemente uma delas nos ajudará a testar grande parte das demais: [o REPL](http://openjdk.java.net/jeps/222).

### Read-Eval-Print-Loop (REPL)

Se você já programa em linguagens como Scala, Ruby, Swift e JavaScript entre tantas outras, possivelmente já brincou com algum tipo de REPL. Essa ferramenta nada mais é do que um ambiente simples e interativo onde você pode facilmente executar códigos, oferecendo uma forma bastante efetiva de experimentar novos recursos e APIs.

Vale a pena ler esse trecho presente na motivação do proposta:

> Exploration of coding options is also important for developers prototyping code or investigating a new API. Interactive evaluation is vastly more efficient in this regard than edit/compile/execute and System.out.println. Without the ceremony of class Foo { public static void main(String\[\] args) { ... } }, learning and exploration is streamlined.

### Instalando o último build do JDK 9

Antes de mais nada você precisará instalar um build recente do JDK 9 para testar o conteúdo desse e dos futuros posts. O processo não é trabalhoso, eu recomendo bastante que você não fique apenas na teoria. Além disso, novas versões estão sempre disponíveis, não deixe de atualizar sempre que for experimentar um novo recurso.

Você encontra o [ultimo build do JDK 9 aqui](https://jdk9.java.net/download/) e, após fazer download, basta apontar seu JAVA\_HOME para essa instalação e usar o comando `java -version` pra confirmar que o processo deu certo.

### Download e execução do REPL

Feito isso, você já poderá baixar a implementação de REPL do Java 9, que é conhecida como Kulla (aka JShell). Há sempre a opção de clonar o repositório Mercurial do projeto, compilar e buildar manualmente, mas você não precisa fazer nada disso. Para testar, basta baixar o JAR [disponível nessa instância do Cloudbees do AdoptOpenJDK](https://adopt-openjdk.ci.cloudbees.com/view/OpenJDK/job/langtools-1.9-linux-x86_64-kulla-dev/).

Tudo pronto, `java -jar` nele e você já estará usando o REPL. O build usado nesse post foi o [kulla--20160126010041.jar](https://adopt-openjdk.ci.cloudbees.com/view/OpenJDK/job/langtools-1.9-linux-x86_64-kulla-dev/lastSuccessfulBuild/artifact/kulla--20160126010041.jar), caso você queira usar a mesma versão.

\[code highlight="1,2,3,4,5, 6"\] Turini $ java -jar kulla--20160126010041.jar

| Welcome to JShell -- Version 9-ea | Type /help for help \[/code\]

### Uma nova forma de experimentar código em Java

Chegou a hora de colocar as mãos na massa, mas aqui vai um aviso: isso é viciante. Eu não passo um dia sem usar o REPL desde que instalei ele aqui pela primeira vez, e estou falando de experimentar código do dia a dia, não só dos novos recursos da linguagem.

Podemos começar criando uma nova variável chamada valor, que guardará um inteiro:

\[code highlight="1,2,3,4,5,6"\] int valor = 100; | Added variable valor of type int with initial value 100 \[/code\]

Repare que há um feedback para todas as suas ações. Você pode desativá-lo com o comando `/feedback off,` mas eu pessoalmente gosto. Digite `/feedback` e pressione o tab pra ver as demais opções. Não deixe de usar o tab (autocomplete) sempre que for usar um comando novo pra ver todas as suas possibilidades.

### Com ou sem ponto e vírgula?

Ah, sabe o ponto e vírgula clássico do final da linha? No REPL é opcional:

\[code highlight="1,2,3,4,5,6"\] int valor = 100 | Modified variable valor of type int with initial value 100 \[/code\]

É um tanto conveniente, mas eu sinceramente ainda não me acostumei.

### Trabalhando com variáveis temporárias

E tem mais! Você também não precisa declarar o tipo das variáveis:

\[code highlight="1,2,3,4,5,6"\] semTipo = 100 | Error: | cannot find symbol | symbol: variable semTipo | semTipo = 100 | ^-----^ \[/code\]

Ops, precisa sim! Esse foi só um exemplo pra você ver a saída de um comando inválido. Por outro lado, se você não declarar o nome da variável, o comando será aceito. Experimente digitar apenas o número 100, ou ainda fazer alguma operação matemática sem declarar o tipo e variável de retorno:

\[code highlight="1,2,3,4,5,6"\] valor / 3 | Expression value is: 33 | assigned to temporary variable $3 of type int \[/code\]

O resultado da expressão foi salvo em uma variável temporária, nesse caso chamada $3. Veja que o feedback nos avisou o nome da variável e também o seu tipo. Se quiser testar, você pode fazer um `sysout` na nova variável:

\[code highlight="1,2,3,4,5,6"\] System.out.println($3) 33 \[/code\]

Legal, não é? Você também pode usar o comando `/vars` pra visualizar todas as variáveis disponíveis, incluindo as temporárias:

\[code highlight="1,2,3,4,5,6"\] /vars | int valor = 100 | int $3 = 33 | double pi = 3.141592653589793 \[/code\]

### Apagando e editando variáveis

Quer apagar alguma delas? Use o `/drop` com o ID ou nome da variável:

\[code highlight="1,2,3,4,5,6"\] /drop pi \[/code\]

Ou se preferir, você também pode editá-la com o comando `/edit`.

![Screen Shot 2016-03-29 at 00.35.22](https://blog.caelum.com.br/wp-content/uploads/2066/03/Screen-Shot-2016-03-29-at-00.35.22.png)

Ouch, que editor é esse? Se você é dos meus, vai preferir fazer isso no melhor editor do mundo: o VIM. O comando `/seteditor` te ajudará com isso:

\[code highlight="1,2,3,4,5,6"\] /seteditor vim | Editor set to: vim \[/code\]

### Explorando as APIs do Java

Lembre que você está em um ambiente Java, você pode usar qualquer classe da API. Ah, e você pode sempre usar o TAB como atalho para autocompletar. Na classe `Math`, por exemplo:

![Screen Shot 2016-03-29 at 01.02.16](https://blog.caelum.com.br/wp-content/uploads/2066/03/Screen-Shot-2016-03-29-at-01.02.16.png)

Experimente chamar algum de seus métodos:

\[code highlight="1,2,3,4,5,6"\] Math.random() | Expression value is: 0.12308153862256899 | assigned to temporary variable $6 of type double \[/code\]

### Declarando métodos e classes

O JSheel vai bem além das variáveis e evaluações simples, você também pode criar métodos ou mesmo classes. Lembra do clássico desafio da sequência de _fibonacci_? Que tal resolvê-lo assim:

\[code highlight="1,2,3,4,5,6"\] long fibonacci(int n) { if(n<2) return n; return fibonacci(n-1) + fibonacci(n-2); } | Added method fibonacci(int) \[/code\]

Vamos ver se eu acertei na lógica? Posso fazer um for de 0 a 4 imprimindo a saída:

\[code highlight="1,2,3,4,5,6,7"\] for(int i=0; i<5; i++) System.out.println(fibonacci(i)) 0 1 1 2 3 \[/code\]

Também poderíamos ter criado uma classe `Fibonacci` com esse método dentro, mas criei fora de uma classe justamente pra demonstrar que isso é aceito. Você pode usar os comandos `/methods` e `/classes` para listar todos os métodos e classes disponíveis. O `/drop` e `/edit` também funcionam pra deletar e editar as classes e métodos.

### E quanto aos imports?

E se no lugar do `for` clássico eu quisesse usar a API de `Stream`s do Java 8 para iterar e imprimir a sequência de _Fibonacci_? Não tem erro, basta digitar `Stream` e ops...

\[code highlight="1,2,3,4,5,6"\] Stream.of(1,2,3,4) | Error: | cannot find symbol | symbol: variable Stream | Stream.of(1,2,3,4) | ^----^ \[/code\]

Eu preciso importar essa API! Até agora não tinhamos precisado importar nada, já que só usamos classes e métodos disponíveis nos imports default. Você pode usar o comando `/imports` pra ver o que já está disponível. Para usar a API de `Stream`s, basta importá-la:

\[code highlight="1,2,3,4,5,6"\] import java.util.stream.\* \[/code\]

Perfeito, já podemos usar suas classes a vontade:

\[code highlight="1,2,3,4,5,6"\] Stream.of(1,2,3,4).map(i -> fibonacci(i)).forEach(System.out::println) 1 1 2 3 \[/code\]

Experimente também usar o `/help` para ver uma lista com todos os comandos disponíveis.

### Acompanhando as novidades do Java 9

Vamos sempre postar novidades da nova versão da linguagem aqui e [no blog do Alura](http://blog.alura.com.br/). A [lista de discussão do Java 9](http://mail.openjdk.java.net/mailman/listinfo/jdk9-dev) está bastante movimentada, não deixe de passear por lá. Se você ainda não conhece Java 8, vai gostar de ver [esse curso do Alura](https://www.alura.com.br/curso-online-java8-lambdas?utm_source=blogcaelum&utm_medium=blog&utm_campaign=blogcaelumjava9) e também nosso [curso de Arquitetura da Caelum](https://www.caelum.com.br/curso-arquitetura-java/).

E você, o que achou do REPL? Tenho a impressão que será bastante usado em nosso dia a dia e também nas salas de aula. O que você acha?
