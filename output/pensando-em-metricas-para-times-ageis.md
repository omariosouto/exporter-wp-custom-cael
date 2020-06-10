---
title: "Pensando em métricas para times ágeis"
date: "2011-01-26"
author: "ceci"
authorEmail: "cecilia.fernandes@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Juntamente à decisão de usar Scrum para gerenciar projetos de forma ágil, vem, segundo o seus criadores, a obrigação de traçar o gráfico de BurnDown, apenas uma das [inúmeras métricas](http://www.dtsato.com/blog/2007/06/23/metricas-vs-metricas-e-uma-propaganda-no-final/ "métricas") que podem ajudar (ou atrapalhar!) a acompanhar características do projeto e da equipe.

Escolher bem quais métricas usar em cada momento de um projeto é até mais importante do que fazer uma boa escolha de metodologia: é com base nas métricas que evoluimos e aperfeiçoamos nossa forma de trabalhar, escolhendo a inadequada corre-se o risco de parar no processo de melhoria contínua. Métrica específicas podem ajudar em diversas situações distintas, como algumas listadas a seguir.

### Problema: nunca sabemos se atrasaremos ou adiantaremos

Em uma ocasião, um time termina tudo o que se comprometeu a fazer antes do tempo planejado, mas, como só descobriu que isso aconteceria próximo a entrega, fica parado até que o cliente (ou P.O.) possa definir um novo item e explicá-lo. Em outra iteração, o mesmo time se compromete com 5 histórias e acaba, no último dia, descobrindo 3 delas não ficarão prontas.

### Discussão de métrica:

