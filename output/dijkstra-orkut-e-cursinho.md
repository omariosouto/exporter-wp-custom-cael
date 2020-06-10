---
title: "Dijkstra, Orkut e Cursinho"
date: "2006-08-21"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O [cursinho da poli](http://www.cursinhodapoli.org.br/) costuma ministrar palestras sobre as diversas carreiras para os seus alunos, e este último domingo foi a 7a [Jornada de Trajetórias Profissionais](http://www.cursinhodapoli.org.br/htmls/JTP/2006.asp).

O [professor Carlinhos](http://www.ime.usp.br/~cef/) do [deparatamento de Ciência da Computação](http://www.ime.usp.br/dcc/) da USP não poderia comparecer, então ele me obrigou indicou a participar em seu lugar. Lá fui eu, tentar recrutar nossos futuros colegas de pesquisa e de trabalho. Tentei bolar algo que fosse interessante aos jovens em idade de cursinho.

Comecei a apresentação com uma citação que eu acho perfeita para quebrar a ilusão de que nosso ramo é dominar o computador (ilusão a qual nossos parentes e amigos insistem em mater pedindo nossa ajuda na instalação de uma webcam ou scanner):

> Ciência da Computação tem tanto a ver com o computador como a Astronomia com o telescópio, a Biologia com o microscópio, ou a Química com os tubos de ensaio. A Ciência não estuda ferramentas, mas o que fazemos e o que descobrimos com elas.

É de [Edsger Dijkstra](http://pt.wikipedia.org/wiki/Edsger_Dijkstra). E foi através de seu famoso algoritmo para resolver o problema do [caminho mínimo](http://pt.wikipedia.org/wiki/Problema_do_caminho_mais_curto) que eu comecei a apresentar o que estudamos no curso. Introduzi o problema de encontrar a menor rota entre duas cidades dado o mapa do Brasil e a distância entre cada uma delas. Tentei fazer com que eles percebessem que uma solução qualquer, testando todos os caminhos, chegaria a `n!` combinações, um número astronômico (e.g., 15 fatorial = 1307674368000). Também mostrei que a solução gulosa, sempre procurando pelo vizinho mais próximo, da solução errada. Por último apenas disse que poderíamos fazer algo muuuuito rápido e rodei [uma simulação](http://www.kt.agh.edu.pl/~pacyna/lectures/nowoczesne_sieci_IP/routing/dijkstra/), sem dizer nada sobre _nlogn_.

Mas a platéia reagiu mesmo quando fiz a comparação com o Orkut: _"sabe quando você entra no perfil de uma pessoa do Orkut, e o site te mostra o caminho entre você e a outra pessoa? Então, o [Orkut Büyükkokten](http://pt.wikipedia.org/wiki/Orkut_B%C3%BCy%C3%BCkkokten), criador do site, estudou computação e aplicou esses conhecimentos para poder fornecer esse tipo de informação"_, todos balançaram a cabeça, de maneira afirmativa. Ok, confesso que menti um pouco. Na verdade o Orkut não precisa de caminho mínimo pois as arestas não tem peso, basta uma busca em largura. Além disso o Orkut não vai disparar uma busca em largura em um grafo com 10 milhões de nós a cada vez que você acessar um perfil diferente, ele na verdade mantém (penso eu) uma estrutura de dados para realizar operações em grafos dinâmicos.

Com isso espero ter angariado um ou outro novo cientista da computação. E quem sabe mais programadores Java!
