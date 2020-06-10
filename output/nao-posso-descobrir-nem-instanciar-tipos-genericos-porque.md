---
title: "Não posso descobrir nem instanciar tipos genéricos! Porque?"
date: "2008-04-28"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

São incontáveis os posts no GUJ com uma pergunta semelhante: "[Posso extrair o nome de um tipo genérico?](http://guj.com.br/java/87606-extrair-nome-do-tipo-generico)", "[Não consigo extrair tipo do genérico!](http://guj.com.br/java/87532-generics---nao-consigo-instanciar-o-tipo)", "[Utilizando generics para instanciar objetos](http://www.guj.com.br/java/82372-utilizando-o-generics-para-instanciar-objetos)", entre outros. Curioso que esse tipo de pergunta tem aumentado muito nos últimos tempos, identificando um possível crescimento no uso do Java 5 em diante. Já era a hora!

Eu já havia postado sobre isso quando falei de [reificação de tipos parametrizados](https://blog.caelum.com.br/2007/04/08/generics-inferencia-de-tipos-e-reificacao-no-java-7/), mas de uma maneira mais geral.

A questão básica é a seguinte: Se eu tenho um tipo genérico, que recebe um tipo parametrizado `T` como argumento, eu posso instanciar `T` de alguma forma?

Quem sabe tentar assim:

\[java\] class Dao<T> { public T cria() { return new T(); } } \[/java\] Essa forma não funciona. Você não tem garantias sobre os construtores que o tipo `T` possui. E então se tentarmos assim:

\[java\] class Dao<T> { public T cria() { return T.class.newInstance(); } } \[/java\] Aqui a sintaxe até poderia ser possível, mas infelizmente o java não sabe quem é `T` nem mesmo em tempo de execução. Nem mesmo com manipulação de bytecode ou qualquer outro recurso. Isso porque o compilador "apaga" essa informação depois de utilizada: é a tal da mal falada **[erasure](http://download.oracle.com/javase/tutorial/java/generics/erasure.html)**.

**Qual seria a vantagem da erasure?**

Antes de mais nada: erasure não serve para poder rodar código do Java 5 em VMs Java 1.4 ou menor! Não é esse o objetivo.

Quando a JSR14 do generics foi proposta, eles queriam mais que compatibilidade para trás em relação a compilação, eles queriam também a possibilidade de migrar o código antigo para poder usar código novo: uma `ArrayList` precisa poder ser passada como argumento para alguém que receba `List<QualquerClasse>` como argumento!

Para ilustrar a situação, imagine que eu tenho uma aplicação grande **X** que usasse a classe `ArrayList` em muitos lugares (como a grande maioria das aplicações). Essa aplicação **X** usa a biblioteca **B**, que recebe `List` como argumento em muitos de seus métodos. Se um dia a bibioteca **B** passasse a usar `List` genérica, gostaríamos que a aplicação **X** atualizasse **B** sem maiores problemas: sem precisar recompilar nada, nem trocar nada no código fonte. O [Neal Gafter escreveu muito sobre isso](http://gafter.blogspot.com/2004/09/puzzling-through-erasure-answer.html) pouco antes da release do Java 5, dada as inúmeras críticas que eles estavam recebendo.

Se o Java tivesse optado por outra maneira de implementar generics, teria ou quebrado compatibilidade com o uso não genérico de classes que viraram genéricas, ou precisaria criar classes paralelas as atuais, praticamente copiadas e coladas, só que estas genéricas, tornando as antigas deprecated ou legadas.

**Como o .NET resolveu o mesmo problema?**

O .NET seguiu essa segunda forma, e criou uma hierarquia quase que paralela de coleções dentro do name space `System.Collections.Generic`.

A [IList](http://msdn2.microsoft.com/en-us/library/system.collections.ilist.aspx) é a interface que define as operações em uma lista não genérica, e a [ArrayList](http://msdn2.microsoft.com/en-us/library/system.collections.arraylist.aspx) é sua implementação mais comumente usada. Quando entrou generics no .NET eles criaram uma outra interface para a lista, com mesmo nome, só que genérica: a [IList<T>](http://msdn2.microsoft.com/en-us/library/5y536ey6.aspx).

A classe [List](http://msdn2.microsoft.com/en-us/library/6sh2ey19.aspx) já é a implementação da interface genérica, e é ela quem você vai usar em vez de `ArrayList`. Ela possui uma definição bem estranha:

\[csharp\] public class List : IList, ICollection, IEnumerable, IList, ICollection, IEnumerable \[/csharp\]

Ela implementa tanto a lista genérica como a não genérica. O .NET tem um recurso que nós não temos, que faz com que apesar dessa lista também implementar a interface não genérica, você só consegue invocar os métodos não genéricos (que trabalham com `Object`) se estiver se referenciando a ela explicitamente como uma lista não genérica, como o código abaixo:

\[csharp\] IList x = new List<String>(); x.Add(2); \[/csharp\]

Apesar desse código compilar, no .NET temos essa informação dos tipos genéricos em tempo de execução, o que fará gerar uma exceção:

`System.ArgumentException: The value "2" is not of type "System.String" e cannot be used in this generic collection`

No Java teríamos apenas um _unchecked warning_ na linha da declaração da referência, e uma possível `ClassCastException` mais a frente no código.

Aqui a vantagem é você poder passar uma `List` genérica para um código .NET antigo, que recebe como argumento uma `IList` não genérica. Além disso, no .NET você pode sim descobrir quem é `T` em tempo de execução:

\[csharp\] class ClasseGenerica<T> { void metodo() { Console.WriteLine(typeof(T)); } } \[/csharp\] **Em resumo**: o sistema de generics do .NET é realmente seguro, não temos como burlá-lo através de _unchecked casts_, como ocorre em Java. O Java novamente sacrificou alguns recursos interessantes em favor a compatibilidade de versões e interoperabilidade entre classes genéricas e as não genéricas já existentes. Cada um com sua vantagem. Como citei no outro artigo, existem algumas idéias de [dar suporte a tipos genéricos reificados no Java](http://gafter.blogspot.com/2006/11/reified-generics-for-java.html), ao mesmo tempo que outros ficariam ainda com a erasure, sendo que você pode escolher qual o que te agrada para aquela classe genérica em particular. Talvez ter as duas opções adicione ainda mais complexidade a tipagem genérica do Java, mas eu particularmente gosto da idéia.

Mais uma vez um post que era para ser sucinto ficou longo. Agradeço ao Rafael Steil e Rodrigo Kumpera pela colaboração, e ao Lucas Cavalcanti e Guilherme Moreira pedindo para que fosse elaborado um post mais completo sobre esse assunto tão pertinente.
