---
title: "Então você quer ser um arquiteto Java?"
date: "2010-07-21"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Durante o atual processo de revisão do livro de [Arquitetura e Design de Software](http://www.arquiteturajava.com.br), discussões apareceram sobre [o termo **arquiteto**](http://martinfowler.com/ieeeSoftware/whoNeedsArchitect.pdf). Antes de definir o que faz um arquiteto, há o termo **arquitetura**.

\[caption id="attachment\_2942" align="aligncenter" width="501" caption="Quem é o arquiteto? Aquele que senta sozinho e toma todas as grandes decisões?"\][![](https://blog.caelum.com.br/wp-content/uploads/2010/07/neo-architect.jpg "neo-architect")](https://blog.caelum.com.br/wp-content/uploads/2010/07/neo-architect.jpg)\[/caption\]

**O que é a arquitetura de uma aplicação?** Uma pergunta difícil de responder. Entre as definições mais antigas, Roy Fielding possui um bom texto no primeiro capítulo de sua [dissertação de doutorado](http://www.ics.uci.edu/~fielding/pubs/dissertation/software_arch.htm). O Instituto de Engenharia de Software da Universidade de Carnegie Mellon [apresenta diferentes definições](http://www.sei.cmu.edu/architecture/start/definitions.cfm), [algumas clássicas](http://www.sei.cmu.edu/architecture/start/bibliographicdefs.cfm) e bastante conhecidas, como "_arquitetura é a estrutura do sistema, composta de componentes, as propriedades que são visíveis externamente desses componentes e o relacionamento entre eles_".

[Nas palavras de Martin Fowler](http://martinfowler.com/articles/designDead.html#id21865), "_o termo arquitetura envolve a noção dos principais elementos do sistema, **as peças que são difíceis de mudar**. Uma fundação na qual o resto precisa ser construído_". Fowler [reformula sua definição de arquitetura](http://martinfowler.com/ieeeSoftware/whoNeedsArchitect.pdf) e a define como "_as peças que as pessoas acham que é difícil de mudar_". No mesmo artigo Ralph Johnson, do GoF, diz que arquitetura "_é o conjunto de decisões de design que gostaríamos de ter feito no começo do projeto_" e termina com uma definição mais abrangente: "_arquitetura é tudo aquilo que importa_". Com tantas definições, talvez seja mais fácil diferenciarmos design de arquitetura.

**Qual é a diferença de design e arquitetura de software?** Aqui também temos uma resposta clássica na literatura: a arquitetura é responsável pelos requisitos não-funcionais, e o design pelos funcionais. Mas parece que essa distinção não é tão clara assim para muitos outros autores.

[Neal Ford apresenta uma distinção](http://www.ibm.com/developerworks/java/library/j-eaed10/index.html#distinguishing) simples, realçando que o design é feito em cima do que foi decidido pela arquitetura, e por isso o que faz parte da arquitetura é mais difícil de mudar. Devemos minimizar as peças que dificultam mudanças do nosso design, mas é impossível eliminar todas, além de que flexibilidade sempre vem a um custo de complexidade.

[![](https://blog.caelum.com.br/wp-content/uploads/2010/07/design-150x150.jpg "design")](https://blog.caelum.com.br/wp-content/uploads/2010/07/design.jpg)

É difícil criar um distinção maior entre os dois. No livro Patterns of Enterprise Application Architecture, Fowler diz que "_alguns dos padrões nesse livro podem ser chamados arquiteturais, já que representam decisões importantes sobre essas partes; outros são mais sobre design e te ajudam a implementar essa arquitetura. Eu não faço nenhuma tentativa forte de separar esses dois, já que é **o que é arquitetural ou não é subjetivo**_".

**O arquiteto deve saber programar na plataforma em questão?** Sem dúvida. Cada vez mais vemos que o [design e a implementação devem ser trabalhados juntos](http://www.slideshare.net/pcalcado/expressive-design-in-20-minutes). A imagem de um [arquiteto distante sem profundo conhecimento técnico que apenas toma as grandes decisões](http://www.infoq.com/presentations/agilists-and-architects) ficou pra trás: conhecimento técnico e a capacidade de liderança são as características fundamentais.

Mais do que querer ser o poderoso arquiteto que apenas despacha ordens e toma todas as grandes decisões, cada vez mais enxergamos que o caminho é ser o líder que incentiva essa tomada de decisão, além de ser um exímio programador. Parafraseando mais uma vez Martin Fowler, "_...o arquiteto deve ser como um guia... que é um experiente e capacitado membro da equipe que ensina aos outros a melhor se virarem, ainda assim ele está sempre lá para as partes mais complicadas_".

Vale lembrar que [precisamos de mais de 10 mil horas, ou 10 anos, para dominar uma linguagem](http://norvig.com/21-days.html).