Embora um gráfico de [BurnDown](http://en.wikipedia.org/wiki/Scrum_%28development%29#Burn_down "BurnDown") (ou BurnUp) ajude nesses problemas, ele também pode ser o próprio causador desses cenários. Seguindo cegamente o que é dito no Scrum Guide, uma equipe que usa Scrum teria um BurnDown de tarefas de uma Sprint.

Tal gráfico nos indica razoavelmente bem se terminaremos as histórias mais cedo do que o planejado, mas frequentemente dá a falsa impressão de que tudo está correndo bem, mesmo que ainda falte terminar todas as histórias.

Veja o seguinte BurnDown no penúltimo dia de um Sprint:

[![burndown perigoso](https://blog.caelum.com.br/wp-content/uploads/2011/01/burndown-perigoso-300x185.png "Burndown perigoso")](https://blog.caelum.com.br/wp-content/uploads/2011/01/burndown-perigoso.png)

No último dia, sobram três pontos a fazer, um de cada história, e assim três delas ficam pra trás - o cliente não as recebe. Embora seja uma boa métrica para diversos casos, o BurnDown não nos ajudou nesse momento. O que poderia ajudar mais?

### Solução: quadro branco

Uma ferramenta que traz de brinde várias métricas bastante úteis e interessantes é o [quadro branco](http://www.woompa.blog.br/2010/10/15/quadro-branco/ "quadro branco") - quando, claro, bem utilizado. Vejamos como um quadro bastante simples, que apenas contenha um quadro de acompanhamento, já nos ajudaria no problema acima:

[![quadro branco do mesmo sprint](https://blog.caelum.com.br/wp-content/uploads/2011/01/whiteboard-wont-finish-300x210.png "quadro branco equivalente")](https://blog.caelum.com.br/wp-content/uploads/2011/01/whiteboard-wont-finish.png)

Esse quadro também mostra a mesma informação que o burndown nos dá, mas além disso nos aponta algumas outras informações importantes.

A primeira delas é que, apesar de faltar pouco para terminarmos, **há três histórias pendentes**. Isto é, embora tudo esteja sendo feito, as tarefas em andamento comprometem três das cinco histórias planejadas - é um impacto muito grande na iteração, se não conseguirmos terminá-las.

A segunda, e talvez mais importante, é que a história mais prioritária da iteração não foi terminada, enquanto a última ficou pronta até antes do último dia. Temos um **problema de prioridade** que passaria em branco se confiássemos apenas no gráfico de BurnDown.

### Problema: "isso aí é com tal desenvolvedor"

Outro problema que assombra diversos times de desenvolvimento é código confuso, frequentemente legado, que fica na responsabilidade exclusiva de um programador que já está lá há tempos e conhece melhor aquela bagunça.

E essa situação não está limitada a código legado. Também vemos vários cenários em que uma parte que requer um conhecimento específico de negócio acaba caindo na mão de apenas um ou dois desenvolvedores de um time.

Para reduzir o problema de concentração de conhecimento, uma das técnicas que pode ajudar é o pareamento - isto é, dois desenvolvedores temporariamente trabalhando sobre o mesmo problema em uma mesma máquina, discutindo as possibilidades enquanto constroem a solução.

Mas, mesmo em times que utilizam [pareamento](http://www.improveit.com.br/xp/praticas/programacao_par "pareamento") como prática comum, [tal acúmulo ainda pode acontecer](https://blog.caelum.com.br/pratica-agil-facilite-a-comunicacao-interna/). Se for esse o caso, o conhecimento fica concentrado em duas pessoas, isto é, o truck factor aumenta de um para dois: não é grande vantagem.

Medir a difusão de conhecimento é algo verdadeiramente complexo, mas um dos sinais mais facilmente observáveis é a quantidade de vezes que uma mesma dupla pareou.

### Solução: Quadro de pareamento

Para fazer esse acompanhamento, é comum a utilização de um quadro de pareamentos como o seguinte:

[![quadro de pareamentos](https://blog.caelum.com.br/wp-content/uploads/2011/01/pareamentos-300x196.png "quadro de pareamentos")](https://blog.caelum.com.br/wp-content/uploads/2011/01/pareamentos.png)

Essa imagem nos indica um ambiente em que o pareamento está concentrado em dois pares de desenvolvedores: um, Luiz e Atoji, e o outro, Caires e Victor. Provavelmente haverá ilhas de conhecimento limitadas a um par de pessoas - apesar de ser uma situação melhor que conhecimento isolado, é possível mudá-la.

Como o time está atento para a métrica, o problema é rapidamente identificável e solucionável: apenas uma questão de disciplina na troca mais frequente de pares. Aliado a outras técnicas, como code review e apresentações de discussões internas, as ilhas de conhecimentos podem ser minimizadas.

### Problema: incêndios constantes e o bombeiro é você

Outra frequente reclamação em times que começam a usar Scrum é que eles conseguiriam terminar os itens com que se comprometeram no planejamento, mas que [constantes interrupções](http://www.infoq.com/news/2008/07/interruption-driven-development "constantes interrupções") os fazem perder tempo apagando incêndios.

Até mais importante do que a própria interrupção é notar que cada uma delas faz o desenvolvedor perder o foco no que estava fazendo e, portanto, tomará mais algum tempo para recuperar a linha de raciocínio e voltar a produzir.

### Solução: gráfico de interrupções

Para que tanto a equipe quanto as pessoas que os interrompem notem tal prejuizo, um simples contador de interrupções pode ajudar. A idéia é bastante simples: para cada interrupção durante o Sprint, o contador é incrementado. Então, ao final do Sprint, a contagem vai para um gráfico simples que mede as interrupções através dos Sprints desse projeto.

O tracking das interrupções, quando comparados ao desempenho do time em cada Sprint, pode levantar dados importantes. Contudo, a simples marcação do número tende a fazer as pessoas evitarem interromper o time. Frequentemente, pessoas externas ao time não se dão conta da frequência das interrupções.

### O uso do quadro, afinal

Nesse contexto, para um time com todos esses problemas, o quadro acabaria, nesse momento, assim:

[![quadro completo](https://blog.caelum.com.br/wp-content/uploads/2011/01/quadro-final-300x159.png "quadro completo")](https://blog.caelum.com.br/wp-content/uploads/2011/01/quadro-final.png)

E, claro, é preciso manter em mente [a razão pela qual cada uma dessas métricas está aí](http://agilenomundoreal.com.br/2010/06/24/restricoes-sao-boas-mas-restricoes-sao-ruins/ "restrições"). Se uma delas deixar de fazer sentido, o time deve removê-la; se outro problema recorrente aparecer, o time deve buscar uma boa métrica para deixá-lo visível e, eventualmente, resolvê-lo. O mundo do desenvolvimento não deve se reduzir a expressar qualidade através de métricas pois muitas daquelas que seriam necessárias envolvem o tato humano de como lidar com uma situação, algo que uma função matemática simples ainda não é capaz de mensurar.

Esses foram apenas exemplos do que pode acontecer a um time. Outros casos envolveriam outras métricas. E seu time? Quais métricas trazem ou trouxeram bons resultados para vocês? Quais acabaram atrapalhando e enviesando o desenvolvimento?
