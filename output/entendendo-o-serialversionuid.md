---
title: "Entendendo o serialVersionUID"
date: "2008-04-01"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O `serialVersionUID` é uma dúvida constante entre muitos desenvolvedores. Afinal, quando e para que exatamente usá-lo? Devo gerar um número aleatório bem grande, ou um número qualquer? Essas perguntas são comuns, e ao desenvolvedor experiente é necessário conhecer a fundo esse detalhe do [processo de serialização do Java](http://java.sun.com/javase/6/docs/platform/serialization/spec/serialTOC.html).

Quando um objeto é serializado no Java, essa sequência de bytes, além de conter seus atributos de instância não transientes, carrega consigo um número que indentifica a "versão" da classe que foi usada durante o processo. Esse é o chamado `serialVersionUID`, ou seja, o indentificador de versão de serialização de uma classe. Esse número é utilizado para saber se o objeto que estamos recuperando é de uma versão "compatível" com a versão da classe que foi utilizada quando serializamos o objeto: em outras palavras, os arquivos `.class` não precisam ser necessariamente os mesmos para que o processo de serialização ocorra com sucesso.

Por exemplo, considere a seguinte classe `Usuario`:

\[java\] package br.com.caelum;

public class Usuario implements Serializable { private String login; } \[/java\]

Essa classe possui o `serialVersionUID` igual a `2806421523585360625L`. **Esse número não é aleatório**! Ele é um hash (SHA) calculado em cima dos nomes dos seus atributos, e assinaturas dos métodos em uma ordem bem definida [pela especificação do processo de serialização](http://java.sun.com/javase/6/docs/platform/serialization/spec/class.html#4100). E como eu sei esse número? O JDK vem com a ferramenta `serialver`, que implementa esse mesmo hash:

`serialver br.com.caelum.Usuario`

Se o `serialVersionUID` utilizado durante a serialização não bater exatamente com o `serialVersionUID` da classe que está sendo usada para recuperar essa informação, uma exception é lançada: `java.io.InvalidClassException`.

Por exemplo, se adicionarmos um novo atributo na nossa classe `Usuario`:

\[java\] public class Usuario implements Serializable { private String login; private String senha; } \[/java\]

Agora teremos o `serialVersionUID` valendo `416295346730660862L`. Caso você serialize um `Usuario` com a primeira classe aqui definida, e tentar recuperar essa informação usando essa nova versão de classe, receberemos a conhecida `java.io.InvalidClassException`. Esse é o comportamente que em muitos casos queremos, mas algumas vezes fazemos pequenas modificações na classe as quais percebemos que não impactarão no processo de serialização, e precisamos manter compatibilidade com a versão antiga daquela classe. Para isso, basta definirmos explicitamente qual é o nosso `serialVersionUID`, e no caso de querer manter compatibilidade com a classe `Usuario` anterior, vamos utilizar o valor de `serialVersionUID` que seria gerado pela JVM: `2806421523585360625L`. O código ficaria:

\[java\] public class Usuario implements Serializable { private static final long serialVersionUID = 2806421523585360625L; private String login; private String senha; } \[/java\]

Às vezes recebemos um warning do Eclipse, e ele pede para que seja definido o `serialVersionUID` da classe em questão. Isso ocorre porque você implementa `Serializable` ou uma de suas mães a implementa. O Eclipse então te abre três opções: utilizar o `@SurpressWarnings` para você assumir o risco, usar um valor default, ou usar o valor gerado. O gerador de UIDs do Eclipse é exatamente o mesmo gerador utilizado pelo Java SE para criar os UIDs padrão! Reforçando, **esse número não é um número aleatório**!

Quando alguém esquece de manter o mesmo `serialVersionUID` para duas versões compatíveis de uma classe, podemos ter problemas em usar diferentes versões do software que são teoricamente compatíveis. Isso muitas vezes acontece em servidores de aplicação, e se seu cliente esta desatualizado em relação a versão dos jars necessários pelo servidor, podemos ter alguns `InvalidClassException`s que poderiam ser facilmente evitados se o `serialVersionUID` tivesse sido corretamente aplicado. Claro que algumas outras vezes as versões realmente não são compatíveis e a exception procede.

Esse grave problema pode acontecer mesmo usando classes do Java SE entre diferentes versões, como é o caso da classe `[java.text.AttributedCharacterIterator.Attribute](http://java.sun.com/j2se/1.4.2/docs/api/java/text/AttributedCharacterIterator.Attribute.html)` (utilizada pela `java.awt.Font`). Do Java 1.3 para o Java 1.4 essa classe foi levemente alterada, e o `serialVersionUID` [gerado pelo algoritmo da JVM desta classe mudou](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=4365406) de `-1514471214376796190L` para `-9142742483513960612L`. Quando alguém serializava uma `java.awt.Font` em uma versão não podia desserializa-la em outra, sendo que as versões tecnicamente são compatíveis: a não definição explícita do `serialVersionUID` gerou um bug no Java SE. Como isto foi resolvido? Definiram o `serialVersionUID` como `-1514471214376796190L`, que é o valor que seria gerado pela JVM na versão anterior da classe.

Como então devemos proceder para escolher um `serialVersionUID` apropriado? É muito simples: se essa classe está nascendo neste momento, você pode se dar ao luxo de utilizar um `serialVersionUID`, como por exemplo:

\[java\] public class Usuario implements Serializable { private static final long serialVersionUID = 1L; private String login; } \[/java\]

Porém se você está definindo o `serialVersionUID` de uma classe já em produção, e sabe que a mudança que está fazendo é compatível com a versão anterior, você deve utilizar o `serialVersionUID` que seria gerado pela JVM na primeira versão, como foi o caso aqui quando adicionamos o atributo `senha` na classe `Usuario`, e também foi o caso da correção do bug da classe `java.text.AttributedCharacterIterator.Attribute`. Quando você fizer uma alteração onde percebe que o cliente precisará de atualização das classes envolvidas, basta definir um `serialVersionUID` diferente dos anteriormente utilizados.

Para completar, implementar uma interface que não define métodos (`Serializable`) e ser forçado a escrever um atributo sem um contrato mais burocrático é um tanto estranho em uma linguagem como o Java. Sem dúvida, se esse mecanismo todo tivesse sido inventado já com a existência de anotações, `Serializable` seria uma anotação e `version` um atributo dela, talvez obrigatório, criando algo como `@Serializable(version=12345L)`. Boas serializações e invocações remotas!
