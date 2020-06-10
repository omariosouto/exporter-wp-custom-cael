---
title: "Scala Days Londres 2012: como foi o evento"
date: "2012-04-26"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Entre os dias 17 e 18 de abril aconteceu em Londres o [Scala Days](http://days2012.scala-lang.org/), evento que reuniu diversos ícones envolvidos com o desenvolvimento da linguagem [Scala](https://blog.caelum.com.br/scala-sua-proxima-linguagem/), a qual também já foi tema de diversos posts no blog da Caelum. [A grade contou com palestras](http://days2012.scala-lang.org/program) sobre frameworks utilizados no mundo de desenvolvimento do Scala, detalhes de implementação do compilador, estado das IDE's que estão sendo desenvolvidas para melhorar o ferramental, até casos de uso da linguagem nas empresas.

[![scala days london 2012](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays-300x180.jpg "scaladays")](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays.jpg)

Um detalhe bastante interessante foi ver a preocupação com os problemas de tempo de compilação e [compatibilidade binária](http://lift.la/scalas-version-fragility-make-the-enterprise) que a linguagem apresenta. Ainda é muito comum que as versões novas de bibliotecas não sejam compativeis com versões prévias e versões novas da linguagem obrigarem os mantenedores dos frameworks a recompilarem seus projetos. O [ScalaTest](http://www.scalatest.org/download), por exemplo, tem versões de downloads para diferentes versões do Scala.

O keynote mais esperado foi o de Martin Odersky, principal criador da linguagem, comentando sobre os rumos do Scala. Ele comentou sobre as novas [SIP's](http://docs.scala-lang.org/sips/index.html), Scala Improvement Process, que são as propostas de melhorias para a linguagem, algo análogo às JSRs. Três bastante comentadas foram as [Implicit Classes](http://docs.scala-lang.org/sips/pending/implicit-classes.html), [Values Classes](http://docs.scala-lang.org/sips/pending/value-classes.html) e as [Macros](http://docs.scala-lang.org/sips/pending/self-cleaning-macros.html). Esta última é uma nova maneira de realizar meta-programação na linguagem, a ideia é tranformar as Macros na forma padrão de criar extensões do compilador. Martin também comentou sobre a nova [API de reflection](http://alots.wordpress.com/2012/03/06/getting-into-the-new-scalas-2-10-reflection-api/) que foi introduzida na versão 2.10 da linguagem. Eles conseguiram facilitar bastante a vida do programdor, principalmente quando é necessário analisar detalhes específicos do código Scala, como os implicits.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays10-150x150.jpg "scaladays10")](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays10.jpg) [![](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays3-150x150.jpg "scaladays3")](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays3.jpg) [![](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays1-150x150.jpg "scaladays1")](https://blog.caelum.com.br/wp-content/uploads/2012/04/scaladays1.jpg)

 

Foi um evento muito bom, enquanto que no ano passado tiveram em torno de 250 inscrições, esse ano chegou na marca de 400 pessoas. Ficou claro que os próximos passos da linguagem são na direção de deixá-la mais preparada para uma abordagem enterprise. O esforço na melhoria das IDEs, compatibilidade binária e compilador deixa evidente a preocupação do time da Typesafe com o ferramental de suporte. O mesmo ferramental que é alvo de muitas critícas hoje em dia. Um outro ponto importante é a importância dada à inovação: basta analisar as novas SIP's que trazem sempre novidades. Você já pode conferir [algumas das palestras online](http://skillsmatter.com/event/scala/scala-days-2012).
