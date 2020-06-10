---
title: "Entendendo o pattern Strategy em PHP"
date: "2015-05-26"
author: "renan.saggio"
authorEmail: "renan.saggio@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Embora o PHP inicialmente não ser uma linguagem orientada a objetos, em sua versão 5 foi adicionado suporte para esse popular paradigma. Desde então a linguagem e suas bibliotecas tem adotado cada vez mais suas soluções, mas com isso, precisamos sempre nos atentar às boas práticas. É aqui que os padrões de projeto nos ajudam. Neste post veremos o problema e solução de uma situação bastante comum do mundo OO.

Vamos imaginar que temos um sistema de e-commerce em que o usuário pode pagar o pedido de várias formas e dependendo da forma recebe um desconto.

 

Forma de pagamento

Desconto

Boleto

10%

Debito Online

7%

PayPal

5%

Cartão de crédito

0%

Para implementar essa funcionalidade, nós vamos mexer na classe Carrinho que contém um atributo $itens que armazena a lista de itens, um método que calcula o total da compra somando o valor de todos os itens e um método que de acordo com a forma de pagamento calcula o total da compra com um possível desconto.

\[code language="php"\]

<?php class Carrinho {

// adiciona item, remove item e outros métodos do carrinho

public function getTotal() {

$total = 0; foreach($this->itens as $item) { $total += $item->getValor(); }

return $total; }

public function getTotalComDesconto() { // precisamos implementar este método }

} ?> \[/code\]

Então podemos começar a escrever o nosso método getTotalDesconto. Para poder calcular o desconto, precisamos saber qual é a forma de pagamento e por isso vamos pedi-la por parâmetro. Já que temos várias possibilidades, vamos verificar todas.

\[code language="php"\]

public function getTotalComDesconto($formaPagamento) { $total = $this->getTotal(); if($formaPagamento == "Boleto") { $total = $total \* 0.9; }else if($formaPagamento == "Debito") { $total = $total \* 0.93; }else if($formaPagamento == "PayPal") { $total = $total \* 0.95; }

return $total;

} \[/code\]

Mas o que acontece se for necessário implementar uma nova forma de pagamento? Bom, teríamos que adicionar mais um if a esse método e isso não é bom! Veja que o nosso método não vai parar de crescer nunca.

A nossa classe Carrinho sabe muito sobre os meios de pagamento, quem deveria saber como calcular o valor com desconto é o meio de pagamento, temos basicamente um código procedural disfarçado dentro de uma classe onde é necessário verificar todas as possibilidades com ifs para tomar uma decisão.

Já que não é responsabilidade da classe carrinho fazer este cálculo, vamos extrair esse comportamento para classes mais específicas, Por exemplo o primeiro if que verifica se a forma de pagamento Boleto no lugar de fazer:

\[code language="php"\] if($formaPagamento == "Boleto") { $total = $total \* 0.9; } \[/code\]

podemos extrair para essa nova classe:

\[code language="php"\]

class Boleto {

public function calcula($total) { return $total \* 0.9; }

}

\[/code\]

seguindo esse raciocínio cada forma de pagamento que estamos verificando nos ifs vai virar uma classe como Boleto,DebitoOnline,Paypal.

\[code language="php"\]

class DebitoOnline { public function calcula($total) { return $total \* 0.93; } }

class PayPal { public function calcula($total) { return $total \* 0.95; } }

class CartaoCredito { public function calcula($total) { return $total; } }

\[/code\]

Agora que temos classes especificas para o pagamento vamos utilizá-las  no nosso método.

\[code language="php"\]

public function getTotalComDesconto($formaPagamento) {

$total = $this->getTotal();

if($formaPagamento == "Boleto") { $boleto = new Boleto(); $total = $boleto->calcula($total); }else if($formaPagamento == "Debito") { $debito = new DebitoOnline(); $total = $debito->calcula($total); }else if($formaPagamento == "PayPal") { $paypal= new PayPal(); $total = $paypal->calcula($total); }

return $total; } \[/code\]

Veja que isso não resolveu o nosso problema dos ifs e esse método ainda cresce toda vez que é preciso adicionar uma forma de pagamento. Todas as formas de pagamento tem algo em comum,  elas possuem um método chamado calcula que recebe o valor bruto e retorna o valor com desconto. Se conseguirmos garantir isso, não importa qual é a forma de pagamento, só precisamos chamar o método calcula.

Em orientação a objetos, como podemos forçar que uma classe implemente um método ? Criando uma interface! Então vamos criar a interface FormaDePagamento que força todo mundo a implementar um método chamado calcula.

\[code language="php"\]

interface FormaDePagamento { public function calcula($total); }

\[/code\]

Agora todas as formas de pagamento devem implementar esta interface:

\[code language="php"\]

/\* agora todas as classes que representam uma forma de pagamento implementam a interface FormaDePagamento \*/

class DebitoOnline implements FormaDePagamento { public function calcula($total) { return $total \* 0.93; } }

\[/code\]

O nosso getTotalComDesconto pede alguém com um método calcula, que recebe o valor bruto por parâmetro e invoca esse método:

\[code language="php"\]

public function getTotalComDesconto(FormaDePagamento $formaPagamento) { $total = $this->getTotal(); $total = $formaPagamento->calcula($total); return $total; } \[/code\]

Veja que agora toda vez que for necessário adicionar uma forma de pagamento basta criar uma classe que implemente a interface FormaDePagamento portanto, o nosso método getTotalComDesconto não cresce conforme criamos uma forma de pagamento pois agora injetamos a estratégia do cálculo. A principal vantagem dessa solução é que a tarefa de adicionar uma forma de pagamento se torna muito mais simples o que facilita a manutenção do código, alem disso temos classes mais coesas e por mais que a classe carrinho esteja acoplada a outras classes veja que o parâmetro pede uma interface ou seja estamos nos acoplando a uma abstração que são naturalmente mais estáveis.

Esta abordagem que utilizamos é um design pattern que chamamos de **Strategy**.

Se interessa por design patterns? Veja este pattern e outros no [nosso curso do alura](https://www.alura.com.br/cursos-online-php#design-pattern-php)!
