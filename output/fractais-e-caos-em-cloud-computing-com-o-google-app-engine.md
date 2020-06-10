---
title: "Fractais e Caos em cloud computing com o Google App Engine"
date: "2009-09-03"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Estudando métodos matemáticos no curso de matemática aplicada, fiz minha iniciação científica sob supervisão do [professor Eduardo Colli](http://www.ime.usp.br/~colli/), em um trabalho de conclusão de curso sobre sistema dinâmicos discretos, [que implementei em Java](http://pulga.sourceforge.net/). É na área de [sistemas dinâmicos](http://en.wikipedia.org/wiki/Dynamical_system) - em geral não lineares - que começaram a falar sobre o comportamento caótico de determinadas partículas (uma área próxima a teoria do caos). Um dos sistemas dinâmicos mais estudados é o [atrator de Lorenz](http://en.wikipedia.org/wiki/Lorenz_attractor).

[![](https://blog.caelum.com.br/wp-content/uploads/2009/09/rhnov06g02-286x300.png "rhnov06g02")](https://blog.caelum.com.br/wp-content/uploads/2009/09/rhnov06g02.png)

Aproveitando que esses algoritmos são paralelizaveis, resolvemos colocar o [Google App Engine](http://appengine.google.com) em uma prova de fogo, e tirar proveito da plataforma em nuvem para gerar os gráficos mais rapidamente, como num cluster: não só usar o Google App Engine para escalabilidade e disponibilidade, mas sim também para performance. O principal procedimento do algoritmo consiste em iterar uma mesma função na ordem de **bilhões** de vezes. Isto é, na versão tradicional em uma única thread, existe um ou mais loops parecidos com esse pseudo código:

```

for(int tempo = 0; tempo < 60*60; tempo++) {
  for(int inicial=0;inicial < 1000; inicial++) {
    x0, x1 = condicaoInicial[inicial];
    for(int iteracao = 0; iteracao <= 100000; iteracao++) {
      x0, x1 = recalcula(x0, x1);
    }
  }
}
```

O código acima está bem mais simples do que [o código real](http://pulga.cvs.sourceforge.net/viewvc/pulga/pulga/), mas ilustra o quão lento seria a execução do mesmo. Um exemplo comum de execução deste software envolve encontrar a [bacia de atração](http://en.wikipedia.org/wiki/Basin_of_attraction) de um atrator, tarefa que demorava entre 2 a 5 minutos para o conhecido [mapa de Henon](http://en.wikipedia.org/wiki/H%C3%A9non_map).

Em nossas máquinas, um diagrama de bifurcação de Henon, como mostrado a seguir, demorava cerca de 5 minutos para ser computado:

![diagrama de bifurcação do mapa de henon](http://upload.wikimedia.org/wikipedia/commons/thumb/e/e7/HenonMap_BifurcationDiagram.png/600px-HenonMap_BifurcationDiagram.png)

Cada um desses desenhos pode parecer rodar em um período razoavelmente curto de tempo, mas uma vez que o mapa de Henon depende de 2 parâmetros, se eu desejar varrer um desses parâmetros (considerando ele o tempo) e criar um vídeo do mesmo com duração de 1 minuto e 24 frames por segundo, o tempo médio seria de 3 horas. Em situações reais, com outros atratores, chegamos a criar filmes que **demoraram mais de 7 horas** para serem criados.

Para tornar esse software paralelizável, a nova aplicação possui três partes: um **cliente** que vai iniciar o pedido, um **servidor** que recebe esse pedido e dispara diversas requisições para o **cloud**, onde cada máquina vai gerar um pouco do gráfico.

O cliente pede para o servidor as cores que deve usar para pintar uma imagem via [ajax/jsonp](http://en.wikipedia.org/wiki/JSON#JSONP). A aplicação do servidor foi implementada em rails, esta dispara diversas requisições em paralelo para os servidores do Google App Engine utilizando uma API de IO não blocante, similar ao [NIO](http://java.sun.com/j2se/1.4.2/docs/guide/nio/index.html) no Java. Já os nós do cloud rodam Java no Google App Engine, basicamente com o código do software original. Após a execução cada nó devolve os números para o servidor ruby, que vai agregando os resultados conforme eles vão sendo processados, para gerar a imagem final.

Os testes iniciais mostraram redução de até 51 vezes no tempo de execução por executar o programa em 100 requisições paralelas ao Google App Engine:

### 100 iterações por requisição

Requisições

Tempo total (segundos)

**Tempo por iteração (milisegundo)**

local

6

16.6

1

3

33

10

6.4

156.25

50

14

357

100

17

588

**200**

**23.55**

**849**

Usando o [memcached](http://www.danga.com/memcached/), que é [o cache fornecido pelas APIs do Google App Engine](http://code.google.com/appengine/docs/java/memcache/overview.html), temos um ganho ainda maior:

```

CacheFactory cacheFactory = 
       CacheManager.getInstance().getCacheFactory();
cache = cacheFactory.createCache(Collections.emptyMap());
String result = (String) cache.get(script);
if (result != null) { ... }
```

Após essas modificações, o nosso ganho é de um desempenho **93 vezes superior** por rodar a aplicação em ~100 máquinas!

Podemos ainda melhorar esses números se tirarmos ainda mais proveito da infraestrutura que o HTTP e a rede nos fornece: proxies. Configurando adequadamente o proxy de sua empresa o conteúdo cacheado de diversas requisições não expira até a requisição seguinte ocorrer, obtendo resultados assustadoramente rápidos. Não é a toa que os gurus dos webservices RESTFul [consideram o Squid uma excelente opção no lugar de um gigante ESB](http://www.infoq.com/presentations/soa-without-esb).

![hiperboloide](http://farm4.static.flickr.com/3577/3842438342_09aaa2026b_o.png)

Para quem se interessar, existem diversos outros [exemplos de mapas 2d de sistemas dinâmicos](http://en.wikipedia.org/wiki/List_of_chaotic_maps) de relativa fácil implementação.

Processos em batch e relatórios são exemplos de tarefas que costumam demorar bastante no mundo corporativo, e muitos deles poderiam ser quebrados em tarefas paralelas. **Nós programadores estaremos em breve rodando muitas das nossas aplicações no cloud**: não só porque é uma maneira de ter mais escalabilidade e disponibilidade, mas também porque esse tipo de infraestrutra elástica diminui muito os custos operacionais das grandes empresas de hosting. Saber tirar proveito disso vai ser papel fundamental de todo programador. [Bancos de dados não relacionais](http://blog.oskarsson.nu/2009/06/nosql-debrief.html) e [linguagens que facilitam o trabalho da programação concorrente](https://blog.caelum.com.br/scala-sua-proxima-linguagem/) tornam-se excelentes nesse cenário.
