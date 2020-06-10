---
title: "Mudanças simples x Soluções simples"
date: "2010-11-18"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O processo de entrega de uma funcionalidade nova envolve implementar ou corrigir algo que o nosso sistema atualmente não comporta. Dados os problemas que o programa resolve nesse instante, existem diversas maneiras distintas de resolvê-los, todas válidas, algumas mais limpas e mais simples do que outras.

O gráfico a seguir mostra uma situação onde nosso programa não resolve o problema atual que desejamos atacar, onde:

a) **vermelho**: representa variações do sistema que não resolveriam meu problema; b) **amarelo**: minha situação atual; c) **verde**: soluções possíveis.

\[caption id="attachment\_3453" align="aligncenter" width="300" caption="Soluções possiveis para um problema"\][![Soluções possiveis para um problema](https://blog.caelum.com.br/wp-content/uploads/2010/11/1-300x251.png)](https://blog.caelum.com.br/wp-content/uploads/2010/11/1.png)\[/caption\]

Mas dentro das soluções possíveis, existem aquelas **mudanças que são simples** de serem executadas, por exemplo incluir um "if" ou simplesmente fazer um copy e paste de código, um pequeno passo, uma mudança simples, um _baby step_. **Mudanças simples que resolvem o problema**, representadas em verde escuro aqui:

\[caption id="attachment\_3454" align="aligncenter" width="279" caption="Mudanças simples"\][![Mudanças simples](https://blog.caelum.com.br/wp-content/uploads/2010/11/2.png)](https://blog.caelum.com.br/wp-content/uploads/2010/11/2.png)\[/caption\]

Por fim, existem as soluções mais simples para o problema. A solução mais simples pode ser extrair uma classe ou utilizar uma biblioteca que executa a lógica para nós, por exemplo. No diagrama a seguir as soluções mais simples são representadas em azul:

\[caption id="attachment\_3455" align="aligncenter" width="300" caption="Soluções simples"\][![Soluções simples](https://blog.caelum.com.br/wp-content/uploads/2010/11/3-300x252.png)](https://blog.caelum.com.br/wp-content/uploads/2010/11/3.png)\[/caption\]

A execução desses passos, _baby steps_, traz o código para dentro das mudanças simples que resolvem o problema. Mas a sequência dos mesmos pode nos levar para diversos lugares diferentes: alguns mais próximos das soluções mais simples, [outros cada vez mais distantes das mesmas, nos deixando com um débito técnico cada vez maior](http://www.aniche.com.br/2010/11/voce-quer-testar-metodos-privados-certeza/).

Pior ainda, a adoção cega de _baby steps_ ou de mudanças simples sem nenhuma prática para garantir a qualidade pode levar a uma situação onde seja impossível chegar em uma solução simples: seu sistema vira uma [grande bola de lama](http://en.wikipedia.org/wiki/Big_ball_of_mud).

Essa situação é o inferno de qualquer sistema: passos simples não são mais capazes de [manter as coisas simples](http://en.wikipedia.org/wiki/KISS_principle).

\[caption id="attachment\_3458" align="aligncenter" width="260" caption="Uso puro de baby steps não implica em melhoria de qualidade. Passos muito simples podem levar seu sistema a situações negativas."\][![Uso puro de baby steps](https://blog.caelum.com.br/wp-content/uploads/2010/11/Diagramalindo-389x1024.png)](https://blog.caelum.com.br/wp-content/uploads/2010/11/Diagramalindo.png)\[/caption\]

Em algumas situações, o _baby step_ pode implicar em uma das melhores soluções, mas para isso é necessário o domínio da tecnologia, do negócio, design e linguagem, algo que nem sempre é a realidade do desenvolvedor quando escreve uma funcionalidade:

\[caption id="attachment\_3463" align="aligncenter" width="512" caption="Baby step de um desenvolvedor com domínio forte da tecnologia, linguagem, domínio de negócio e sistema."\][![Baby step e design](https://blog.caelum.com.br/wp-content/uploads/2010/11/Diagramalindo-3.png)](https://blog.caelum.com.br/wp-content/uploads/2010/11/Diagramalindo-3.png)\[/caption\]

Como é difícil executar um passo-a-passo muito suave e chegar em uma solução simples qualquer, adicionamos mais uma técnica ao processo: além de executar as tarefas em pedaços pequenos (mas não mínimos), refatoramos nosso código, para simplificá-lo.

Cada passo de simplificação através da refatoração coloca nosso código mais próximo do sistema ideal: o mais simples de todos que resolve nossos problemas.

\[caption id="attachment\_3462" align="aligncenter" width="512" caption="A refatoração que a cada passo simplifica o código nos leva cada vez mais próximos a melhor solução para nossos problemas."\][![Refatorando](https://blog.caelum.com.br/wp-content/uploads/2010/11/Diagramalindo-2-1024x853.png)](https://blog.caelum.com.br/wp-content/uploads/2010/11/Diagramalindo-2.png)\[/caption\]

[Com a refatoração contínua](http://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672), cada passo que damos ao implementar uma nova feature nos mantém ainda próximos das soluções simples.

Mais importante do que fazer passos minúsculos (_baby steps_) é fazer passos que fazem sentido para levá-lo em direção a melhor solução (e não somente a mudança mais simples). Para isso não podemos nos esquecer de refatorar. E não só refatorações de baixo nível (como renomear variáveis, mover métodos, etc), mas sim refatorações de alto nível (como abstrair comportamentos).

Imagine o seguinte trecho de código, responsável por calcular o salário de um funcionário. Dependendo do cargo do funcionário, o algoritmo para o cálculo é diferente.

\[java\] public class CalculoDeSalario {

public CalculoDeSalario(RegrasFiscais regrasFiscais, ...) { // ... }

public double calcula(Funcionario f) { if(f.getCargo() == Cargo.VENDEDOR) { // calcula salario do vendedor usando regras fiscais, etc }

if(f.getCargo() == Cargo.GERENTE) { // calcula salario do gerente usando regras fiscais, etc } } } \[/java\]

Agora suponha que um novo cargo apareceu na empresa e precisamos calcular o salário das pessoas que farão o trabalho desse novo cargo. O código mais simples que faria isso acontecer, seria:

\[java\] public class CalculoDeSalario {

public CalculoDeSalario(RegrasFiscais regrasFiscais, ...) { // ... }

public double calcula(Funcionario f) { if(f.getCargo() == Cargo.VENDEDOR) { // calcula salario do vendedor usando regras fiscais, etc }

if(f.getCargo() == Cargo.GERENTE) { // calcula salario do gerente usando regras fiscais, etc }

if(f.getCargo() == Cargo.MARKETING) { // calcula salario do marketing usando regras fiscais, etc } } } \[/java\]

Mas essa não é a melhor solução que resolveria o problema. A cada novo _if_ inserido nesse código, mais longe estamos da melhor solução. O design fica cada vez mais rígido. E pior: refatorar esse trecho de código fica cada vez mais difícil: até mesmo o segundo if que foi adicionado ao código já havia o afastado da melhor solução!

Ao implementar uma nova funcionalidade, o programador [deve observar o design atual da sua aplicação](http://www.agiledata.org/essays/tdd.html). Se o design atual já provê uma maneira fácil de implementar a funcionalidade, então vá em frente. Caso contrário, o programador deve **refatorar o design para permitir que você possa adicionar essa funcionalidade da maneira mais simples possível**.

No exemplo acima, uma solução seria criar uma estratégia (do padrão de projeto Strategy) para calcular o salário de acordo com o cargo. Cada cargo seria responsável por devolver o seu algoritmo de cálculo. Por exemplo:

\[java\] public class CalculoDeSalario {

public CalculoDeSalario(RegrasFiscais regrasFiscais, ...) { // ... }

public double calcula(Funcionario f) { AlgoritmoDeCalculoDeSalario calculadora = f.getCargo().getAlgoritmoDeCalculo(regrasFiscais); return calculadora.calcula(); } } \[/java\]

Se essa é a melhor solução, é difícil dizer. Mas com certeza ela é uma solução melhor do que as anteriores. Mas, veja que após essa refatoração, implementar o cálculo de salário para o novo cargo é muito mais fácil. Agora posso voltar aos meus _baby steps_, já que a implementação corrente está em um lugar seguro.

Toda essa experiência se resume a uma frase que surgiu em uma conversa entre Guilherme Silveira e Maurício Aniche:

> A mudança mais simples para resolver um problema não é necessariamente a solução mais simples para resolvê-lo.
