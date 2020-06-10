---
title: "Modelagem OO > Linguagem OO"
date: "2019-01-31"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Recentemente ouvi o [podcast sobre OO e SOLID no Hipsters.tech](https://hipsters.tech/praticas-de-orientacao-a-objetos-hipsters-129/), e decidi contribuir com a discussão.

Por muito tempo achei que o grande segredo para se desenhar um bom sistema orientado à objetos era conhecer, e muito bem, cada um dos princípios SOLID, encapsulamento, coesão, acoplamento, DDD, padrões de projeto.

Tanto achava isso que até escrevi um [livro inteiro sobre OO e SOLID pela Casa do Código](https://www.casadocodigo.com.br/products/livro-oo-solid) em 2012.

> SOLID, padrões de projeto, Tell Don't Ask, e similares, ajuda, as pessoas a **implementarem sistemas OO**, mas não a **modelarem sistemas OO**.

Modelagem de software é fundamental, independente de quais sejam as suas escolhas (por exemplo, tecnologia, framework, ou paradigma de linguagem). Modelar significa entender um problema do mundo real, que geralmente é muito complexo, e transformá-lo em um conjunto de definições/abstrações, mais simples, que nos permitam então conversar e manipular o problema.

Imagine o processo de compra numa loja grande, por exemplo, Amazon. É muito complexo. Para conseguirmos entender, programar, e depois manipular, precisamos pensar em abstrações. Veja que estou falando de abstração como um conceito genérico; não como "uma interface que depois possa ser estendida por classes, e que pra esse caso, o padrão de projeto X cai bem". **Abstração como uma ferramenta de comunicação**.

E a boa abstração, de novo, não é aquela só com baixo acoplamento e alta coesão e <coloque sua boa prática aqui>, mas sim aquela que nos ajuda a ser **preciso** sobre o que estamos falando. [Dijkstra](https://en.wikipedia.org/wiki/Edsger_W._Dijkstra) que inspirou minha linha de pensamento:

> _"O propósito da abstração não é sobre ser vago, mas sobre criar um novo nível de semântica na qual podemos ser absolutamente precisos." --_ Edsger Dijkstra1

Modelagem é e sempre será algo de alto nível. E é por isso que os trabalhos (mais importantes) da área (por exemplo, [David Parnas](https://en.wikipedia.org/wiki/David_Parnas), [Grady Booch](https://en.wikipedia.org/wiki/Grady_Booch), [Bertrand Meyer](https://en.wikipedia.org/wiki/Bertrand_Meyer), [Eric Evans](https://domainlanguage.com/)) são praticamente ignorantes em relação à linguagens de programação. Afinal, você não precisa delas para modelar.

Mas, claro, em algum momento, você, desenvolvedor, vai implementar o que modelou. A vantagem de então usar-se uma linguagem OO é que ela oferece (alguns poucos) mecanismos para expressar seu modelo: dados e comportamentos perto um do outro, acesso/não acesso à dados, e extensibilidade.

Nesse momento, toda a discussão sobre SOLID, padrões de projeto, e etc, passam a fazer sentido, já que elas são excelentes guias de como fazer isso usando as linguagens OO que temos atualmente.

Mas essa é a parte fácil do trabalho, se você me perguntar. Fácil no sentido de que sua utilização é praticamente mecânica, dado um modelo. A parte difícil é modelar.

Minha mensagem final é que deveríamos falar mais sobre modelagem e menos sobre linguagens OO e padrões!

1 Você pode ler a transcrição da ACM Turing Lecture de 1972 onde Dijkstra menciona a frase que separei no texto: [https://www.cs.utexas.edu/~EWD/transcriptions/EWD03xx/EWD340.html](https://www.cs.utexas.edu/~EWD/transcriptions/EWD03xx/EWD340.html). Em inglês: _"The purpose of abstraction is not to be vague, but to create a new semantic level in which one can be absolutely precise."_
