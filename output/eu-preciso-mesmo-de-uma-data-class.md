---
title: "Eu preciso mesmo de uma Data Class no Kotlin?"
date: "2018-03-05"
author: "alex.vieira"
authorEmail: "alex.vieira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Existem diversos tópicos, artigos e projetos relacionados à linguagem Kotlin que reforçam bastante a ideia de códigos mais concisos em relação ao Java. De fato, em muitas das abordagens, é bem clara a diferença de escrita e leitura de código.

Porém, por ser uma linguagem relativamente nova, é muito comum aparecerem afirmações tendenciosas, principalmente quando existem comparações entre linguagens.

Sendo assim, vou abordar um dos assuntos que mais vejo a comunidade usando/falando, mas que não abordado exatamente como muitos dizem. Este é o famoso **Data Class**.

## O que muitos dizem...

De uma maneira geral, muitos afirmam que se você quer criar uma classe para representar um [POJO](https://pt.wikipedia.org/wiki/Plain_Old_Java_Objects) de maneira consisa, só precisa criar um Data Class, em código teríamos o seguinte resultado:

```kotlin

data class Pessoa(
       val nome: String,
       val idade: Int)
```

O código é bem mais enxuto comparando com a amostra que apresentei no [primeiro artigo que escrevi de Kotlin](https://blog.caelum.com.br/como-preparar-o-ambiente-e-escrever-seu-primeiro-codigo-com-kotlin/). Se criarmos uma pessoa a partir desta classe, temos a seguinte possibilidade:

```kotlin

val joao = Pessoa("João da Silva", 28)
```

Repare que já envio o nome e idade via construtor. A princípio, nenhuma novidade, porém e se tentarmos criar uma pessoa sem enviar os argumentos do construtor?

```kotlin

val joao = Pessoa("João da Silva", 28)
val maria = Pessoa()
```

O código não compila! Você pode estar pensando:

> _“Isso acontece pois estamos trabalhando com properties `val` que precisam ser inicializadas!”_

Pode até ser um motivo, mas perceba que mesmo modificando para properties mutáveis:

```kotlin

data class Pessoa(
       var nome: String,
       var idade: Int)
```

A instância de construtor vazio não funciona! Isso acontece, pois o Kotlin exige que todas as variáveis ou properties sejam inicializadas por padrão! Portanto, declarar um Data Class não significa que temos um POJO.

## Criando construtor vazio ao inicializar properties

Para lidar com esse tipo de situação, podemos usar uma técnica muito comum que é a inicialização padrão:

```kotlin

data class Pessoa(
       var nome: String = "",
       var idade: Int = 0)
```

Dessa forma, somos capazes de criar um objeto que vai representar a `Maria` sem nenhum problema:

```kotlin

val joao = Pessoa("João da Silva", 28)
val maria = Pessoa()
maria.nome = "Maria da Silva"
maria.idade = 28
```

Em outras palavras, para mantermos o mesmo comportamento que vemos no POJO, precisamos definir valores padrões para as nossas properties! Agora vem uma nova questão:

> “Precisamos mesmo de um Data Class para definir um POJO?”

## Criando o real POJO

Pode até parecer estranho, mas se removermos a keyword **data**:

```kotlin

class Pessoa(
       var nome: String = "",
       var idade: Int = 0)
```

O código além de compilar, funciona sem nenhum problema… **Portanto, se a sua intenção é criar um POJO conciso, você não precisar declarar uma Data Class**. Neste instante, muito provavelmente, você deve estar nessa situação:

> _“Então para que serve o Data Class?”_

A ideia do Data Class, além de manter o mesmo comportamento de uma classe comum, é nos oferecer funções pré implementadas baseando-se nas properties do construtor primário.

> Além do construtor primário, somos capazes de utilizar um construtor secundário nas classes do Kotlin, esse assunto eu abordo com bastante detalhe no meu [curso de Android com Kotlin](https://www.alura.com.br/curso-online-android-com-kotlin-parte-1).

Por debaixo dos panos, o Data Class nos entrega as seguintes funções:

- `equals()/hashCode()`: para comparação de objetos;
- `toString()`: impressão padrão no formato "Pessoa(nome=João da Silva, idade=28)";
- `componentN()`: para permitir o uso do Destructuring Declaration;
- `copy()`: para copiar o objeto com flexibilidade.

Aproveitando a menção dessas funções, vamos explorar como cada uma delas funciona.

## Comparando os objetos

Para testarmos o comportamento de comparação, vamos criar um clone do João da Silva e realizar um print:

```kotlin

val joao = Pessoa("João da Silva", 28)
val cloneDoJoao = Pessoa("João da Silva", 28)
print(joao == cloneDoJoao)
```

> Diferentemente de usar um `equals()` estou usando o operador `==` que é traduzido como `equals()`, isso é possível devido ao [Operator Overloading](https://kotlinlang.org/docs/reference/operator-overloading.html). Vou deixar esse assunto para um outro momento.

O resultado desse código é `true`, portanto, a comparação entre os objetos está sendo feita a partir das suas properties!

## Imprimindo os objetos do Data Class

Agora vamos ver o `toString()` da Maria:

```kotlin

val maria = Pessoa()
maria.nome = "Maria da Silva"
maria.idade = 28
print(maria)
```

Ao testar o código temos o seguinte resultado:

```

Pessoa(nome=Maria da Silva, idade=28)
```

## Pegando as properties em variáveis separadas

Com a implementação do `componentN()` somos capazes de declarar variáveis a partir das properties do objeto conforme a proposta do Destructuring Declaration:

```kotlin

val (nome, idade) = maria
```

Isso mesmo, as variáveis, `nome` e `idade` são uma cópia das properties do objeto `maria`.

## Copiando objetos

Por fim, se a Maria tiver uma irmã gêmea podemos copiar a idade da Maria e modificar apenas apenas o nome para o da irmã gêmea dela:

```kotlin

val maria = Pessoa()
maria.nome = "Maria da Silva"
maria.idade = 28
val julia = maria.copy("Julia da Silva")
print(julia)
```

Ao testar o código temos o seguinte resultado:

```

Pessoa(nome=Julia da Silva, idade=28)
```

## Resumindo

A conclusão que chegamos é que o Data Class é uma alternativa para criarmos uma classe, mas, como vimos, ele só é necessário caso uma das suas implementações padrões seja necessária para você. **Portanto, não o utilize caso você não tenha a intenção de usar alguma de suas implementações padrões**.

É válido lembrar que ambas funções, funcionam apenas para properties do construtor primário, ou seja, as demais properties da classe, como por exemplo no corpo da mesma, não serão consideradas.

Agora que conhece o Data Class, o que achou da proposta dele? Compartilhe comigo nos comentários.
