---
title: "Agile 2011, dívida técnica e o Hard Choices"
date: "2011-10-04"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O [evento Agile 2011](http://agile2011.agilealliance.org/) aconteceu em Salt Lake City e contou com um misto [tracks da indústria e da academia](http://program2011.agilealliance.org/). Junto com o [Maurício Aniche](http://www.caelum.com.br/instrutores/mauricio-aniche/) foi possível aprender um pouco mais da visão de cada lado, além de [presenciar](http://program2011.agilealliance.org/event/01fed7d376110912e4b6577ded880946) [exemplos](http://program2011.agilealliance.org/event/ecc469bda49c3308e688e7c5cf5d147e) dessa rica experiência de prática e teoria.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/09/hard-choices-agile.png "hard-choices-agile")](https://blog.caelum.com.br/wp-content/uploads/2011/09/hard-choices-agile.png)

A Caelum [apresentou o resultado de uma pesquisa interna](http://program2011.agilealliance.org/event/9a086497e4cd2632ba5381f77a9c71c0) com seus instrutores de como funciona o aprendizado, qual seu papel e como ele acontece dentro da empresa.

[Um workshop acadêmico muito interessante](http://program2011.agilealliance.org/event/b3b404bd9080a6487c0b856294578d04) discutia e apresentava de forma didática a questão das decisões técnicas adequadas: qual é a importância da qualidade do nosso código? O grupo de arquitetura da Carnegie Mellon criou um simples jogo de tabuleiro, o [Hard Choices](http://www.sei.cmu.edu/architecture/tools/hardchoices/). É uma forma fácil de você apresentar ao seu chefe o custo/benefício de utilizar gambiarras, evitar testes e o cowboy coding.

Três ou quatro jogadores começam na posição Start de um tabuleiro. A cada rodada, um jogador rola um único dado e pode anda o número de casas de acordo. Ao tirar um 5, por exemplo, ele pode andar cinco casas para frente, ou quatro casas para frente e uma para trás, ou cinco casas para trás: isto é, ele pode misturar passos para frente e para trás. Ao terminar sua rodada, o jogador que parar em uma casa com ferramenta, ganha uma carta (que vale 1 ponto).

Por outro lado, o primeiro jogador que chegar ao final ganha 7 pontos, o segundo 3 pontos e o terceiro 1 ponto. Como pode ser interessante acabar em primeiro, existem alguns atalhos (pontes). Ao passar pela ponte, o jogador corta caminho (para frente ou para trás) e recebe uma carta de ponte. Enquanto o jogador possui essa carta, ele anda somente o número resultante do dado menos o número de cartas de ponte que possui. Por exemplo, se ele possui uma carta de ponte e tira 3 no dado, ele anda somente 2 casas.

Quando um jogador terminar o tabuleiro, todos os outros só podem andar para frente (usando, ou não, pontes). Para remover o peso da decisão de cortar caminho, em qualquer rodada o jogador pode ficar parado, trocando sua movimentação por jogar uma carta de ponte fora, voltando então a andar mais rápido (uma analogia a gastar tempo para refatoração e melhoria do código).

O jogo é bem simples e cada jogador usa táticas diferentes para acumular o máximo de pontos. Ele reflete já na primeira rodada a grande dúvida de todo processo de desenvolvimento: **corto o caminho e chego antes, trocando isso por andar mais devagar daqui pra frente?** Isto é, como desenvolvedor, faço algo negativo e assumo o prejuízo até o momento que julgar adequado? E quando será esse momento?

Matematicamente, o jogo é um modelo simplificado do que enfrentamos no dia a dia: cortar caminho afeta somente o jogador, enquanto no mundo real afeta todos os desenvolvedores. Também é possível quantificar o prejuízo no jogo, mas na vida real é difícil saber o quanto vamos perder em cada ação. Ao mesmo tempo, assumir o caminho do perfeccionismo pode levar muito tempo e não maximizar o resultado: a entrega de uma funcionalidade. Aqui sentimos o peso da dívida técnica. Em geralessa dívida consiste no tradeoff de entregar uma parte de uma implementação com menos qualidade do que a adequada, para depois melhorá-la. Os perigos estão, claro, quando não se paga a dívida e deixa a mesma acumular os juros, a ponto de se tornar muito caro qualquer mudança futura.

No [QConSP](http://qconsp.com) três palestras abordaram a dívida técnica (ou débito, uma outra possível tradução) de projetos: "[Refatoração em larga escala](http://www.slideshare.net/dtsato/refatorao-em-larga-escala)", "[Design de código: a qualidade que faz a diferença](http://www.slideshare.net/caelumdev/design-de-cdigo-qualidade-que-faz-a-diferena-qcon-2011)" e a "[Dívida técnica: precisando de crédito?](http://www.slideshare.net/alexandrefreire/dvida-tecnica-precisando-de-crdito-quo-fundo-entrar-e-como-evitar-que-o-cobrador-bata-na-sua-porta)" (alguns autores traduzem como dívida técnica).

E você, qual tática você usaria no jogo? E no dia a dia como desenvolvedor? Quando abre mão da qualidade conscientemente e quando luta para recuperá-la?
