---
title: "Como não aprender orientação a objetos: o excesso de ifs"
date: "2011-04-12"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Aglomerados de `if`s aparecem com frequência, e chegam até a ter um aspecto engraçado. Em alguns casos poder dar a impressão de que estamos usando orientação a objetos, já que cada cláusula costuma envolver a invocação de um método, dependendo do tipo do objeto. Infelizmente, essa sensação é falsa, e chegou até a gerar o conhecido movimento [_anti if campaign_](http://www.antiifcampaign.com/) na internet.

![](https://blog.caelum.com.br/wp-content/uploads/2011/02/Image-6.png "anti-if")

O exemplo a seguir mostra uma sequência de ifs que indicam condições distintas de execução:

\[java\] public double calculaBonus(Funcionario f) { if (f.getCargo().equals("gerente")) { return f.getVendasDoMes() \* 0.05 + getSalario() \* 0.1; } else if (f.getCargo().equals("diretor")) { return f.getVendasDoMes() \* 0.05 + getSalario() \* 0.2 + (Today.isDecember() ? getSalario() : 0); } else { return f.getVendasDoMes() \* 0.01; } } \[/java\]

Esse tipo de condicional pode ser retrabalhado em objetos (ou funções dependendo da linguagem) que definam o comportamento a ser executado em cada caso. Herança e poliformismo podem (e na maioriam dos casos, devem) ser usados de maneira controlada para **alterar um comportamento**:

\[java\] class Funcionario { // outros atributos e metodos aqui public double getBonus() { return vendasDoMes \* 0.01; } } class Gerente extends Funcionario { // outros atributos e metodos aqui public double getBonus() { return vendasDoMes \* 0.05 + getSalario() \* 0.1; } } \[/java\]

Nesse caso, os responsáveis pelo comportamento são encontrados de acordo com uma condição: o tipo que o objeto representa, sendo que cada subtipo pode redefinir o comportamento do método. Não haverá necessidade de `if`s, já que essa descoberta de qual método executar é feita pela máquina virtual: `funcionario.getBonus()`.

Esses ifs não costumam aparecer sozinhos. Assim como o comportamento do bonus, surgem em breve outros comportamentos com os mesmos ifs em outras partes do sistema:

\[java\] public double liberaVerba(Funcionario f, Produto produto) { if (f.getCargo().equals("gerente")) { return produto.getValor() < 5000 || produto.getTipo().equals(Tipo.URGENTE); } else if (f.getCargo().equals("diretor")) { return produto.getValor() < 10000; } else { return produto.getValor() < 1000 || produto.getTipo().equals(Tipo.USO\_DIARIO); } } \[/java\]

Esse tipo de código fica então muito instável: uma mudança de comportamento deve ser alterada em diversos lugares distintos e, pior ainda, é muito fácil esquecer um deles ao criar um novo comportamento: alguns dos pontos de multiplos ifs são esquecidos. Ao escrever o mesmo código pela segunda vez, seguimos [a prática sugerida no Pragmatic Programmers, de não nos repetir (DRY)](http://pragprog.com/the-pragmatic-programmer/extracts/tips).

Mas o uso da herança é delicado, e o desenvolvedor deve estar ciente [de que ela pode trazer um acoplamento indesejado](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/) e [suas alternativas](https://blog.caelum.com.br/compondo-seu-comportamento-heranca-chain-of-responsibility-e-interceptors/). O uso de interfaces se encaixaria aqui com perfeição.

Outros tipos de condições podem determinar qual ação deve ser tomada, como por exemplo o valor de um parâmetro, resultando em uma abordagem que utiliza um mapa. Note como, nesse caso, novamente, nenhum `switch` ou sequências de `if`s precisam ser feitos: `if`s são substituídos por polimorfismo:

\[java\] private Map<String, Aplicador> taxas = new HashMap<String, Aplicador>();

public void processa(String taxa, double juros) { impostosRecolhidos += taxas.get(taxa).aplicaComJuros(juros); } \[/java\]

De todas as variações, a solução do registro (em geral um Map) e a chave é a abordagem mais simples e capaz de separar responsabilidades distintas. Uma outra variação, que remove a necessidade de registrar os itens no mapa, mas abusa de reflection, muitos casos desnecessariamente, é a seguinte:

\[java\] interface AplicadorDeTaxa { double aplicaComJuros(double valor); } }

public void processa(String taxa, double juros) { Object instancia = Class.forName("br.com.caelum.taxas." + taxa).newInstance(); AplicadorDeTaxa aplicador = (AplicadorDeTaxa) instancia; impostosRecolhidos += aplicador.aplicaComJuros(juros); } \[/java\]

A invocação a `Class.forName("br.com.caelum.taxas." + taxa).newInstance();` pode ainda ser encapsulada em uma Factory, que em vez de buscar por um nome de classe, consultaria anotações ou um arquivo de configuração.

Esses problemas com o `if` surgem também em outros paradigmas. Em linguagens funcionais é possível atingir o mesmo resultado usando lambdas, ou ainda em procedurais é possível passar ponteiros de funções com abstract data types.

Além dos casos em que `if`s e condicionais podem ser trocados pelo bom uso de polimorfismo, podemos seguir as boas práticas e evitar condicionais complicados e muito aninhados.
