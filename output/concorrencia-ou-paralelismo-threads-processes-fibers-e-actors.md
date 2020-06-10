---
title: "Concorrência ou paralelismo: Threads, Processes, Fibers e Actors"
date: "2009-09-25"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quanto mais processamento é necessário para resolver um problema, mais nos deparamos com projetos que envolvem questões de paralelismo, concorrência e distribuição de tarefas. Quais seriam as opções que existem e quais as características de cada uma delas?

Como já sabemos, o problema em escrever código para ser rodado paralelamente é grande quando temos acesso a dados compartilhados (shared memory) e existe uma ou mais escritas ocorrendo no mesmo dado.

A abordagem dos monitores do Java (`wait`/`notify`) e das [pthreads do C](http://en.wikipedia.org/wiki/POSIX_Threads) estão ligadas a objetos que sinalizam quando podemos e quando não devemos escrever nessa memória compartilhada e, apesar de extremamente poderosa, vem se mostrando difícil de ser trabalhada e mantida pelos desenvolvedores. Tudo isto está conectado com a questão de concorrência preemptiva, onde um escalonador agenda e executa as threads/processos, inclusive em mais de um processador simultaneamente.

Existem duas outras alternativas que ganham força hoje em dia: os **atores e processos** de linguagens como Erlang e Scala, e as **fibers** do ruby 1.9.

Em linguagens como Erlang, onde as "variavéis" são imutáveis, não existem efeitos colaterais ao se executar uma função, portanto podemos dizer que a "memória compartilhada" não sofre dos males das outras linguagens, já que não há estado que possa ser alterado. Muitos dizem que [o grande problema das linguagens atuais é justo o estado](http://blog.lucindo.com.br/2008/05/22/state-youre-doing-it-wrong/).

Qual a grande vantagem da imutabilidade fazer parte de uma lingaugem? Duas invocações poderiamm ser paralelizadas pelo interpretador/compilador, como o exemplo de pseudo código Java abaixo mostra:

\[java\] String geraCorpo(Movimentacao\[\] movimentacoes) { String conteudo = ""; for (Movimentacao m : movimentacoes) { conteudo += m.geraConteudo(); } return conteudo; }

String geraRodape(Informacao\[\] informacoes) { String rodape = ""; for(Informacao info : informacoes) { rodape += info.geraConteudo(); } return rodape; }

String processaRelatorio (Movimentacao\[\] movimentacoes, Informacao\[\] informacoes ) { return geraCorpo(movimentacoes) + geraRodape(informacoes) } \[/java\]

Repare que quando invocarmos o `processaRelatorio` as duas outras funções seriam executadas sequencialmente, mas um compilador/interpretador inteligente poderia executar as duas invocações em paralelo e concatenar o resultados assim que ambos estiverem disponíveis. Ele poderia ir mais além, invocando partes de cada laço em paralelo em processadores diferentes. Isso se tivessemos a garantia de que não haveria efeitos colaterais ao invocarmos cada uma dessas funções, garantia a qual não se da para fazer com o Java.

**Funções que não causam efeitos colaterais** permitem otimizações impressionantes por parte do interpretador/compilador e são uma ótima alternativa para fazer uso de todos os _cores_ que temos disponibilizados (número que só cresce) evitando o desperdício.

Por outro lado, existem partes do sistema que podem ser manualmente agendados para executarem [concorrentemente e - as vezes - até mesmo em paralelo](http://www.javaworld.com/javaworld/jw-09-1998/jw-09-threads.html?page=1) (sendo que [as duas coisas são diferentes)](http://existentialtype.wordpress.com/2011/03/17/parallelism-is-not-concurrency/).

Em sites de verificação de rotas de vôo, diversas requisições para diferentes sites podem ser executadas simultaneamente com uma tarefa extra que que concatena todos os resultados. Para que essa tarefa final receba as respostas, precisamos trocar mensagens entre aqueles que processam as diversas requisições e a tarefa que concatena as informações. Alguém responsável por pegar informação de um site seria aquele que recebe o identificador do site que será pesquisado (por exemplo tam, gol etc), os critérios de pesquisa, e quem é responsável pela tarefa final, de concatenar os resultados.

Dada as 3 informações, esse alguém executa uma requisição que é blocante, isto é, segura a sua thread até obter a resposta, executa transformações (parseia) a mesma, e termina notificando o responsável da finalização do processo, como o pseudo-código abaixo:

\[scala\] class Agente { def recebe(site, pesquisa, callback) { resposta = http.request(site + "?search\_for=" + pesquisa) resultado = parseia(resposta) callback.recebe (resultado) } } \[/scala\]

E então alguém responsável por concatenar os resultados:

\[scala\] class TarefaFinal { final = \[\] def recebe(resultado\_parcial) { final.add resultado\_parcial } def aguarda\_ate\_o\_fim { // aguarda ate o fim de todos os parciais // e entao retorna o resultado total } } \[/scala\]

A tarefa mãe seria quem dispara diversos agentes que farão as pesquisas e o agente final, que colherá os resultados, ainda com pseudo-código:

\[scala\] class Pesquisador { def busca(opcoes) { concatenador = new TarefaFinal sites = {'www.tam.com.br', 'www.voegol.com.br', 'www.aerolinhascaelum.com.br'} for site in sites { new Agente().envia (site, opcoes, concatenador) } return concatenador.aguarda\_ate\_o\_fim } } \[/scala\]

Em Java, por exemplo, utilizariamos uma thread para cada agente, para que cada requisição blocante não impedisse a execução das outras em **paralelo**. Com isso, podemos utilizar todos os processadores de uma máquina, mas o peso de gerenciar muitas threads em Java é algo que queremos evitar.

Outra solução, ainda em Java, é utilizar a api de NIO, permitindo executar tarefas de entrada e saída sem que a thread atual aguarde o resultado final. Por um lado essa alternativa aumentaria a possibilidade de atendermos muito mais requisições, porém há um custo aí de ficar perguntando se o resultado "já está pronto". É um trade-off entre performance e escalabilidade. Essa abordagem pode ser feita de maneira um pouco mais elegante e transparente através de [bibliotecas de dataflow concurrency](http://code.google.com/p/gparallelizer/wiki/DataflowConcurrency).

A solução que algumas linguagens apresentam é chamada de processos. Esses processos serão executados concorrentemente, e, como o código é escrito visando minimizar efeitos colaterais, em grande parte ele poderá ser executado em paralelo. Esses processos são mais leves pois não implicam em um overhead de informações devido a troca do estado de memória e de pilha de execução quanto a implementação de threads em Java.

Ainda mais impressionante é a capacidade de executar cada um desses processos em máquinas diferentes da atual. Se o código do agente é facilmente serializável, ou que permita um rápido bootstrap em diversas máquinas remotas, fica muito barato rodar esses processos em diversas máquinas, inclusive efetuando concatenações parciais, até obter o resultado final, se aproximando das idéias do [Map Reduce](http://en.wikipedia.org/wiki/MapReduce), [descrito pelo google em 2004](http://labs.google.com/papers/mapreduce-osdi04.pdf).

Por fim, existe a opção de [co-rotinas que voltam a ser abordadas](http://en.wikipedia.org/wiki/Coroutine), nesse caso no mundo Ruby, [através de Fibers](http://www.infoq.com/news/2007/08/ruby-1-9-fibers).

O uso de Fibers permite escrever um código onde N processos mantem a comunicação entre si, dizendo uns aos outros quando é o momento de deixar de executar para dar espaço para execução do outro processo. Muitos se lembrarão do método `Thread.yield()` de Java, que tem um comportamento definido parecido com o mencionado. Mas ele **não garante que a thread atual para** e deixa outro processo rodar.

Por outro lado, fibers garantem que aquele processo pare momentaneamente para deixar aquele que o invocou continuar sua execução. A grande vantagem está em poder separar, com isso, o código que controla diversos processos (fibers) e o código que executa cada processamento. A desvantagem está em que os processos estão rodando concorrentemente, não em paralelo.

Hoje em dia, outro [assunto que está sendo muito discutido](http://research.microsoft.com/en-us/um/people/simonpj/papers/stm/index.htm#beautiful) é a questão da [memória transacional](http://en.wikipedia.org/wiki/Software_transactional_memory). Uma maneira que, resumidamente, permite criar transações em cima de suas variáveis de memória para que, em casos onde imutabilidade de dados compartilhados não é possível, haja um controle automático.

Mas essa abordagem apresenta certas dificuldades e detalhes importantes de serem estudados, como as dificuldades de se fazer operações de IO durante uma transação dessas - o problema de "como efetuar o rollback do lançamento de um foguete?". Quem implenta uma possível solução para isso é o haskell, através dos [monads](http://en.wikipedia.org/wiki/Monad_(functional_programming)).

Só enxergaremos melhor as todas as vantagens e desvantagens de cada uma dessas opções aqui citadas quando elas quando elas forem parte do dia a dia de muitos programadores, um futuro não tão distante.

Agradeço ao [Rafael Ferreira](http://blog.rafaelferreira.net/) e ao [Renato Lucindo](http://blog.lucindo.com.br/) pelas discussões e revisões.
