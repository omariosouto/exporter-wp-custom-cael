---
title: "Encontrando smells no seu código"
date: "2015-11-17"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Nem sempre pensamos na qualidade do nosso código enquanto estamos escrevendo-o. E, vez ou outra, deixamos passar uma classe que tem com mais linhas do que deveria, métodos com muitos parâmetros, ou mesmo até mais acoplada ou menos coesa do que gostaríamos.

Mas como fazer pra detectar esses problemas? Afinal, é impossível olhar classe a classe ou método a método. É para isso que temos ferramentas, como [PMD](https://pmd.github.io/), [Checkstyle](http://checkstyle.sourceforge.net/), [FindBugs](http://findbugs.sourceforge.net/), e etc. Elas nos ajudam a encontrar classes que são problemáticas.

Como elas fazem isso? Elas saem lendo o seu código, e capturando tudo que acham estranho. E depois nos dão um relatório! Para dar um gostinho disso, vamos experimentar o PMD. É fácil, basta baixá-lo (na versão certa do seu sistema operacional), e executá-lo!

No Linux e Mac, o PMD vem com um _run.sh_ dentro da pasta /bin. E aí, basta configurarmos. Tudo que precisamos passar pra ele é:

- \-d e apontar o diretório do projeto que queremos analisar. No meu caso, vou analisar o [MetricMiner](https://github.com/mauricioaniche/metricminer2), o projeto que uso em meu doutorado.
- \-f e o formato que o PMD deve escrever a saída. Ele possui vários formatos, como CSV, HTML, texto, e etc.
- \-R e a lista de regras a serem avaliadas. O PMD possui uma [lista de regras enorme para Java](https://pmd.github.io/pmd-5.4.0/pmd-java/rules/index.html) (e muitas outras linguagens).

Vamos começar então com apenas uma regra:

\[code\] ./run.sh pmd -d ~/workspace/metricminer2/src/ -f html -R rulesets/java/basic.xml > muito.html \[/code\]

E voilà! Olha a [saída aqui](https://s3.amazonaws.com/caelum-online-public/aniche/pmd/pouco.html). Com o conjunto de regras _java-basic_, o PMD reclamou sobre "[Avoid using a branching statement as the last in a loop](https://pmd.github.io/pmd-5.4.0/pmd-java/rules/java/basic.html#AvoidBranchingStatementAsLastInLoop)" em 2 classes. Ou seja, ele criticou que em dois loops, a última linha deles era um _break_. E, segundo o manual, isso pode ser causar bugs ou mesmo dificultar a legibilidade do código.

Legal, né!? Podemos aumentar ainda mais o conjunto de regras que ele vai executar. Por exemplo, _basic_ e _design._ Veja [aqui](http://s3.amazonaws.com/caelum-online-public/aniche/pmd/muito.html) que agora o número de problemas levantados é muito maior. E você pode clicar em cada um deles para entender um pouco mais!

\[code\] ./run.sh pmd -d ~/workspace/metricminer2/src/ -f html -R rulesets/java/basic.xml,rulesets/java/design.xml > muito.html \[/code\]

Agora, mais importante do que usar a ferramenta, é saber interpretar os dados. Veja que muitos dos problemas acontecem na classe Java8BaseListener, que é uma classe gerada automaticamente pelo [Antlr](http://www.antlr.org/) (um framework usado pelo MetricMiner). Ou seja, é uma classe que não é mantida manualmente, então não deveria aparecer no relatório.

Então, agora que você conhece a ferramenta, mais do que sair usando, customize-a. Escolha a dedo as regras para executar, e crie o [seu conjunto de regras](https://pmd.github.io/pmd-5.4.0/customizing/howtomakearuleset.html). E por quê não também plugar o PMD na sua ferramenta de build favorita? Tudo é possível!

Agora, a próxima pergunta: com todos esses smells, quais eu refatoro, e quais eu não refatoro? Pois isso é assunto para o próximo post. Nesse meio tempo, você pode ler mais sobre boas práticas de programação em meu [livro de OO e SOLID para ninjas](http://www.casadocodigo.com.br/products/livro-oo-solid) ou mesmo no [curso online do Alura](https://www.alura.com.br/curso-online-orientacao-a-objetos-avancada-e-principios-solid).
