---
title: "Como preparar o ambiente e escrever seu primeiro código com Kotlin"
date: "2017-08-23"
author: "alex.vieira"
authorEmail: "alex.vieira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Recentemente tivemos diversas novidades em um dos maiores eventos de tecnologia do mundo, o [Google I/O](https://events.google.com/io/) 2017.

Dentre as diversas novidades, uma das que muitos programadores ficaram atentos foi justamente no anúncio da Google informando que agora o [Kotlin](https://kotlinlang.org/) terá suporte total no Android Studio a partir da versão 3.0.

Tal informação gerou um grande impacto na comunidade que começou a dar bastante atenção na linguagem e conhecer os diferenciais que a linguagem tem em relação ao famoso Java.

Por mais que eu goste da linguagem Kotlin, não significa que ela é melhor ou pior que a linguagem Java ou outras linguagens existentes. Portanto, fique à vontade em avaliar e decidir o que faz mais sentido para o seu dia a dia.

## Afinal, o que é Kotlin?

Para quem nunca ouviu falar, Kotlin é uma linguagem de programação desenvolvida pela [JetBrains](https://www.jetbrains.com/) com as seguintes características:

- **Multiparadigma:** além de suportar a orientação a objetos, existem features comuns do paradigma funcional.

- **Multiplataforma:** mesmo rodando em seu [modo nativo](https://github.com/JetBrains/kotlin-native) ela também pode ser compilada e executada em um ambiente Java, isto é, [na própria JVM](https://kotlinlang.org/docs/reference/server-overview.html). Também, é possível [escrever em Kotlin e gerar código JavaScript](https://kotlinlang.org/docs/reference/js-overview.html).

- **Consistente:** permite criar estruturas mais enxutas, se comparado com a quantidade de código que outras linguagens apresentam, como é o caso de um [POJO no Java](http://www.guj.com.br/t/o-que-e-pojo-afinal/70787/3).

Existem muitas outras características motivadoras para considerarmos a utilização da linguagem, entretanto, o foco deste post será apresentar um primeiro contato com a linguagem para desenvolvedores Java.

Em outras palavras, não vou entrar em muitos detalhes sobre os motivos pelos quais você deve considerar o Kotlin, mas não se preocupe, deixo aqui a referência da [excelente documentação](https://kotlinlang.org/docs/reference/) que especifica com muitos detalhes tudo que veremos aqui. Bora começar?

## Preparando o ambiente

Como exemplo para o post, farei uso de um projeto para um sistema bancário da empresa Bytebank. Algo similar ao que vemos na apostila de [Java Orientação a Objetos](https://www.caelum.com.br/apostila-java-orientacao-objetos/).

Caso queira testar o Kotlin, recomendo fortemente o uso do [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) que é a IDE oficial da JetBrains para desenvolvimento Java, como também, para desenvolver em Kotlin. Claro, você pode estar pensando:

> _"Puts, eu vou ter que pagar o IntelliJ pra testar o Kotlin?"_

Não necessariamente, pois a própria versão Community já permite criar um projeto Kotlin sem nenhuma restrição, ou seja, tanto [projeto Kotlin "puro"](https://kotlinlang.org/docs/tutorials/getting-started.html) ou com uma [Build Tool](https://medium.com/@alex.felipe/entenda-o-que-s%C3%A3o-build-tools-do-mundo-java-682a26e38ff2) como é o caso do [Maven](https://kotlinlang.org/docs/reference/using-maven.html) ou [Gradle](https://kotlinlang.org/docs/reference/using-gradle.html).

Para os amantes do Eclipse, não fiquem chateados a [JetBrains também dá suporte para ele](https://kotlinlang.org/docs/tutorials/getting-started-eclipse.html).

> **Dica:** Caso seja o seu primeiro contato com o IntelliJ IDEA, aproveite para dar uma olhada em algumas das [dicas para quem está começando](http://blog.alura.com.br/dicas-e-truques-de-intellij-idea-para-quem-esta-comecando/) com a ferramenta :)

## Criando o primeiro código no Kotlin

Após realizar o setup do projeto, percebemos que temos praticamente o mesmo resultado quando criamos um projeto Java, isto é, temos o nosso famoso **src** para escrevermos o nosso código fonte.

Sendo assim, vamos criar o arquivo **Principal.kt**, em seguida, adicionaremos o método **main** que é responsável em executar a nossa aplicação, neste momento você deve estar pensando:

> _"Bom, então bora para o `public static void main…`"_

Pois é, a princípio pensamos isso mesmo, entretanto, como eu havia mencionado, o Kotlin tende ser mais consistente durante a escrita de código e por isso temos o seguinte resultado para o nosso famoso **main**:

```kotlin

fun main (args: Array){
    println("Bem vindo ao Bytebank")
}
```

Com o **main** implementado, precisamos apenas executar para verificar o que acontece! Nesse instante, provavelmente, você deve estar pensando:

> _"Calma aí! E como fica a estrutura da classe para deixar esse código do main?"_

Um dos grandes detalhes do Kotlin é que **não precisamos de uma estrutura de classe** para criar uma função, nem para executar o **main**! Portanto, ao executar o código do jeito que tá, temos o seguinte resultado:

```kotlin

Bem vindo ao Bytebank
```

Algo já esperado né? Mas, além de não precisar de uma classe, note que temos algumas mudanças na assinatura do nosso **main**:

- A primeira delas é justamente a _keyword_ **fun**, isso significa que no Kotlin, ao invés de declarar métodos, declaramos funções. Na prática acabam sendo a mesma coisa, a diferença é que identificamos as funções por meio de uma palavra reservada da linguagem.

- Note que agora não precisamos mais indicar o modificador de acesso **public**, ou seja, por padrão o Kotlin já considera que todos os membros sejam públicos, isto é, todas as variáveis, funções ou classes serão marcadas como **public** caso não tenha indicado um modificador de acesso em específico.

- Também em nenhum momento falamos qual é o retorno da função, isso significa que por padrão, quando não indicamos o retorno, o Kotlin já devolve uma classe chamada [`Unit`](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-unit/index.html), essa classe representa o nosso famoso `void`.

Claro, observe que o nosso famoso **sysout** (`System.out.Println()`) ficou bastante resumido também! Apenas chamando o `println()`, como também, não está com os famosos ponto e vírgula (`;`)...

> **Observações:** Por mais que não tenha o " ; " para indicar o final da instrução de uma linha de código, não significa que teremos sérios problemas, por exemplo, ao tentar fazer o seguinte código: `println("Bem vindo ao Bytebank") println(“novo print”)`.
> 
> O Kotlin tenta interpretar o segundo `println()` e, por isso, apresenta um erro.
> 
> Em outras palavras, o " ; “ é opcional, portanto, para fazer com que o código de exemplo funcionasse, bastaria deixar da seguinte maneira: `println("Bem vindo ao Bytebank"); println(“novo print")`.

O mesmo código em Java, teria o seguinte aspecto:

```java

public class Principal {
    public static void main(String[] args) {
        System.out.println("Bem vindo ao Bytebank");
    }
}
```

Veja que nesse primeiro passo o Kotlin foge bastante dos famosos [boilerplates](https://pt.wikipedia.org/wiki/Boilerplate_code) que vemos no Java.

## Utilizando as primeiras variáveis

Bacana, fizemos o nosso primeiro contato, portanto vamos avançar. O nosso próximo passo é representar uma conta por meio dos seguintes valores:

- Número da conta
- Titular
- Saldo

No Java teríamos o seguinte código:

```java

public class Principal {
    public static void main(String[] args) {
        System.out.println("Bem vindo ao Bytebank");
        int numeroDaConta = 1;
        String titular = "Alex";
        double saldo = 100.0;
    }
}
```

E no Kotlin? Vejamos:

```kotlin

fun main(args: Array) {
    println("Bem vindo ao Bytebank")
    val numeroDaConta = 1
    val titular = "Alex"
    val saldo = 100.0
}
```

Novamente, temos algumas diferenças um tanto quanto notáveis:

- As variáveis não apresentam seus tipos;
- Para declarar variáveis estamos usando a _keyword_ **val**.

Nesse momento você pode estar pensando:

> _"O que está acontecendo no código? As variáveis não possuem tipos?"_

A princípio pode parecer que as variáveis não possuem tipos, mas se tentarmos, por exemplo, verificar qual é a classe do Java que é utilizada em runtime:

```kotlin

println(numeroDaConta.javaClass)
println(titular.javaClass)
println(saldo.javaClass)
```

Temos o seguinte resultado:

```kotlin

int
class java.lang.String
double
```

Veja que o Kotlin já consegue assinar pra gente o tipo da variável em tempo de compilação! Mas agora vem aquela questão bem comum:

> _"Mas se no caso, eu quiser restringir o tipo da variável?"_

## Lidando com a tipagem das variáveis

Pensando justamente nesse detalhe, temos a capacidade de atribuir o tipo que esperamos para a variável usando o seguinte padrão: `nomeDaVariavel: Tipo`. Considerando o nosso exemplo inicial, poderíamos escrever o nosso código da seguinte maneira:

```kotlin

val numeroDaConta: Int = 1
val titular: String = "Alex"
val saldo: Double = 100.0
```

Repare que essa abordagem deixa o código mais flexível, isto é, uma vez que é bem claro qual o tipo da variável, podemos deixar o tipo implícito para evitar um código ambíguo durante a leitura do mesmo.

Entretanto, nos casos que assinamos uma variável e não é tão claro o tipo que ela está recebendo, podemos deixar o tipo de forma explícita para facilitar a leitura e compreensão.

Um dos comportamentos comuns quando usamos variáveis em qualquer linguagem de programação, é justamente o fato de alterarmos os seus valores, por exemplo, ao invés de deixar o titular com apenas o primeiro nome, é muito comum colocarmos o nome completo, no meu caso Alex Felipe, portanto, vamos atribuir novamente o valor da variável `titular`:

```kotlin

val numeroDaConta: Int = 1
val titular: String = "Alex"
val saldo: Double = 100.0
titular = "Alex Felipe"
```

Nesse instante o Kotlin apresenta um erro: **_Val cannot be reassigned_**. Em outras palavras, ele está nos dizendo que não podemos reatribuir um valor para a variável `titular`. E agora?

## Maneiras de declarar variáveis

Como vimos até agora, tivemos que utilizar a _keyword_ `val` para declarar uma variável no Kotlin, mas o que isso realmente significa?

Para a galera de Java, a única preocupação durante a criação da variável é justamente em indicar o seu tipo junto do seu nome sucessivamente, certo?

Mas no Kotlin o conceito é um pouco diferente, quando usamos o `val` estamos indicando que queremos uma variável **_read-only_**, ou seja, apenas para leitura, ou melhor, uma constante!

É justamente por este motivo que o Kotlin apresentou aquele erro… Com toda certeza você está pensando:

> _"E como eu declaro, de fato, uma variável que permite alterar o seu valor"_

Para isso o Kotlin reserva a _keyword_ `var`. Continuando com o nosso código anterior, poderíamos alterar o nome do titular com o seguinte ajuste:

```kotlin

val numeroDaConta: Int = 1
var titular: String = "Alex"
val saldo: Double = 100.0
titular = "Alex Felipe"
```

De forma resumida, caso suas variáveis precisarem mudar de valor, utilize o `var`, caso contrário, utilize o `val`.

> É válido mencionar que, se sua variável não foi criada para ser modificada no código, sempre considere o uso do `val` para garantir a integridade do seu código :)

## Criando classes

Veja que já conseguimos representar uma conta do banco por meio das variáveis que criamos, mas sabemos que, ao invés de usarmos variáveis soltas, utilizamos classes para representar uma entidade do mundo real, como é o caso da nossa conta!

Sendo assim, vamos criar a classe que conterá as variáveis que acabamos de criar. No Java temos a seguinte estrutura:

```java

public class Conta {

    private int numeroDaConta;
    private String titular;
    private double saldo;

    public int getNumeroDaConta() {
        return numeroDaConta;
    }

    public void setNumeroDaConta(int numeroDaConta) {
        this.numeroDaConta = numeroDaConta;
    }

    public String getTitular() {
        return titular;
    }

    public void setTitular(String titular) {
        this.titular = titular;
    }

    public double getSaldo() {
        return saldo;
    }

    public void setSaldo(double saldo) {
        this.saldo = saldo;
    }

}
```

Como podemos ver, temos atributos privados junto dos seus métodos de acesso (getters e setters). E como fica no Kotlin?

```kotlin

class Conta {
    var numeroDaConta: Int = 0
    var titular: String = ""
    var saldo: Double = 0.0
}
```

Bem menor, concorda? Mas perceba que temos um detalhe bem peculiar:

> _"Por que tivemos que inicializar os membros da classe?"_

## Seguindo as boas práticas de programação

Um dos detalhes bem bacana dentro do Kotlin, é justamente a ideia de seguir diversas das boas práticas de programação, ou seja, a linguagem, por padrão, não permite que uma variável não seja inicializada e, como podemos ver, isso conta para membros de classes também.

Portanto, se não inicializamos via construtor, por exemplo, precisamos apresentar um valor para a variável por padrão.

Claro, existem casos que inicializar uma variável não é um comportamento esperado, como é o caso do conceito de [injeção de dependência](https://blog.caelum.com.br/ioc-e-di-para-frameworks-mvc/). No Kotlin, temos algumas alternativas para lidar com esse tipo de situação, no caso da injeção de dependência, poderíamos usar o recurso `lateinit`.

> _Muito legal, mas... Como faço pra instanciar uma classe e atribuir os valores em seus atributos no Kotlin?_

## Criando objetos e atribuindo valores aos seus membros

Sendo mais prático, faríamos o seguinte código para criar uma conta no Java:

```java

Conta conta = new Conta();
conta.setNumeroDaConta(1);
conta.setTitular("Alex Felipe");
conta.setSaldo(100.0);
```

No Kotlin ficaria da seguinte maneira:

```kotlin

val conta = Conta()
conta.numeroDaConta = 1
conta.titular = "Alex Felipe"
conta.saldo = 100.0
```

Note que não é necessário usar a _keyword_ **new** para criar uma instância, também, perceba que usamos o operador "=" para atribuir um valor para cada membro da classe `Conta`. E tenho quase certeza que você está se perguntando:

> _"Considerando as boas práticas de OO, acessar um atributo diretamente é perigoso, justamente por permitir que qualquer um manipule o valor como bem entender... Então por que deixamos assim no Kotlin?"_

## Utilização de properties

A princípio, parece que estamos acessando diretamente os atributos da classe olhando a estrutura do código escrito em Kotlin, mas, na verdade, estamos acessando uma _[property](https://en.wikipedia.org/wiki/Property_(programming))_, ou seja, ao invés de acessar o campo em si, acessamos de fato os seus "getters" e “setters”.

> _"Mas Alex, como você me garante que é uma property mesmo? Pois no meu ponto de vista parece um acesso direto..."_

Essa é uma pergunta recorrente e é fácil testarmos o código para garantir se realmente estamos lidando com uma property ou um campo de fato. Para isso, podemos implementar um setter qualquer de uma property, por exemplo, vamos alterar o setter da property `numeroDaConta`:

```kotlin

class Conta {
    var numeroDaConta: Int = 0
    set(value) {
        println("Recebendo valor na property numeroDaConta")
        numeroDaConta = value
    }
    var titular: String = ""
    var saldo: Double = 0.0
}
```

Veja que foi adicionada a _keyword_ `set` logo abaixo da property `numeroDaConta`. Dentro do `set` estamos fazendo um print indicando recebemos um valor na property. Vamos testar?

[![](https://blog.caelum.com.br/wp-content/uploads/2017/08/stackoverflow.png)](https://blog.caelum.com.br/wp-content/uploads/2017/08/stackoverflow.png)

Isso mesmo, tomamos um `StackOverFlowError` por realizar uma operação sem fim! Em outras palavras, fizemos um setter que fica chamando ele mesmo, ou melhor, um setter recursivo!

## Mudando o valor de uma property diretamente

Para evitar esse tipo de situação, isto é, caso seja necessário modificar o próprio campo de uma property dentro do seu setter, o Kotlin oferece um recurso conhecido como _[Backing Fields](https://kotlinlang.org/docs/reference/properties.html)_. Basicamente, usamos a _keyword_ **field** e temos acesso direto ao atributo:

```kotlin

class Conta {
    var numeroDaConta: Int = 0
    set(value) {
        println("Recebendo valor na property numeroDaConta")
        field = value
    }
    var titular: String = ""
    var saldo: Double = 0.0
}
```

Testando novamente percebemos que a mensagem "Recebendo valor na property numeroDaConta" é apresentada apenas uma única vez, e se imprimirmos a property:

```kotlin

println(conta.numeroDaConta)
```

Apenas o valor dela é apresentado!

## Para saber mais

Por mais que tenhamos visto bastante das peculiaridades do Kotlin, ainda é só o começo, isto é, existem diversas outras features interessantes da linguagem para colocarmos na balança se é algo que vale a pena investir o tempo de estudo ou não, veja alguma delas:

- [Null Safety](https://kotlinlang.org/docs/reference/null-safety.html)

- [Data Class](https://kotlinlang.org/docs/reference/data-classes.html)

- [Functions](https://kotlinlang.org/docs/reference/functions.html)

- [Lambdas](https://kotlinlang.org/docs/reference/lambdas.html)

- [Extensions](https://kotlinlang.org/docs/reference/extensions.html)

Claro, além delas também existe o caso da interoperabilidade que existe entre o código Kotlin e Java que de fato é um dos grandes destaques de usar o Kotlin no ambiente Java, ou seja, chamar classes do Java dentro do código Kotlin como chamar classes do Kotlin no Java.

## Mais conteúdos de Kotlin

Agora que você já teve o primeiro contato com Kotlin, vou aproveitar e deixar um agregador de conteúdo onde listo todos os conteúdos de Kotlin que escrevi e que ainda vou escrever. Espero o seu feedback lá também ;)

https://medium.com/collabcode/seja-tamb%C3%A9m-um-desenvolvedor-kotlin-770188949486

## Conclusão

Como vimos, o Kotlin vem com a proposta de tentar simplificar o máximo possível o nosso código, isto é, evitando os famosos boilerplates no código. Também, a ideia da linguagem é tentar aplicar muitas das boas práticas de programação existentes em outras linguagens, como é caso do Swift, C# entre outras. De modo geral aprendemos os seguintes tópicos:

- Criação da função main
- Declaração de variáveis e seus tipos
- Criação de classes e objetos
- Utilização e acesso direto de properties

Agora que teve esse primeiro contato com o Kotlin, aproveite e deixe o seu comentário sobre o que achou da linguagem :)
