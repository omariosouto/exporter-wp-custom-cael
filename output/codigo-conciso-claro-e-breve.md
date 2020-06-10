---
title: "Código conciso: claro e breve"
date: "2011-06-16"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Há [poucos anos](https://blog.caelum.com.br/2009-ano-do-ruby-on-rails-no-brasil/), com a [onda](https://blog.caelum.com.br/scala-sua-proxima-linguagem/) [de novas linguagens](https://blog.caelum.com.br/a-jvm-e-as-outras-linguagens-voce-esta-preparado/), surgiram discussões defendendo código com a intenção de ser "conciso".

[![](https://blog.caelum.com.br/wp-content/uploads/2011/06/concise-300x228.jpg "Conciso")](https://blog.caelum.com.br/wp-content/uploads/2011/06/concise.jpg)

Ser conciso é [expressar muito, com poucas palavras; ser claro e sucinto](http://www.answers.com/topic/concise); [curto, mas com todas as informações necessárias](http://en.wiktionary.org/wiki/concise); [breve e claro](http://en.wiktionary.org/wiki/succinct). Note como todas as definições de concisão implicam em ser curto, mas também na clareza da informação. Dois fatores fundamentais para [a busca do código de qualidade](http://www.caelum.com.br/curso/pm-87-agile-praticas-ageis/)

Tomemos como exemplo o ato de salvar um usuário no único banco de dados acessado por uma aplicação:

\[ruby\] user.saveInTheDatabase(database) \[/ruby\]

Ele é claro, e chega a ser até mesmo redundante [uma vez que "tipifica" o nome do método](https://blog.caelum.com.br/em-busca-do-nome-adequado-metodos-variaveis-e-classes/) ao repetir a palavra `database`. Já o código a seguir mantem a clareza relativa a responsabilidade citada, além de ser sucinto:

\[ruby\] user.save \[/ruby\]

Em uma **outra situação**, o mesmo código acima pode ser implementado executando outras tarefas, como inserindo outros objetos relativos a um usuário:

\[ruby\] def save super maquinas\_a\_instalar.each do |instancia| cria\_maquina\_no\_banco(instancia) end

\# ou qualquer outro tipo de efeito colateral end \[/ruby\]

Agora o código anterior, continua sucinto mas já não é mais tão claro:

\[ruby\] user.save \[/ruby\]

Ele não diz que faz o que realmente fará. A busca pelo código curto introduziu a possibilidade de engano daquele que vai ler, pior ainda, o nome do método e a maneira de implementá-lo levam ao erro: ele não é conciso, ele é somente breve.

Por isso alguns usos excessivos de técnicas avançadas, como AOP ou mesmo reflection, precisam de muito cuidado. Remotabilidade transparente, EJBs, JPA, ActiveResource e quaisquer outras bibliotecas que trabalham um serviço de maneira a ocultá-lo podem gerar problemas caso o usuário não tenha um certo conhecimento do efeito de suas chamadas. É o caso do excesso de invocações remotas ou de queries sqls, que os frameworks já citados podem trazer sem um conhecimento mais profundo..

[Concisão não significa texto curto](http://revistalingua.uol.com.br/textos.asp?codigo=12308). Um dos grandes desafios de um autor é ser conciso, para não cansar seus leitores, mantendo a clareza de seu texto. Algumas linguagens ou até mesmo bibliotecas podem acelerar ou desacelerar o processo de falar algo com poucas palavras, o que não significa necessariamente o código mais fácil de entender e, consequentemente, de manter a longo prazo.

Escrever pouco - a métrica de linhas de código - traz o menor código, [mas não necessariamente o mais claro ou melhor](http://martinfowler.com/bliki/CannotMeasureProductivity.html). No exemplo a seguir, o cliente está sendo somente salvo ou algo mais está sendo feito e a "pseudo-concisão" está nos enganando?

\[java\] cliente.save() \[/java\]
