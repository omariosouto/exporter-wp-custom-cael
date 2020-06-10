---
title: "Vazamento de memória e de conexões"
date: "2008-09-02"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Descrevo aqui a minha aventura junto com diversos desenvolvedores da Caelum: Cauê Guerra, Filipe Sabella, Anderson Leite, Pedro Mariano e o Pedro Matiello para resolver um grande problema que ocorria em um dos nossos projetos.

Assim como quando um avião cai, não é um bug que gera um grande problema, mas sim uma série de fatores que contribuem para a dificuldade na hora de encontrar complicações estruturais de um projeto.

Vamos aos fatos, que incluem bugs e vazamentos em **bibliotecas** **famosas**, profiling e _open call hierarchy_ de eclipse.

**Sintoma: o servidor web parava de responder para qualquer página, exceto a tela de login**

Nesse caso, o servidor entrava na tela de login mas ao tentar submete-la ou acessar qualquer outra funcionalidade o browser não recebia resposta alguma do servidor web.

A primeira coisa a pensar era que o processador estaria a **100%**, que é o caso comum em laços infinitos ou threads gulosas além da conta. Mas um comando rápido no servidor (_top_) mostrava que o processador estava a **0%**. Claro, se estivesse a 100%, teríamos duas opções:

1. rodar o profiler e detectar o método que estaria consumindo 100% do cpu
2. imprimir todas as threads do sistema (com Thread.métodos estáticos) e ver o stack trace de cada uma delas... detectaríamos o mesmo ponto.

