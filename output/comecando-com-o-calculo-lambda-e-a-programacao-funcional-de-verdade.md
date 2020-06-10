---
title: "Começando com o cálculo lambda e a programação funcional"
date: "2011-04-18"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![](https://blog.caelum.com.br/wp-content/uploads/2011/04/sicp-207x300.jpg "sicp")](https://blog.caelum.com.br/wp-content/uploads/2011/04/sicp.jpg) Na Caelum há muitos interessados e entusiastas da programação funcional pura, e resolvi ler o [Structure and Interpretation of Computer Programs](http://mitpress.mit.edu/sicp/), clássico curso introdutório do MIT, por indicação do Rafael Ferreira, Renato Lucindo, Pedro Matiello e outros amigos. O livro é muito interessante para quem não conhece nenhum dialeto LISP, como eu. Mesmo se você conheça bem uma linguagem mais funcional como Ruby, ou esteja entrando em Scala, Clojure, Fantom, CAL e outras funcionais que rodam na JVM, vai aprender muito a como pensar diferente, e [não cair na armadilha](https://blog.caelum.com.br/modelando-as-classes-do-fj-16-em-scala/) de usar esse paradigma como um simples truque para avaliar predicados em coleções.

O livro fala por diversas vezes, mas sempre en passant, do [lambda calculus](http://en.wikipedia.org/wiki/Lambda_calculus). Parei um pouco a leitura para [estudar mais da formalidade](http://www.inf.fu-berlin.de/lehre/WS03/alpi/lambda.pdf) criada por Alonso Church (orientador do Turing!) e do que é possível fazer apenas com funções, base para [a criação do Lisp](http://www-formal.stanford.edu/jmc/recursive.html) por [John McCarthy](http://www-formal.stanford.edu/jmc/) na década de 60. Aliás, tudo é possível, já que o [cálculo lambda é Turing complete](http://en.wikipedia.org/wiki/Turing_completeness).

O símbolo `λ` definie uma função que recebe alguns parâmetros, e o símbolo `.` separa os parâmetros do resultado. Por exemplo, `λx.x` é a função identidade `f(x)=x`. Aplicando `λx.x` no número `5` obtemos `(λx.x)5` (também escrito como `λx.x 5`), que resulta em substituir `x`, retornando `5`. Trivial? Por enquanto.

A função que retorna o quadrado de um número seria representada por `λx.x*x`. Mas aqui usamos o símbolo `*`, que ainda não foi definido em nossa linguagem. Deveríamos ter uma função multiplicadora que recebe dois argumentos, chamada `m`, e a função quadrado ficaria `λx.mxx`, isso é, `f(x)=m(x,x)`. Como implementar a multiplicação? Usando o símbolo da soma, mas aí fica o problema de escrever o `+` funcionalmente... mas há até como representar isso apenas com funções, sem depender de nenhuma operação básica da matemática.

Vamos começar com algo mais simples, definindo duas funções que representam `True` e `False`:

\[code\] T ≡ λab.a F ≡ λab.b \[/code\]

Isso é, `T` é uma função que recebe dois parâmetros, e retorna o primeiro deles. Analogamente `F` retorna o segundo. Na verdade, todo lambda recebe apenas um único parâmetro, escreveríamos então que `T` é um lambda que recebe `a` e devolve um novo lambda que recebe `b` e que devolve sempre `a`, isto é, `T ≡ λa.λb.a`, que é a versão curried de `λab.a` e tem o mesmo resultado. Não há uma memória utilizada como habitual: os valores `a` e `b` estarão fechados dentro do contexto do primeiro e do segundo lambda.

Pode parecer difícil a princípio, mas é isso mesmo: verdadeiro e falso podem ser representados como funções. Podemos então "traduzir" um booleano nesse formato aplicando-o ao par de `String` `"verdadeiro", "falso"`. [Em ruby](https://gist.github.com/673304), conforme sugestão do Leonardo Bessa, teríamos:

\[ruby\] T = lambda { |a,b| a } F = lambda { |a,b| b } display = lambda { |boolean| boolean\['verdadeiro','falso'\]} \[/ruby\]

Já conseguimos sinalizar se um booleano é falso ou verdadeiro apenas com funções (`display[T]`, por exemplo). Mas isso é muito trivial, como ficam os operadores booleanos? Iniciando pela negação, ela pode ser definida da seguinte forma:

\[code\] NOT ≡ λx.xFT \[/code\]

`NOT` é uma função que recebe um booleano, e devolve a **aplicação** desse booleano nos parâmetros `F` e `T`. Isso é possível pois aqui **um booleano é uma função**, e passamos essa função por parâmetro. Em código:

\[ruby\] NOT = lambda{ |x| x\[F,T\] } \[/ruby\]

O `AND` e o `OR` lógico vão receber dois parâmetros, e vão seguir um raciocínio similar. No caso do `AND`, pegamos o primeiro booleano, se ele for verdadeiro, depende do segundo elemento (devolvemos ele então!), caso ele seja falso, retornamos falso diretamente. \[code\] AND ≡ λab.abF \[/code\]

O `AND` recebe dois booleanos e aplica o primeiro (já que booleanos são funções) em `b` e `F`, pois, se `a` for a função `T`, devolverá `b`, caso seja `F`, devolverá o segundo argumento, que é `F`, como desejamos. O `OR` é análogo, devolvendo T se o primeiro parâmetro é verdadeiro, e devolvendo o segudo parâmetro caso contrário: `OR ≡ λab.aTb`. Em Ruby teremos:

\[ruby\] AND = lambda{ |a,b| a\[b,F\] } OR = lambda{ |a,b| a\[T,b\] } \[/ruby\]

E em [Scheme](http://racket-lang.org/), o dialeto LISP criado por Guy Steele (e um dos criadores também do... Java!), ficamos mais próximo ainda da sintaxe do cálculo lambda:

\[clojure\] (define T (lambda (a b) a)) (define F (lambda (a b) b)) (define NOT (lambda (x) (x F T))) (define AND (lambda (b1 b2) (b1 b2 F))) (define OR (lambda (b1 b2) (b1 T b2))) \[/clojure\] Quer um desafio maior? Os mais sucintos dialetos LISP trabalham sempre em cima de apenas uma estrutura de dados: a lista. E essa lista, por sua vez, é manipulada por três primitivas básicas: a construção de um par (A,B) (`pair` ou `cons`), a seleção do primeiro elemento de um par (`head` ou `car`), e a seleção do segundo elemento de um par (`tail` ou `cdr`). De tal maneira que `head(pair(a,b))` resulte em `a` e `tail(pair(a,b))` resulte em `b`.

Como implementar essas três primitivas? Você vai ver que, sem usar estrutura de dados, porém com auxílio de ifs, já dá algum trabalho. É um bom exercício. Mas há como implementar sem nem ifs, nem estruturas! E não é tão complicado, basta que a primeiriva `pair`, dado os dois elementos a e b, guarde-os. Como guardar se não temos estrutura de dados? Devolvemos um lambda que por sua vez recebe um novo lambda que será aplicado nos dois argumentos. As primitivas de `head` e `tail` vão então aplicar esse lambda, que representa um par, passando uma função que, dado dois argumentos, devolve o primeiro ou último elemento.

\[code\] pair ≡ λab.λf.fab head ≡ λp.p(λab.a) tail ≡ λp.p(λab.b) \[/code\]

Isto é, dado dois elementos a `pair`, por exemplo 1 e 2, receberemos de volta uma função que por sua vez recebe outra função `f` e aplica-a nos argumentos anteriores (`λf.fab`). Aplicando `head`, por exemplo, em um par, o lambda `λab.a` será recebido como `f` e aplicado sobre os argumentos originais. Papel e caneta são bons nessas horas para fazer essa expansão passo a passo.

Repare que poderíamos escrever `head` e `tail` utilizando das funções `T` e `F` previamente criadas, passando-as como argumento à função par em questão, como por exemplo `head ≡ λp.pT`.

[A solução em Scheme](https://github.com/peas/sicp/blob/master/src/lambda-listas.sch) é bastante direta. Fica para você implementar esse código em ruby. É muito mais simples do que você imagina, e fará você compreender melhor a notação lambda. [O Renato Lucindo possui uma versão em javascript](http://blog.lucindo.com.br/2007/08/06/lambda-the-ultime-glue/).

Pode não ficar claro num primeiro instante, mas depois de escrever o código você terá um desses raros e importantes momentos "wow" da programação. Há uma outra representação funcional impressionante: o [encoding de Church](http://en.wikipedia.org/wiki/Church_numeral) para números naturais, possibilitando escrever os números e operações aritméticas [apenas com lambdas](https://github.com/peas/sicp/blob/master/src/lambda-aritimetica.sch). Se [isso é útil](http://jim-mcbeath.blogspot.com/2009/09/type-safe-builder-in-scala-using-church.html)? É um passo importante para entender que programação funcional é muito mais que passar funções como argumentos, entender a importância das primitivas como o `fold-right`, [compondo-as](https://github.com/peas/sicp/blob/master/src/capitulo2-sequencias.sch) e enxergar que as possibilidades são grandes e o código mais sucinto, sem efeitos colaterais. Quando programamos em ruby, python ou scala, podemos aproveitar muito mais da linguagem com essas abstrações em mente.

Terminando com [uma citação do Guy Steele](http://www.paulgraham.com/quotes.html), sobre a motivação de ter criado o Java, já que já havia criado o Scheme: "_Nós não estavamos querendo ganhar os programadores LISP, estávamos atrás dos programadores C++. (Com Java) conseguimos trazer muitos deles meio caminho em direção ao LISP._"

Agradecimentos a revisão do [Ricardo Herrmann](http://www.ime.usp.br/~herrmann/).
