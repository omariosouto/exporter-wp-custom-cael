---
title: "Swift a nova linguagem da Apple"
date: "2014-10-07"
author: "fabio.pimentel"
authorEmail: "fabio.pimentel@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Se você é desenvolvedor Apple já deve saber que uma nova linguagem de programação foi lançada no último [WWDC 14](https://developer.apple.com/wwdc/), Swift é nome dela.

Até a versão do iOS 7 a linguagem utilizada oficialmente era o Objective-C que sempre foi um degrau mais alto na escalada pela proficiência, tanto para o desenvolvedor que vinha de outras linguagens quanto para os iniciantes no mundo de programação. A partir do iOS 8 as aplicações poderão ser desenvolvidas com Objective-C e Swift, inclusive com as duas no mesmo projeto.

A tendência é que o Swift atraia mais desenvolvedores por sua facilidade e semelhança com outras linguagens. Nesse artigo vamos focar nos conceitos iniciais dessa nova linguagem.

## Definição de variáveis e constantes

Quando se aprende uma nova linguagem o primeiro passo normalmente a ser dado é a definição de uma variável. No Swift não será diferente, observe o exemplo:

```

 var nome = "Fábio"
```

O primeiro detalhe de certa forma comum nas linguagens mais recentes é a ausência do ponto e vírgula, ele é opcional para a linguagem.

Perceba que não foi necessário a declaração do tipo String, isso porque o compilador infere o tipo após a atribuição. A declaração acima tem os mesmos efeitos práticos da seguinte:

```

var nome: String = "Fábio"
```

Dessa maneira o código abaixo nem compila, pois a linguagem possui uma tipagem estática.

```

var nome = "Fábio"
nome = 10 // Não compila pois nome é String, já que esse tipo foi inferido da linha anterior
```

A palavra **var** antes do nome indica que podemos mudar o nome para outra String, ou seja é uma variável e o código a seguir é válido:

```

var nome = "Fábio"
nome = "Zico"
```

Caso uma alteração não fosse permitida, deveríamos declarar uma constante usando **let.**

```

let nome = "Fábio"
nome = "Zico" // Não compila pois nome é constante
```

## Trabalhando com classes

Por ser uma linguagem que segue o paradigma da Orientação de Objetos, saber a definir uma classe é imprescindível.

No Swift, diferentemente do Objective-C que usa um arquivo de definições(.h) e outro de implementações(.m), usa-se um único arquivo(.swift) para a definição de uma classe. Veja abaixo o exemplo: _Veiculo.swift_

```

class Veiculo{
   var ano: Int 

}
```

Com o código anterior, definimos um Veiculo que possui um atributo _ano_.

Mas da maneira como declaramos algumas informações ainda são necessárias para a classe compilar:

1)Declaração de construtor; 2)Inicialização de atributos;

_Veiculo.swift_

```

class Veiculo{
   var ano: Int
   var valor: Double = 20_000.0

   //construtor
   init(){
      self.ano = 2014
   }
}
```

Perceba que o atributo ano foi inciado no construtor e o atributor valor, novo atributo, foi inicializado fora. Sem problemas, podemos iniciar das duas formas. Essa exigência de iniciar o valor acontece pois não existe um valor default para os tipos. Outro ponto a ser notado é na declaração do valor que foi usado underscore para uma melhor legibilidade. Nada impede também de usar o literal como de costume: 20000.0;

## Modificadores de acesso

Talvez você tenha percebido que estamos acessando direto o atributo, e essa falta de encapsulamento não é mais necessária com a chegada dos modificadores de acesso a partir do Xcode 6 beta 4.

Existem 3 modificadores por ordem de menos restritivo ao mais restritivo: **public**, **internal** e **private**. Sendo que o default é ser **internal** ao módulo/framework ou app/projeto.

```

class Veiculo{
    private var ano: Int
    private var valor: Double = 20_000.0

    //construtor
    init(){
        self.ano = 2014
    }
}
```

## Instanciando Objetos

Definido os princípios básicos do use de classe no Swift, vemos a necessidade de instanciação dos objetos.

```

var fusca = Veiculo()  //ou var fusca: Veiculo = Veiculo()
```

Ao contrário do que era feito no Objective-c, com duas chamadas de método(alloc e init), aqui usa-se apenas o nome do tipo e parênteses.

## Definindo comportamentos

Uma classe também possui comportamentos e damos essas características através dos métodos que no Swift são declarados como funções(é possível ter funções sem uso de classes).

```

class Veiculo{
   private var ano: Int
   private var valor: Double = 20_000.0
   
   //… construtor omitido
   
   func calculaIPVA() -> Double{
      return self.valor * 0.05 
   }
}
```

No exemplo anterior, foi definido um método cujo nome é calculaIPVA e retorna um Double além de não receber nenhum parâmetro. O seu uso se daria da seguinte forma:

```

 var fusca = Veiculo()
 var valorDoIpva = fusca.calculaIPVA()
```

Em métodos que possuem parâmetros teremos a sintaxe a seguir:

```
   
class Veiculo{
   private var ano: Int
   private var valor: Double = 20_000.0
   //… construtor omitido
   func calculaIPVA() -> Double{
      return self.valor * 0.05
   }
   func calculaDepreciacaoParaAno(ano: Int, comTaxa taxaDeDepreciacao: Double)->                                                       Double{ 
   //lógica omitida   
  } 
}
```

Perceba que no método **calculaDepreciacaoParaAno** temos dois parâmetros e no que se refere a taxa foi usado o conceito de _External Parameter Name_, para que fique mais descritivo. Não é obrigatório nomear o parâmetro como fizemos ao declarar o **comTaxa**, mas é um recomendação para uma melhoria da legibilidade, principalmente com métodos que recebem vários parâmetros. O uso desse método ficaria como a seguir:

```

var fusca = Veiculo()
var valorDoIpva = fusca.calculaIPVA()
fusca.calculaDepreciacaoParaAno(2020, comTaxa: 0.12)
```

### Explorando o Swift

Para os testes anteriores é necessário ter o [Xcode 6](https://developer.apple.com/xcode/downloads/ "Xcode 6").

Utilize um projeto Playground, que como o próprio nome diz é o seu ambiente de diversão/exploração da linguagem e permite uma interatividade com aparência de linguagem de script, embora Swift seja compilado.

### Swift e Objective-C

É importante saber que a comunicação entre Swift e Objective-C é bem transparente e de certa forma até intuitiva. De forma que pode-se usar uma aplicação com as duas linguagens nessa fase transitória. A comunicação não é o foco desse artigo, mas se ficou curioso sugiro que acesse essa documentação : [Swift and Objective-C in the Same Project](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/BuildingCocoaApps/MixandMatch.html "documentação")

### Conclusão

Uma nova porta está aberta aos desenvolvedores que ainda não entraram no mundo iOS. Swift tem tudo para se tornar em um futuro próximo a linguagem dominante no mercado Apple. Para um aprofundamento nos estudos acesse a documentação oficial da linguagem: [Swift Programming Language](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html#//apple_ref/doc/uid/TP40014097-CH3-XID_0 "Swift Programming Language").
