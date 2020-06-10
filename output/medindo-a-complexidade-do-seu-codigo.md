---
title: "Medindo a complexidade do seu código"
date: "2012-03-01"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Sabemos que um dos grandes vilões na hora de mantermos um sistema legado são os trechos de código difíceis ou complicados de entender. Todo programador já se deparou com (ou mesmo escreveu!) códigos com péssimos nomes de variáveis ou muitas linhas de código em um único método, sem contar o excesso de responsabilidade. Mas, sem dúvida, um dos maiores responsáveis pela dificuldade em entender o que um código faz é o simples `if`.

Com essa simples instrução, o desenvolvedor faz com que o mesmo método possa responder de duas, três, quatro, N, …, maneiras diferentes, de acordo com certas condições! E pior, o número de caminhos diferentes pode crescer muito rápido!. Considere o trecho de código:

\[java\] public int contaMaluca(int numero) {  int resultado = 10;  if (numero > 10) resultado += numero;  return resultado; } \[/java\]

Quantos caminhos diferentes esse método possui? Dois! O primeiro acontece se o _if_ falhar; o segundo acontece se o _if_ for verdadeiro. Agora, olhe o código abaixo, que contém apenas um _if_ a mais:

\[java\] public int contaMaluca(int numero) {  int resultado = 10;  if (numero > 10) resultado += numero;  if (numero < 30) resultado \*= 2;  return resultado; } \[/java\]

Repare que temos agora **quatro** caminhos diferentes! Veja que adicionamos apenas um `if` a mais, mas a quantidade de caminhos em relação ao código anterior, simplesmente dobrou! Se adicionarmos um `if` a mais no código acima, parecido com os dois já existentes, veja que o número de caminhos pula pra **oito**! A figura abaixo exemplifica os possíveis caminhos:

## [![](https://blog.caelum.com.br/wp-content/uploads/2012/02/ComplexidadeCiclomatica1.png "Complexidade Ciclomática")](https://blog.caelum.com.br/wp-content/uploads/2012/02/ComplexidadeCiclomatica1.png)

Devemos, quase sempre, manter esse número de caminhos **o menor possível**. Afinal, quanto menor o número de caminhos, mais fácil é entender o código. Mas o `if` não é o único vilão! Outras instruções podem aumentar esse "número de complexidade". Sempre que vemos um _for, while, case_ (do switch), _catch, &&, ||_ ou _?_, podemos incrementar esse número.

Essa conta é bastante conhecida, é a **Complexidade Ciclomática**. A ideia é basicamente contar o número de caminhos diferentes que um método pode ter. Entretanto, como calcular o número de caminhos não é tão fácil, um algoritmo muito conhecido é a **[Complexidade de McCabe](http://www.linuxjournal.com/article/8035)**, que basicamente conta o número de vezes que um `for`, `while`, e todas as instruções mencionadas acima aparecem. Além disso, todo método tem seu contador já inicializado com 1 (afinal, todo método tem pelo menos um caminho). No nosso segundo código, por exemplo, a complexidade de McCabe seria 3.

Não há um número ideal para a complexidade de um método. Mas fato é que uma complexidade ciclomática de 20 ou 30 é demais e esse método deve ser reescrito. Mesmo quebrando-o em dois, é interessante observar a complexidade dos métodos de uma classe em conjunto. Existem diversas ferramentas que calculam esse número para você. Uma das mais populares é o Eclipse Metrics, que é um plugin do Eclipse. Você pode baixá-lo aqui: [http://metrics.sourceforge.net/](http://metrics.sourceforge.net). Ele calcula a complexidade ciclomática de todos os métodos de um projeto!

## [![](https://blog.caelum.com.br/wp-content/uploads/2012/02/metrics-300x163.gif "metrics")](https://blog.caelum.com.br/wp-content/uploads/2012/02/metrics.gif)

Faça o teste: instale o plugin e procure pelos métodos cuja complexidade ciclomática seja alta! Compartilhe conosco o maior número que encontrou! E, obviamente, refatore seu código para diminuir esse número.
