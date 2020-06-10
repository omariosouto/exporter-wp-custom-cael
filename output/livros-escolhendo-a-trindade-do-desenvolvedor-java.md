---
title: "Livros: escolhendo a trindade do desenvolvedor Java"
date: "2006-09-22"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muitas pessoas costumam me perguntar se o livro X é bom, se o livro Y cobre bem Java EE, assim por diante. Alguns desses livros são super específicos de uma determinada tecnologia Java, como por exemplo _Struts in Action_ que só aborda a versão 1.x.y do Struts. Em um curto espaço de tempo você vai acabar reciclando as folhas desses livros: ficarão desatualizados.

Na Caelum começamos a debater sobre quais livros importantes havíamos lidos e que ainda eram úteis, e queríamos muito que os outros que participavam da discussão também tivessem lido. A lista estava ficando muito grande quando resolvemos corta-la para apenas 3. Separamos então a trindade do desenvolvedor Java. O critério da escolha foi tentar achar uma intersecção dos livros considerados importantes por nós que todos saíssem satisfeitos. Repare que já são bem velhinhos:

- Refactoring, [Martin Fowler](http://www.martinfowler.com/)

Livro do cientista chefe da [ThoughtWorks](http://www.thoughtworks.com/), a famosa empresa onde nosso amigo [Carlos Villela](http://lixo.org) trabalha. Um excelente catálogo das gambiarras (_smells_, mal cheiro) que acabam aparecendo no seu código e como você pode fazer para conserta-las de maneira sensata. Ok, você já sabia disso. Exemplos clássicos são o uso de herança apenas por preguiça, uso do switch em vez de polimorfismo, entre milhares. Uma pena o código não usar a conveção da Sun e estar lotado de underscores...

- Effective Java, Joshua Bloch

Livro do ex bambambam da Sun (você pode encontrar o nome dele como `@author` das principais classes do Java SE), agora no Google. Aqui ele mostra como enfrentar os principais problemas e limitações da linguagem. Se você já programa a algum tempo sem dúvida alguma já passou por boa parte dos casos catalogados. Uma excelente leitura, além de simples pois cada caso é relatado em um texto bem curto. Entre os casos interessantes está o uso de _factory methods_, os problemas da herança e do `protected`, uso de coleções, objetos imutáveis e serialização.

- Design Patterns, [Erich Gamma](http://en.wikipedia.org/wiki/Erich_Gamma) et al

O clichê dos clichês não poderia estar fora da lista! Livro do atual líder do projeto Eclipse, entre outros. Compre! E ao contrário do que muitos fazem não saia lendo o catálogo dos patterns decorando-os, concentre-se em ler toda a primeira parte onde eles revelam duas regras de ouro da programação orientada a objetos: "_Evite herança, prefira composição_" e "_Programe voltado a interfaces e não à implementação_". Infelizmente perdi minha cópia hard-cover em inglês, faz uma falta imensa na empresa.

Apesar disso, todos eles revelam sinais de sua idade. No _Design Patterns_, muita gente já considera _Singleton_ como um _antipattern_ (como discutido [neste post](https://blog.caelum.com.br/2006/08/08/singletons-e-static-perigo-a-vista/trackback/)). Mais ainda, alguns vão além, dizendo que se [você precisou utilizar um design pattern, é provável que a sua linguagem não tenha um nível de abstração suficiente](http://en.wikipedia.org/wiki/Design_pattern_(computer_science)) para suprir as suas necessidades. Na minha opinião, uma crítica válida.

No _Refactoring_ e no _Effective Java_, muita coisa já não ocorre mais das formas descritas devido ao Java 5 (em especial com o uso de generics e enumerações), além de que vários códigos podem ser escritos de uma forma mais elegante.

Claro que eu não deixo de ler alguns livros bem técnicos. Os últimos dois que gostei muito foram _Lucene in Action_ e _Enterprise Java Beans 5th edition_ (este último é excelente). Ao contrário dos livros _JSF in action_ e do _Core JSF_, livros os quais não achei que foram escritos de maneira a prender a atenção do leitor: joga-se muita especificação e conceitos, além de que não há uma ordem de aprendizado, parece mais um guia de referência. Sinceramente, não faz sentido algum explicar todos os passos de uma requisição JSF sem antes mostrar concretamente o objetivo e uso da tecnologia.

Ficaram de fora da lista Programming Pearls, Practices of an Agile Developer, The Pragmatic Programmer, The Practice of Programming entre outros gigantes. E você, tem algum livro que considere imprescindível para os desenvolvedores Java? Tem uma outra formação para a sua trindade?
