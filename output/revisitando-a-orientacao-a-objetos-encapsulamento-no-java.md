---
title: "Revisitando a Orientação a Objetos: encapsulamento no Java"
date: "2012-06-14"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Façamos uma aposta. Tenho certeza que você, ao ver a classe abaixo, consegue perceber um problema nela:

\[java\] class Pedido { public String comprador; public double valorTotal; // outros atributos } \[/java\]

Sim. Os atributos estão todos públicos! Isso vai exatamente contra uma das nossas primeiras lições quando aprendemos Java: atributos devem ser privados e precisamos de getters e setters para acessá-los. Vamos então fazer essa mudança no código.

\[java\] class Pedido { private String comprador; private double valorTotal; // outros atributos

public String getComprador() { return comprador; } public void setComprador(String comprador) { this.comprador = comprador; }

public double getValorTotal() { return valorTotal; } public void setValorTotal(double valorTotal) { this.valorTotal = ValorTotal; }

// outros getters e setters } \[/java\]

Agora está melhor, certo? Ainda não. Deixamos escapar na verdade o grande princípio que está por trás da ideia de colocar atributos como privados. Do jeito que a classe `Pedido` está nesse momento, podemos fazer coisas como:

\[java\] Pedido p = new Pedido(); // muda valor do pedido para 200 reais! p.setValorTotal(p.getValorTotal() + 200.0); \[/java\]

Mas aonde está o problema? Imagine outras 10 classes que fazem a mesma coisa: de alguma forma, elas manipulam o valor total do pedido.

![](https://blog.caelum.com.br/wp-content/uploads/2012/06/encapsulamento1.png "encapsulamento1")

Agora imagine que a regra de negócio do pedido mude: todo item comprado ganha desconto de 5% se o valor dele for superior a 1000 reais. Implementar essa mudança não será tarefa fácil. Precisaríamos fazê-la em diferentes classes do sistema.

![](https://blog.caelum.com.br/wp-content/uploads/2012/06/encapsulamento2.png "encapsulamento2")

Quanto tempo demoraremos para mudar o sistema? Não sabemos exatamente aonde devemos fazer as mudanças já que elas estão espalhadas pelo código. Esse, aliás, é um dos grandes problemas de códigos legados: uma simples mudança precisa ser feita em tantas classes e, na prática, sempre esquecemos algum ponto, e nosso sistema frequentemente quebra.

A classe `Pedido` não foi bem desenhada. Demos acesso direto ao atributo `valorTotal`, um atributo importante da classe. Veja que o modificador `private` nesse caso não adiantou de nada, já que demos também um setter para ele. Vamos tentar diminuir o acesso ao atributo, criando métodos mais claros para a operação de depósito:

\[java\] class Pedido { private String comprador; private double valorTotal; // outros atributos

public String getComprador() { return comprador; } public double getValorTotal() { return valorTotal; }

public void adiciona(Item item) { if(item.getValor() < 1000) this.valorTotal += item.getValor(); else this.valorTotal += item.getValor() \* 0.95; } } \[/java\]

Agora, para adicionarmos um item no `Pedido`, faremos uso desse novo comportamento:

\[java\] Pedido p = new Pedido(); p.adiciona(new Item("Chuveiro Elétrico", 500.0)); \[/java\]

Mas qual a diferença entre os dois códigos abaixo?

\[java\] Item item = new Item("Super Geladeira", 1500.0);

// antiga if (item.getValor() > 1000) { c1.setValorTotal(c1.getValorTotal() + item.getValor() \* 0.95); } else { c1.setValorTotal(c1.getValorTotal() + item.getValor()); }

// nova c1.adiciona(item); \[/java\]

Veja que na primeira linha de código, sabemos exatamente **COMO** funciona a adição de um novo ítem no pedido: devemos pegar o valor total e somar o valor novo com desconto de 5% se ele for maior que 1000. Já na segunda linha de código, não sabemos **como** esse processo funciona.

Quando sabemos **O QUÊ** um método faz (igual ao método `adiciona`, sabemos que ele adiciona um ítem no pedido, por causa do nome dele), mas não sabemos exatamente **como** ele faz, dizemos que esse comportamento está **encapsulado**!

A partir do momento que as outras classes não sabem **como** a classe principal faz o seu trabalho, significa que as mudanças ocorrerão apenas em um lugar! Afinal, elas estão escondidas (encapsuladas)!

![](https://blog.caelum.com.br/wp-content/uploads/2012/06/encapsulamento3.png "encapsulamento3")

Ou seja, para implementar a regra de negócios nova, bastaria mexermos em um único lugar:

\[java\] public void adiciona(Item item) { if (item.getValor() > 1000) this.valorTotal += item.getValor(); else this.valorTotal += item.getValor() \* 0.95;

// nova regra de negócio aqui } \[/java\]

No fim, a real utilidade do `private` é esconder acesso de atributos que precisam ser acessados de maneira mais inteligente. Mas veja que de nada adianta colocar todos os atributos como `private` e criar getters e setters para todos eles. Deixamos o encapsulamento "vazar" do mesmo jeito.

Esconda os atributos, mas pense em comportamentos inteligentes para acessá-los. Uma ótima maneira para saber se o comportamento está encapsulado é olhar para o código que faz uso dele! Se conseguirmos dizer `o que` o método faz, mas sem dizer `como` ele faz, então podemos afirmar que o comportamento está encapsulado!

Muitas vezes deixamos esses princípios passarem. Se quiser revisitar essas e outras boas práticas de Orientação a Objetos junto com os instrutores da Caelum, há mais posts por aqui, como um específico sobre esse [problema dos getters e setters](https://blog.caelum.com.br/nao-aprender-oo-getters-e-setters/), o [excesso de ifs](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-o-excesso-de-ifs/) e o [relacionamento bidirecional entre classes](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-relacionamento-bidirecional/). Quer praticar tudo isso com video aulas, respostas dos instrutores e correção dos seus exercícios? [Confira nosso novo curso online de boas práticas de orientação a objetos](http://www.caelum.com.br/curso/online/orientacao-objetos-java/)!.
