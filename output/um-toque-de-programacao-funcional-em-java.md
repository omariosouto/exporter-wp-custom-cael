---
title: "Um toque de programação funcional em Java"
date: "2011-02-22"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![](https://blog.caelum.com.br/wp-content/uploads/2011/02/java-closures.png) Com [as closures do Java 8](https://blog.caelum.com.br/trabalhando-com-closures-no-java-8/) previstas apenas para meados de 2012, surgem outras possibilidades para trabalhar um pouco mais funcionalmente. Enquanto isso não acontece, como podemos fazer para que os conceitos de programação funcional ajudem a escrever o nosso código?

Consideremos o caso de calcular a média ponderada de uma `List<Prova>`, onde Prova tem `nota` e `peso`. Em java teríamos um laço como:

\[java\] public double mediaPonderada(List<Prova> provas) { double somaNotas = 0.0; double somaPesos = 0.0; for (Prova prova : provas) { somaNotas += prova.getNota() \* prova.getPeso(); somaPesos += prova.getPeso(); } return somaNotas / somaPesos; } \[/java\]

Mas gostaríamos de algo mais genérico, para poder tirar média ponderada de qualquer objeto. A abordagem padrão em java é extrair uma interface com os getters para o valor da nota e para o peso, e fazer com que `Prova` implemente essa interface:

\[java\] public interface Ponderavel { double getValor(); double getPeso(); } public class Prova implements Ponderavel {...} \[/java\]

Assim a implementação do `mediaPonderada` ficaria:

\[java\] public double mediaPonderada(List<Ponderavel> ponderaveis) { double soma = 0.0; double somaPesos = 0.0; for (Ponderavel ponderavel : ponderaveis) { soma += ponderavel.getValor() \* ponderavel.getPeso(); somaPesos += ponderavel.getPeso(); } return soma / somaPesos; } \[/java\]

E o que acontece se não pudermos (ou não quisermos) implementar a interface só pra chamar o método `mediaPonderada`? Teríamos que usar, então, a mesma abordagem que o [Collections.sort](http://download.oracle.com/javase/1.5.0/docs/api/java/util/Collections.html#sort(java.util.List, java.util.Comparator)), e criar uma outra interface para poder calcular a média ponderada de uma lista qualquer:

\[java\] public interface Ponderante<T> { double valorDe(T t); double pesoDe(T t); } \[/java\]

assim a chamada do método ficaria: \[java\] List<Prova> provas = ...; double media = mediaPonderada(provas, new Ponderante<Prova>() { public double valorDe(Prova prova) { return prova.getNota(); } public double pesoDe(Prova prova) { return prova.getPeso(); } }); \[/java\]

Isso resolve o problema de uma forma geral, mas prejudica bastante a leitura da chamada do método com a criação da classe anônima. Para quem já programou em [Scala](https://blog.caelum.com.br/scala-sua-proxima-linguagem/) ou em alguma outra linguagem funcional, a abordagem natural seria passar os métodos a serem chamados nos objetos da lista como argumentos do método `mediaPonderada`. Por exemplo, o código em Scala seria:

\[scala\] var media = mediaPonderada(provas, \_.nota, \_.peso) ... \[/scala\]

Nesse código chamaremos os métodos nota e peso (os equivalentes aos getters de java) em cada elemento da lista `provas`, usando-os como valor e peso. Mas será que conseguimos fazer algo parecido com isso no java que temos hoje em dia? A resposta é **sim**, basta deixarmos de lado alguns dos nossos preconceitos e usarmos a criatividade.

A primeira coisa que precisamos é receber os métodos que serem chamados como parâmetro do `mediaPonderada`. Poderíamos usar o `java.lang.Method` para isso, mas para evitar a grande quantidade de exceptions que deveriam ser tratadas e a programação orientada a string, usaremos a [Function](http://guava-libraries.googlecode.com/svn/tags/release08/javadoc/com/google/common/base/Function.html) do [Google Guava](http://guava-libraries.googlecode.com/):

\[java\] public <T> double mediaPonderada(List<T> lista, Function<T, Double> valor, Function<T, Double> peso) { double soma = 0.0; double somaPesos = 0.0; for (T t : lista) { soma += valor.apply(t) \* peso.apply(t); somaPesos += peso.apply(t); } return soma / somaPesos; } \[/java\]

A princípio a chamada do método não melhorou muito:

\[java\] double media = mediaPonderada(provas, new Function<Prova, Double>() { public Double apply(Prova p) { return p.getNota(); } }, new Function<Prova, Double>() { public Double apply(Prova p) { return p.getPeso(); } }); \[/java\]

Agora precisamos criar uma forma de capturar uma chamada de método e transformá-la numa `Function`. Para capturar a chamada, podemos usar um proxy, que é uma classe filha de uma interface ou classe em que podemos controlar o seu comportamento através de um `MethodInterceptor`, que intercepta todas as chamadas de métodos. O [VRaptor](http://vraptor.caelum.com.br) já possui um criador de proxies pronto, o [ObjenesisProxifier](https://github.com/caelum/vraptor/blob/master/vraptor-core/src/main/java/br/com/caelum/vraptor/proxy/ObjenesisProxifier.java), que torna bem simples o processo de criar um proxy.

Vamos, então, criar uma classe e um método para poder criar esse proxy facilmente e guardar o método capturado num um pouco deselagante `ThreadLocal` (para possibilitar uma maior flexibilidade, deveríamos guardar uma pilha de métodos e argumentos):

\[java\] public class Funcional { private static final ThreadLocal<Method> method = new ThreadLocal<Method>(); private static final ThreadLocal<Object\[\]> args = new ThreadLocal<Object\[\]>(); public static <T> T of(Class<T> type) { return new ObjenesisProxifier().proxify(type, new MethodInvocation<T>() { public Object intercept(T proxy, Method method, Object\[\] args, SuperMethod superMethod) { Funcional.method.set(method); Funcional.args.set(args); return null; } }); } } \[/java\]

Estamos usando métodos estáticos para poder usar o import static e deixar o código um pouco mais legível, e precisamos do `ThreadLocal` para podermos armazenar o método chamado sem se preocupar com problemas de concorrência. Agora precisamos de um outro método que transforma o método capturado numa `Function`:

\[java\] public static <T,F> Function<T,F> function() { final Method method = method.get(); final Object\[\] args = args.get(); return new Function<T,F>() { public T apply(F f) { return (T) new Mirror().on(f).invoke() .method(method).withArgs(args); } }; } \[/java\] Usamos aqui o [Mirror](http://projetos.vidageek.net/mirror/), que permite invocar o método via reflexão envelopando as checked exceptions da API de reflection em exceções de runtime. Assim, para conseguir criar a `Function` só precisaríamos chamar o método `of` antes:

\[java\] of(Prova.class).getNota(); Function<Prova, Double> getNota = function(); of(Prova.class).getPeso(); Function<Prova, Double> getPeso = function(); double media = mediaPonderada(provas, getNota, getPeso); \[/java\]

E se tirarmos vantagem da ordem de chamada dos métodos em java podemos fazer com que o método function receba um parâmetro, que será ignorado:

\[java\] public <F,T> Function<F,T> function(T ignorado) {...} \[/java\]

Assim podemos invocar:

\[java\] Function<Prova, Double> getNota = function(of(Prova.class).getNota()); Function<Prova, Double> getPeso = function(of(Prova.class).getPeso()); double media = mediaPonderada(provas, getNota, getPeso); \[/java\]

Ou ainda (com um pequeno truque por causa da inferência de tipos genéricos do java):

\[java\] double media = mediaPonderada(provas, function(of(Prova.class).getNota()), function(of(Prova.class).getPeso())); ... public <T> Function<Prova,T> function(T ignorado) { return Funcional.function(ignorado); } \[/java\]

Extraindo o `of(Prova.class)` para uma constante chamada `_`, chegamos em algo bem próximo à sintaxe do Scala:

\[java\] private static Prova \_ = of(Prova.class); ... double media = mediaPonderada (provas, function( \_.getNota()), function( \_.getPeso())); ... \[/java\]

Aqui trocamos a complexidade da implementação - e até um pouco de performance - pela legibilidade e extensibilidade do uso desse código (poderíamos usar essa function em [várias das classes do Guava](http://guava-libraries.googlecode.com/svn/tags/release08/javadoc/com/google/common/collect/package-frame.html), por exemplo), mesmo abusando de recursos e estruturas polêmicas - como `ThreadLocal` e métodos estáticos. E essa é uma abordagem que vem sendo usada em várias bibliotecas, como [JUnit](http://junit.org/), [Hamcrest](http://code.google.com/p/hamcrest/) e [Mockito](http://mockito.org/). É importante notar também que muitas das idéias desse código final vieram do Scala, e de outras linguagens funcionais, reforçando a importância de [aprender várias linguagens de programação](http://www.programadorpoliglota.com.br/).

Esse é um exemplo do malabarismo que podemos fazer com java e proxies, e o resultado ainda é um pouco difícil de ler. A abordagem do Java 8 vai ajudar a divulgar essas técnicas, com uma sintaxe muito mais adequada. O código completo [pode ser visualizado aqui](https://gist.github.com/837684). Para quem gostou desse tipo de manipulação, existe o projeto [LambdaJ](http://code.google.com/p/lambdaj/) que adiciona várias características funcionais ao java, deixando alguns códigos, como o que vimos acima, mais concisos e legíveis.