Mas como nosso problema não era esse, uma possível causa levantada pelo Rafael Cosentino foi de que poderia haver algum deadlock, travando todas as requisições. Pensando na dificuldade de detectar tal falha, e acreditando que o problema estava relacionado com o consumo de memóriaapesar de não haver `OutOfMemoryError`s), decidimos usar o [JProfiler](http://www.ej-technologies.com/products/jprofiler/overview.html).

O primeiro passo foi rodar o profiler no [Stella Boleto](http://stella.caelum.com.br/boleto-index.html) para verificar se ele estava com algum leak, pois sabíamos que o mesmo consome uma memória razoável durante o processo de geração do boleto, devido a imagem de background de alta resolução ser carregada num `BufferedImage`. Memória a qual deveria ser liberada. Ao rodar o profiler, o Caue Guerra, executou diversas threads em paralelo com invocações complexas do Stella Boletos. Durante e após as execuções, não houve memory leak algum, e o garbage collector consumia os objetos corretamente.

Sem nenhum ponto especial, teríamos que fazer o profiling da aplicação inteira.

Foram várias tentativas com profiling remoto offline, o código java invocava a api de profiling para depois analisarmos os resultados em nossas máquinas, porém um **bug do JProfiler com o Apache Tomcat 6.0.16** inviabilizou tal análise. Fomos então rodar a aplicação no Jetty 7 (apontando para a mesma configuração do tomcat) sem que nenhum usuário da aplicação live notasse a mudança: uma migração transparente de servlet containers executada com êxito, [assim como ocorreu no GUJ](https://blog.caelum.com.br/2008/06/27/melhorando-o-guj-jetty-nio-e-load-balancing/).

Em paralelo, o Tomcat foi atualizado para 6.0.18, que funciona com a última versão do JProfiler corretamente e levantamos o mesmo com apenas 64 megas de ram (para estressar o garbage collector mais frequentemente). Rodando o Tomcat em outra porta, mas acessando o mesmo banco de produção, permitiu que testássemos junto ao cliente exatamente as funcionalidade que ele julgava "lentas".

Em algum ponto durante o uso do JProfiler, o mesmo indicou que não havia nenhum deadlock de threads, bastando para ele verificar se existem threads esperando por monitores de maneira cruzadas.

**Primeiro sinal de leak**: Executamos algumas tarefas no sistema e vimos que o número de objetos crescia de maneira adequada, porém, ao rodar o GC, alguns objetos ficavam para trás... objetos que são entidades do projeto! É comum o número de `String`s, arrays de char e de bytes crescerem, mas entidades do nosso próprio sistema? E agora? Por que isso acontece? Alguém continuou mantendo referências para objetos do projeto que não deveriam estar referenciados. Novamente o profiler vai ajudar.

**Segundo sinal de leak**: Cutucando mais um pouco percebemos que algumas `Session`s do hibernate tambem não estavam sendo fechadas nem coletadas! Essa costuma ser uma forte indicação de vazamento de conexão. E no caso do Hibernate, isso poderia vir a gerar mais leak ainda, dado que muitas entidades ficariam presas no cache de primeiro nível, já que a sessão continuava aberta.

\[caption id="" align="alignright" width="478" caption="Objetos do hibernate apos o gc"\]![](http://farm4.static.flickr.com/3228/2818736467_087744fd45_o.png "Objetos do hibernate apos o gc")\[/caption\]

Mas olhando a fundo, percebemos que a maior parte dessas sessões, exceto uma, estava injetada em objetos de modelo na sessao web. Isto é, são sessões usadas pelos nossos [Repositories](https://blog.caelum.com.br/2007/06/09/repository-seu-modelo-mais-orientado-a-objeto/), que faz todo o sentido para o projeto e não é um memory leak, pois a sessão que era referenciada deveria estar fechada e com isso os objetos de cache de primeiro nível dele, coletados.

Sendo assim, partimos para testar funcionalidades que acreditavamos ter consumo grande de memória.

**Primeiro sinal de consumo excessivo de memória**: Encontramos uma funcionalidade que levantava cerca de 40 mil objetos na memória. Com isso, o consumo era de cerca de 40 megas de objetos só nesse select... mas o Tomcat estava configurado para 64 megas? O que acontecia? O consumo de memória aumentava, o garbage collector era invocado de sua maneira parcial, até que o consumo chegava ao topo, quando garbage collector full era rodado, pedindo mais memória para o sistema operacional.

Mas 64 ram não era espaço suficiente, então rodava o garbage collector full novamente, passando por todos os objetos da memória novamente, liberando mais um pouco e deixando um pouco mais de espaço, mas ainda não o suficiente. Cada vez mais, em intervalos de poucos segundos, o garbage collector era rodado e liberava menos espaço novo do que antes, tudo isso ainda na mesma requisição que usaria 40 megas... até que os espaços são tão pequenos que o processador fica a **100% de processamento**, até acabar a memória e lançar o `OutOfMemoryError`. Nesse meio tempo, o servidor fica praticamente sem dar respostas, dado o alto uso de CPU.

\[caption id="" align="aligncenter" width="544" caption="Garbage collector rodando apos requisicao que criava diversos objetos: note o garbage collector full rodado no final."\]![Garbage collector rodando apos requisicao que criava diversos objetos](http://farm4.static.flickr.com/3084/2819581552_262a85e49e_o.png "Garbage collector rodando apos requisicao que criava diversos objetos")\[/caption\]

 Ok, encontramos um problema. **Solução**: filtrar esses 40 mil objetos para trazer menos objetos para a memória de uma vez só. No nosso caso uma simples busca por esses objetos foi o suficiente. O cliente não queria paginação e já havia recusado a paginação que havia sido feita no Sprint anterior.

Em outros casos, poderiamos usar resultados do hibernate que armazenam uma referencia somente para o objeto atual da lista, podendo garbage coletar os anteriores (uma `StatelessSession`, conforme discutido nos [7 hábitos dos desenvolvedores Hibernate altamente eficazes](https://blog.caelum.com.br/2008/01/28/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/)).

Esse ainda não era o grande problema, pois o nosso sintoma travava o servidordeixando o processador a 0%, e não a 100%.

**Memory leak temporário encontrado:** Voltando ao problema de leak anterior: o profiler mostrou que alguns objetos que estavam sobrando e não sendo garbage coletados. Com um pouco mais de procura, um heap dump mostra todas as referencias de um determinado objeto e, procurando quem referenciava aqueles que não iam para o garbage collector, encontramos que a **implementação padrão do grupo Apache para a JSTL na tag `c:forEach` mantinha uma referencia para o último objeto iterado**. Oops. Como assim?   \[caption id="" align="aligncenter" width="484" caption="c:forEach leak"\]![c:forEach leak](http://farm4.static.flickr.com/3252/2818769367_16595289c4_o.png "c:forEach leak")\[/caption\]

A tag `c:forEach`, para seguir a especificação da JSTL, [mantem uma referência para o último objeto iterado, mesmo após a iteração](https://issues.apache.org/bugzilla/show_bug.cgi?id=33934) (as tags ficam em um pool do servidor de aplicação e são recicladas). Por isso, a referência era fixa e todo aquele pedaco da árvore de referÊncia dos objetos não poderia ser coletado até o proximo `c:forEach` usar aquela instância do pool novamente.

E agora o que isso pode causar?

Primeiro, as instâncias da tag só vão manter a referência por um tempo determinado. Quando utilizadas novamente, aquela árvore de objetos poderá ser coletada... o único problema aconteceria com um número muito grande de usuários executando uma query no servidor ao mesmo tempo. Isso não costuma acontecer em projetos de 1000 usuários, portanto não é uma preocupação no momento.

Novamente não resolvemos o problema principal, mas achamos mais um detalhe importante sobre uma biblioteca que utilizamos no dia a dia sem medo. **Solução**: não se preocupar pois o projeto não terá um número tão grande de usuários simultaneos para causar algum problema. **Continuar usando a JSTL**.

Por fim, as [estatísticas](http://www.hibernate.org/hib_docs/v3/api/org/hibernate/stat/Statistics.html) do Hibernate Session Factory mostravam que raramente uma conexão se perdia. Após mais de 5000 conexões estabelecidas, três estavam abertas. Isto é, a conexão atual, e mais duas... As estatísticas do hibernate é uma das maneira mais simples de obter resultados sobre leaks de conexões, queries mal escritas ou possíveis pontos de bom uso do cache de segundo nível. Oolhando as estatísticas, confirmamos o session leak (connection leak):

\[caption id="" align="aligncenter" width="548" caption="session leak"\][![c:forEach leak enquanto não apontar para outro objeto](http://farm4.static.flickr.com/3242/2819594498_305471f6e7_o.png "c:forEach leak enquanto não apontar para outro objeto")](https://blog.caelum.com.br/wp-admin/session leak)\[/caption\]

Session leak é um dos erros mais comuns em projetos com o Hibernate. Infelizmente encontramos muitos projetos que acessam conexões em métodos estáticos, sem controle algum de quem invoca esse método e de quem libera essa sessão. Nesse projeto, o acesso a camada ORM era feita através de um Interceptador, como no [Open Session in View](http://www.hibernate.org/43.html), que é a maneira que mais gostamos de trabalhar, pois dificulta session leaks e facilita a criação de testes para seu código, já que será usado alguma maneira de inversão de controle.

**Causa encontrada**: Usando o _Open Call Hieranchy_ do Eclipse, encontramos rapidamente 3 referências para abertura de sessões que não fecham ela de maneira adequada (com try/finally mal escritos)...

**Solução:** Abrir e fechar sessões corretamente!

Mas o que um connection leak pode causar no meu projeto? Tudo depende da configuração do seu connection pool. No caso do [c3p0](http://www.hibernate.org/214.html), o mais famoso e amplamente utilizado, existem algumas configurações que você pode fazer como, por exemplo, o número minimo de conexões no pool, `c3p0.minPoolSize`.

Porem, ao usar hibernate essa propriedade muda para hibernate.c3p0.min\_size. Note que o hibernate decidiu não seguir o [padrão](http://www.mchange.com/projects/c3p0/index.html#hibernate-specific "o padrão")! Em uma conversa com o [Diego Plentz](http://plentz.org), commiter do Hibernate e colaborador da Caelum, não encontramos rapidamente um motivo pelo qual o padrão não foi seguido. Talvez algum problema de conflito de propriedades configuradas em um mesmo arquivo, ou talvez para manter compatibilidade com o Hibernate 2.

O que acontecia nesse caso específico? A aplicação usava algumas configurações corretamente, como o minimo e máximo de conexões (nos [testes de integração e de aceite](https://blog.caelum.com.br/2007/02/28/testes-de-aceitacao-com-o-selenium/), o máximo era 3). Mas uma outra configuração importantíssima estava escrita de maneira errada dentro de uma String do XML: configurar o tempo máximo que uma conexão que não está sendo usada seja eliminada, justamente para ignorar leaks. Algo como: **se a conexão está idle, por, digamos, 1 minuto, considere que foi um leak**, e traga ela de volta para o pool.

**Causa encontrada:** Como exatamente essa configuração estava sendo usada no valor errado, junto com o memory leak da implementação da JSTL, após a funcionalidade com session leak acontecer 3 vezes nos testes de integração, não existia mais nenhuma conexão disponível. O mesmo acontecia em produção com um número maior de vezes. Então as threads ficavam esperando uma conexao do connection pool.

Isso podia ser visto na **visualização de threads do profiler**: a cada nova requisição com o servidor sem responder, uma nova thread era criada pelo tomcat para atender a requisição, mas essa thread ficava em **WAITING**, isto é, ela estva esperando uma conexão ser liberada. Pela **view de monitor** era possível verificar que ela aguardava algo do c3p0... a conexão... pronto! 

\[caption id="" align="aligncenter" width="478" caption="Threads aguardando monitores/sleeping etc"\]![Threads aguardando monitores/sleeping etc](http://farm4.static.flickr.com/3244/2819591946_e1ece368cc_o.png "Threads aguardando monitores/sleeping")\[/caption\]

Encontramos o problema... as threads ficavam **paradas (0% de processador)** esperando por conexões que nunca chegariam (_WAITING_)! Um problema que veio por causa da configuração do c3p0 com o problema do connection leak. A **solução** é composta por duas tarefas: corrigir o session/connection leak e corrigir a configuração do c3p0. Encontramos facilmente o erro graças aos testes de integração.

Ufa! Prontos para o próximo desafio...
