---
title: "Domain Specific Languages em ação"
date: "2007-09-21"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em diversos momentos sentimos a necessidade de utilizar uma linguagem para atacar um problema mais específico. Utilizar Java ou C# nesse tipo de problema pode gerar uma enorme quantidade desnecessária de código. Veja um exemplo que passamos na Caelum:

\[java\] Set<Strategy> strategies = new HashSet<Strategy>(); Indicator<Double> close = new ClosePriceIndicator(timeSeries); for (int i = 1; i <= 50; i++) { Indicator<Double> tracker = new EMAIndicator(close, i); Strategy strategy = new IndicatorCrossedIndicatorStrategy(close, tracker); strategies.add(strategy); } \[/java\]

No nosso caso, esse trecho de código deve ser compreensível para analistas de negócio, que não são necessariamente programadores, muito menos possuem conhecimento de Java. [Domain Specific Languages](http://martinfowler.com/bliki/DomainSpecificLanguage.html) é o nome dado a prática de se criar pequenas linguagens para resolver um problemas bem específicos. Elas existem em dois sabores: as externas, que criam uma linguagem própria, e as internas, que na verdade utilizam um subconjunto de instruções de um linguagem já existente e utilizada no sistema.

Entre alguns clássicos exemplos de DSLs internas temos o uso da Criteria do hibernate, o uso do ruby nos arquivos de build do rake. Entre as DSL externas, temos as macros do excel e o xml do ant.

Utilizando [a api de scripting do java 6](https://scripting.dev.java.net/), passamos a usar ruby (através da [JRuby](http://www.jruby.org/)) para escrever essa parte da lógica de negócios, e nosso código em java ficou assim:

\[ruby\] (1..50).collect{|x| Tail::IndicatorCrossedIndicatorStrategy.new(close, Tail::EMAIndicator.new(close, x)) } \[/ruby\]

Criando algumas factories, conseguimos chegar a um código muito mais simples:

\[ruby\] (1..50).collect{|x| cross(close, ema(x)) } \[/ruby\]

Logo, estamos próximos de chegar a algo parecido com uma linguagem natural:

\[code\] x de 1 a 50 quando cruzar (fechamento, ema(x)) \[/code\]

Para tal, uma das possibilidades seria usar um dos [compiladores de compiladores](http://java-source.net/open-source/parser-generators) existentes para Java, porém isso daria muito trabalho.

O [Rodrigo Kumpera](http://www.kumpera.net/) sugeriu escrever o próprio parser, [como fez o Gilad Bracha no Small Talk](http://gbracha.blogspot.com/2007/01/parser-combinators.html). Tanto o Rodrigo quanto o [Renato Lucindo](http://blog.lucindo.com.br) citaram o [spirit++](http://spirit.sourceforge.net/), que faz isso para C++. Uma pena não existir algo equivalente para o Java.

[Phillip Calçado](http://fragmental.com.br/) recomendou fazer um teste para saber se devemos ou não melhorar ainda mais essa DSL. O teste consiste em colocar a linguagem natural ao lado da DSL ruby e ver se o especialista consegue fazer a ponte entre uma e outra. Exemplo:

\[ruby\] (1..50).collect{|x| cross(close, ema(x)) } \[/ruby\]

\[code\] x de 1 a 50 quando cruzar (fechamento, ema(x)) \[/code\]

Se o especialista no domínio não entender a semelhança entre os dois códigos, é necessário aprimorar a DSL em questão.
