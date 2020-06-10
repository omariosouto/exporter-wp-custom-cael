---
title: "DSLs não são para gerentes"
date: "2008-12-30"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já vi e ouvi de muitas pessoas e em muitos lugares que **Domain Specific Languages** são uma ótima ferramenta para deixar o código tão simples de escrever, tão legível e tão parecido com uma linguagem natural (português, inglês), que serve para que _não programadores_ possam escrever parte do código.

A idéia é que o próprio gerente, cliente, analista-não-programador, ou alguém com este tipo de perfil não técnico escreva as regras de negócio em uma linguagem muito parecida com a linguagem natural, [_eliminando a necessidade_ de programadores](http://martinfowler.com/articles/languageWorkbench.html#InvolvingNon-programmers) e _reduzindo_ os custos.

Este pode até ser um uso possível e [interessante](http://bnl.jayfields.com/) para DSLs, mas infelizmente, DSLs não tem a mínima pretenção de serem linguagens naturais. Isso seria muito [ambicioso](http://www.infoq.com/news/2008/03/dsls-are-not-natural-languages) para o escopo de uma línguagem [específica para um domínio](http://pragdave.blogs.pragprog.com/pragdave/2008/03/the-language-in.html). DSLs **não foram criadas para ensinar inglês** ou português aos computadores.

Existe uma outra forma interessante de usar DSLs, pela qual tenho preferência declarada e felizmente, parece haver uma convergência para esse meio de aplicá-las. O objetivo é fazer com que o código fonte do programa fique mais próximo do problema sendo resolvido. DSLs como forma de aumentar a expressividade do código. Dave Thomas e Andy Hunt tratam desse assunto no famoso livro [_The Pragmatic Programmer_](http://www.pragprog.com/the-pragmatic-programmer/extracts/tips), sob o nome de [**Program Close to the Problem Domain**](http://www.artima.com/intv/domain.html).

Em seu livro [Domain Driven Design](http://domaindrivendesign.org/books/#DDD), Eric Evans fala muito sobre a importância de todos os envolvidos no desenvolvimento do sistema usarem a mesma linguagem, que ele chama de **linguagem ubíqua** ([_Ubiquitous Language_](http://domaindrivendesign.org/discussion/messageboardarchive/UbiquitousLanguage.html)). Desta forma, conseguimos diminuir o abismo que existe entre programadores e especialistas no negócio, facilitando comunicação e diminuindo os clássicos problemas do _"telefone sem fio"_.

\[caption id="attachment\_467" align="aligncenter" width="480" caption="problemas de comunicação"\][![problemas de comunicação](https://blog.caelum.com.br/wp-content/uploads/2008/12/software_engineering_explained.gif "Engenharia de Software")](https://blog.caelum.com.br/wp-content/uploads/2008/12/software_engineering_explained.gif)\[/caption\]

DSLs são uma ferramenta para o desenvolvedor. Uma das formas de inserir os termos da linguagem ubíqua no código fonte, de fazer o código refletir o problema que está sendo resolvido. Desta forma, programadores podem fazer com que o código fique auto explicativo e auto documentado. O próprio código se explica; é a documentação de si próprio.

Ando tão "viciado" nessa forma de escrever código, perseguindo expressividade, fazendo com que o próprio código se explique, que em muitos casos a DSL surge naturalmente depois de algumas refatorações. Aqui, [Behavior Driven Development](http://dannorth.net/introducing-bdd/) (e principalmente o ciclo _red-green-refactor_) tem ajudado muito, mas esse assunto fica para um próximo post.

Uma técnica simples para aumentar a expressividade é evitar escrever comentários no meio do código. O tradicional ["comentário é mal cheiro no código"](http://memeagora.blogspot.com/2008/11/comments-code-smell.html) (_code smell_).

Ao invés de escrever o comentário, simplesmente extraia o código que estaria sendo comentado em um novo método. O nome deste novo método deve ser **exatamente** o mesmo que iria ser escrito no comentário.

> "You've written the code, now you have to write about the code. In a perfect world, you'd never have to write comments for this purpose: the code will be expressive enough that someone who reads it will understand it. Two things help achieve this result: expressive, readable languages and the composed method pattern." -- Neal Ford

[Neal Ford](http://www.nealford.com/) diz que linguagens expressivas e legíveis ajudam a eliminar a necessidade de explicar o código. Geralmente, linguagens mais modernas são mais expressivas do que as mais antigas como _Assembly_. Mesmo usando linguagens mais modernas, ainda podemos usar nossa ferramenta de programadores preferida: DSLs para criar a linguagem com a expressividade adequada para resolver o problema!

Essa técnica tem o seu preço, claro. É sempre uma troca, uma questão de vantagens e desvantagens; clássico _trade-off_. Projetar uma DSL pode dar mais trabalho no início, já que precisamos pensar em como desejamos escrever o código ([Language Oriented Programming](http://www.infoq.com/news/2007/09/Language-oriented-programming)) e pode exigir alguns ciclos extras de refatoração até o código chegar num nível bom de expressividade.

O que tenho percebido é que esse possível esforço extra no desenvolvimento (nem está comprovado cientificamente que ele existe [\[1\]](#f1)), costuma compensar mais para a frente, dada a facilidade de ler e entender o código, associado ao fato dos programadores estarem naturalmente o tempo todo usando a linguagem ubíqua.

Além disso, diminui bastante a necessidade da famosa pilha de documentação extra que precisa ser escrita em muitos projetos. Na maior parte deles isso tudo é até escrito quando o software já está pronto! Não estou querendo dizer que manuais não devam ser escritos, mas boa parte desse esforço extra de documentação pode ser reduzido. Principalmente o esforço relacionado a documentação que tem como alvo outros desenvolvedores e pessoas que possivelmente darão manutenção no código.

Assim como os testes durante o desenvolvimento, podemos encarar o uso de DSLs como um **investimento**, e não tempo extra no desenvolvimento.

Feliz ano novo a todos!

* * *

[1.](#extra) eu até acho que fico mais produtivo escrevendo código desse jeito.
