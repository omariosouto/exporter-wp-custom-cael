---
title: "Classes aninhadas: o que são e quando usar?"
date: "2012-03-08"
author: "eric"
authorEmail: "ericzumba@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[![](https://blog.caelum.com.br/wp-content/uploads/2012/03/Nested-Class-in-JAVA-150x150.png "Nested-Class-in-JAVA")](http://shivasoft.in/blog/wp-content/uploads/2011/03/Nested-Class-in-JAVA.png)Como receber notificações de que a um atributo da sessão HTTP foi mudado? Registramos um listener, o padrão observer. Como fazemos para registrar uma ação de saída da aplicação ao clicar o botão "_Sair_"? Mesmo padrão: passamos um `ActionListener` para o `addActionListener`:

\[java\] public class Aplicacao { private JButton botaoSair; private void montaBotaoSair() { botaoSair = new JButton("Sair"); botaoSair.addActionListener(new AcaoSaida()); } } \[/java\]

Onde a ação de saída pode estar definida numa outra classe:

\[java\] public class AcaoSaida implements ActionListener { public void actionPerformed(ActionEvent e) { System.exit(0); } } \[/java\]

Será mesmo que precisamos de uma classe pública (ou mesmo package-protected) chamada `AcaoSaida`? Quem mais vai utilizá-la? Talvez seja uma melhor opção criar essa classe de tal forma que apenas a `Aplicacao` possa enxergá-la. Para isso, podemos cria-la dentro da propria classe `Aplicacao`, de forma **aninhada**\*\*:

\[java\] public class Aplicacao { private JButton botaoSair; private void montaBotaoSair() { botaoSair = new JButton("Sair"); botaoSair.addActionListener(new AcaoSaida()); }

private class AcaoSaida implements ActionListener { public void actionPerformed(ActionEvent e) { System.exit(0); } } } \[/java\]

Considerando que a ação de saída estará exclusivamente vinculada ao botão "Sair" e que, portanto, iremos instanciar apenas um objeto a partir dessa classe, podemos definir a classe no próprio momento em que decidimos instanciar o objeto. Como não precisaremos reaproveitar o comportamento da classe `AcaoSaida` em nenhum outro ponto dessa classe, podemos simplesmente abreviar o trabalho de sequer definir um nome para ela:

\[java\] public class Aplicacao { private JButton botaoSair; private void montaBotaoSair() { botaoSair = new JButton("Sair"); botaoSair.addActionListener(new ActionListener() { public void actionPerformed(ActionEvent e) { System.exit(0); } }); } } \[/java\]

A sintaxe do Java permite darmos `new` em uma interface, como é o caso de `new ActionListener` desde que, após os parênteses do construtor, você implemente todas os métodos abstratos. Qual será o nome dessa classe? Não há! Por este motivo ela é denominada **classe anônima**. Repare que isto é basicamente um truque do compilador: será gerado um arquivo, o `Aplicacao$1.class`, contendo o bytecode referente a esta classe anônima.

Aqui são viáveis as 3 alternativas. O fator mais importante para decidirmos qual estratégia usar foi o quão reaproveitável será a lógica definida dentro da classe. Mas, ainda que essa lógica seja utilizada num único ponto, poderíamos sim, por organização ou gosto, tê-la mantido como uma classe top-level. A decisão, nesse caso, é até um pouco subjetiva, podendo ser refatorada quando necessária.

Mas e se a ação de sair necessitasse o nome do usuário para poder criar uma mensagem melhor? Se ela fosse uma classe top-level, como a `AcaoSaida`, poderíamos receber como argumento no construtor uma `String` ou entidade `Usuario`. Se ela precisasse de muitos outros dados, esse construtor pode começar a ficar muito grande, mostrando aí uma possível dependência mais forte. Mais ainda: se a ação que será disparada precisasse devolver algum valor, como resolver? Um getter na nossa classe `AcaoSaida`? Talvez vários getters, dependendo do caso.

Em situações como essas, onde o listener parece estar tão intimamente ligado ao objeto que registra os listeners, podemos tirar proveito das classes aninhadas, pois acessam os atributos das classes a qual elas pertencem\*\*. Elas podem acesssar tanto o `this` referente ao seu objeto, quanto ao `this` da classe que a contém (_outer class, enclosing class_, classe externa).

Como exemplo de cenário onde o uso de classes privadas é mais apropriado, o [curso online de Design Patterns da Caelum](http://www.caelum.com.br/curso/online/design-patterns/) propõe uma situação para o uso do padrão State. Temos uma classe `Conta` cujos métodos de saque e depósito tem comportamentos diferentes dependendo de a conta ter saldo positivo ou negativo. Caso o saldo seja positivo, no depósito a conta deve descontar 2% do valor. Caso negativo, no depósito deve-se descontar 5% e o saque deve ser impossibilitado com o lançamento de uma exceção.

A implementação dessa solução pode ser estruturada com duas classes que definem o comportamento da conta `Positiva` e `Negativa`, possibilitando que a conta trabalhe de forma diferente nesses casos (cobrando uma taxa a mais de qualquer saque no caso do estado negativo). Ambas assinam o contrato de serem estados de uma conta, definido pela interface `EstadoDaConta`.

\[java\] public class Conta { private double saldo; private EstadoDaConta estado;

private void atualizaEstado() { this.estado = saldo >= 0 ? new Positivo() : new Negativo(); }

public void deposita(double valor) { this.estado.deposita(valor); atualizaEstado(); }

public void saca(double valor) { this.estado.saca(valor); atualizaEstado(); }

private interface EstadoDaConta { void saca(double valor); void deposita(double valor); }

private class Positivo implements EstadoDaConta { public void deposita(double valor) { saldo += valor \* 0.98; }

public void saca(double valor) { saldo -= valor; } }

private class Negativo implements EstadoDaConta { public void deposita(double valor) { saldo += valor \* 0.95; }

public void saca(double valor) { throw new IllegalStateException(); } } } \[/java\]

Como as classes aninhadas podem acessar atributos privados da classe onde estão aninhadas, conseguimos encapsular novamente todo o estado de uma Conta. Dando acesso ao seu comportamento apenas pelos métodos públicos `saca` e `deposita`.

O interessante sobre a situação colocada pelo uso do design pattern state é que, ao contrário da situação do simples `ActionListener` para o botão "Sair", o uso de classes aninhadas resolve um problema importante que é o afrouxamento do encapsulamento da classe agregadora. Usando classes aninhadas, pudemos manter o bom encapsulamento da classe `Conta`. Vale lembrar que exagerar no uso de classes aninhadas pode levar a implementações gigantes e de difícil manutenção, logo a opção por esta estrutura deve ser sempre bem justificada.

Um outro excelente exemplo de uso de classes aninhadas é a implementação de Iterators na api de Collections. Os iterators estão aninhados nas listas. Você pode ver isso na [linha 780 da classe `ArrayList`](http://www.docjar.com/html/api/java/util/ArrayList.java.html), e dentro de seus métodos, o iterador acessa a array através de `ArrayList.this.elementData` (poderia utilizar apenas `this.elementData`, mas essa forma fica mais claro que o atributo elementData pertence a `ArrayList`, e não ao `Itr`, além de resolver possíveis conflitos no caso de atributos com mesmo nome nas duas classes).

Certamente as listas poderiam ter seus iteradores implementados de maneira não aninhada, mas o fortíssimo acoplamento entre as duas classes ficaria claramente indicado pelo excesso de métodos expostos apenas para compartilhar objetos e resultados.

\*\* Há um pouco de detalhes sobre classes aninhadas que não citamos durante o artigo, para simplificar. No dia a dia e até na literatura, o termo _classe interna_ é empregado genericamente para representar todo tipo de classe aninhada, porém ela é um subgrupo. Há dois tipos: as estáticas (utilizando o próprio modificador static, e dessa forma sem estar atrelada a um objeto da outter-class) e as internas (ou não estáticas), que vimos aqui. Mais ainda: quando declaramos [uma classe dentro de um método](http://docstore.mik.ua/orelly/java-ent/jnut/ch03_11.htm), chamamos de classe local, que também pode ser estática ou interna, dependendo do método onde foi declarada. As classes anônimas se enquadram nos dois tipos de aninhadas, mas não possuem nome e não faz sentido declarar um modificador de acesso. Tem muitos outros detalhes que não cobrimos nesse curto post.
