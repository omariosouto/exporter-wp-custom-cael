---
title: "O mínimo que você deve saber de Java 10"
date: "2018-03-20"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Sim, você leu certo: apenas 6 meses [depois do lançamento do Java 9](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-9/), uma nova versão da linguagem já está [disponível para download](http://www.oracle.com/technetwork/java/javase/downloads/index.html). Com um movimento que ficou conhecido como _Java Release Train_, a Oracle está introduzindo o novo sistema de versionamento da linguagem e uma cultura de releases muito mais frequentes na plataforma.

Na prática, o que isso significa?

Em resumo, que você não precisa esperar 3 ou mais anos para encontrar alguma inovação na linguagem! Atualizações menores não precisam mais ficar em espera até que as grandes features como lambda ou jigsaw fiquem prontas. Isso tem uma serie de pontos positivos, mas gera muitas dúvidas e desconforto com relação ao futuro.

O objetivo desse post é explicar o motivo desse grande passo, discutir possíveis problemas e preocupações, e sumarizar tudo o que você deve saber sobre o Java 10.

### Entendendo as motivações do Java Release Train

Em 2013, enquanto escrevia [o livro Java 8 Prático](https://www.casadocodigo.com.br/products/livro-java8) com o Paulo Silveira, recebemos a notícia de que novamente o lançamento da nova versão da linguagem seria prorrogado por alguns meses, por falhas de segurança e detalhes em aberto do projeto lambda. O mesmo aconteceu com o Jigsaw do Java 9, que atrasou aproximadamente um ano seu release final.

Esse é um problema que rodeou o Java por anos: enquanto as grandes features não estavam prontas, pequenas novidades, diversas melhorias em APIs, na JVM e outros recursos ficavam em espera. Depois de 3 ou 4 anos uma tonelada de alterações entravam de uma só vez.

O Mark Reinhold, arquiteto chefe da Oracle, comenta [em um de seus posts](https://mreinhold.org/blog/forward-faster) que para aliviar essa dor chegaram a considerar um formato de lançamentos de novas versões a cada 2 anos, ao estilo train-mode. Em poucas palavras, se a feature não estava pronta até lá, perdia o trem e entrava no próximo.

O grande problema dessa abordagem foi que 2 anos pareceram tempo demais! Foi preferível atrasar 8 meses e lançar o Java 8 com Lambdas, Streams e outros recursos, do que esperar mais 2 anos por isso. Foi então que chegaram nesse novo formato de lançamentos a cada seis meses. Ele é rápido o bastante para minimizar a dor da espera do próximo trem, e ainda assim, lento o bastante para que cada um deles seja entregue com o mesmo padrão de testes e qualidade de hoje.

A ideia é que existam _update-releases_ a cada trimestre, _feature-releases_ a cada semestre e _long-term support releases_ a cada 3 anos. O primeiro deles é extrito a correções de bugs e detalhes de segurança. O segundo, a cada 6 meses, envolve lançamentos de novas funcionalidades, atualizações em APIs, melhorias na JVM entre outros. O último, e mais longo, é equivalente aos 3 anos que esperamos hoje para o lançamento de uma nova versão. Empresas com perfil mais conservador e que preferem maior estabilidade, podem se planejar para migrar de versão do Java a cada 3 anos. Aventureiros, como eu, já estão rodando o Java 10 em produção desde hoje.

O grande objetivo é que existam atualizações mais frequentes e mais oportunidades de inovar e melhorar a linguagem, como já tem acontecido, mas em um formato extremamente mais rápido.

##### Novo versionamento baseado em tempo

Após instalar o Java 10, experimente executar o comando _\--version_. Diferente de todas as outras vezes, a partir de agora ao lado da versão você encontrará uma data:

\[code\] java 10 2018-03-20 Java(TM) SE Runtime Environment 18.3 (build 10+43) Java HotSpot(TM) 64-Bit Server VM 18.3 (build 10+43, mixed mode) \[/code\]

Com isso você consegue identificar qual a versão específica do Java 10 que está sendo utilizada, conforme os novos builds forem saindo. Se quiser, você encontra mais informações sobre o novo modelo de versionamento [aqui em sua JEP](http://openjdk.java.net/jeps/322).

### Features e principais novidades do Java 10

Mas afinal, o que mudou?

Dois anos atrás [escrevi um post no blog da Alura](http://blog.alura.com.br/java-9-na-pratica-inferencia-de-tipos/) sobre uma das mais polêmicas propostas do JDK 9, da inferência de tipos em variáveis locais. Apesar de não ter sido adotada na época, a proposta que despertou o entusiasmo de boa parte da comunidade está agora inaugurando o novo formato de releases da plataforma.

Seu grande ganho está na legibilidade.

O livro _clean code_ bate bastante na tecla de que qualquer desenvolvedor consegue escrever código que uma máquina consegue ler, mas poucos fazem da forma em que humanos também consigam. O problema é que o uso de nomes super descritivos muitas vezes vai ao encontro da legibilidade e acaba gerando uma verbosidade excessiva, como no exemplo:

\[code language="java"\] ArrayIndexOutOfBoundsException exception = new ArrayIndexOutOfBoundsException(); \[/code\]

Quantas vezes você já viu um código como este?

Claro, usar um tipo abstrato do lado esquerdo já ajudaria um pouco, mas não existia uma forma simples de fugir dessa formalidade de declará-lo. Com o novo sistema de inferência de variáveis locais o mesmo código poderá ser escrito assim:

\[code language="java"\] var exception = new ArrayIndexOutOfBoundsException(); \[/code\]

Legal, não é? E mesmo em exemplos simples, de atibuições diretas ou dos tipos mais comuns da linguagem, o ganho de legibilidade pode ser discretamente notado:

\[code language="java"\] // String name = &quot;Rodrigo Turini&quot;; var name = &quot;Rodrigo Turini&quot;;

// User user = users.findBy(name); var user = users.findBy(name);

// List&lt;String&gt; names = new ArrayList&lt;&gt;(); var names = new ArrayList&lt;String&gt;();

// Stream&lt;String&gt; stream = names.stream(); var stream = names.stream(); \[/code\]

Vale lembrar que uma quantidade menor de linhas de código nem sempre resulta em qualidade melhor. O recurso só ajuda a não repetir palavras o tempo todo, e evitar as redundancias frequentes entre as fases de declaração de tipo e instanciação do valor (_Map = new Map_, _User = new User_ e assim por diante). Foi um passo a mais na jornada do _project coin_, do JDK 7, que trouxe o operador diamante e outras possíbilidades como o _try-with-resources_ que ajudaram, aos poucos, a diminuir a verbosidade e melhorar legibilidade do código Java.

##### Limitações do recurso

Talvez sua reação natural seja se preocupar com situações em que o var, em conjunto com um nomes de variáveis ruins, possa diminuir sua capacidade de identificar tipos durante a leitura do código. Um exemplo seria usá-lo em parâmetros de um método:

\[code language="java"\] public void hadle(var x, var y) { // ... } \[/code\]

O compilador teria que inferir um Object nesse caso, já que não existe uma definição do tipo que pode ser usado. No lugar disso, **o código não compila**. Você não pode usar o var em parâmetros de método dessa forma, assim como qualquer outra situação em que a definição do tipo não esteja explicita do lado direito de sua declaração, como no exemplo:

\[code language="java"\] var delta; //oq é isso? um numero? um aviao? não... um erro de compilação! \[/code\]

Além dessa, que foi a principal novidade, o garbage collector G1 recebeu melhorias de performance em sua execução de [full-gc paralelo](http://openjdk.java.net/jeps/307), e uma nova interface favorece o isolamento de seu código fonte abrindo caminho para novas implementações como o zgc com maior facilidade. [Compartilhamento de classes entre aplicações](http://openjdk.java.net/jeps/310) e a remoção do [javah](http://openjdk.java.net/jeps/313) foram outras das pequenas mudanças que entraram.

Você encontra a lista com [todas as novidades aqui](http://openjdk.java.net/projects/jdk/10/).

### O custo da atualização e preocupações futuras

O Stephen Colebourne, criador do Joda e spec lead do java.time, compartilhou [em um de seus posts](http://blog.joda.org/2018/02/java-9-has-six-weeks-to-live.html) várias preocupações sobre o _Java Release Train_. O principal fator para ele é que com lançamentos muito frequentes as versões anteriores ficariam obsoletas -- sem receber atualizações da Oracle.

E essa preocupação faz sentido!

Mas não é nada inesperado, nem mesmo assustador. Justamente para isso existem as versões LTS (long-term-support). Quem hoje está usando o Java 8, que é o LTS, não precisa se preocupar em atualizar pelos próximos 3 anos, quando sairá o JDK 11 e tomará seu lugar como LTS. O próprio Mark menciona em seu post que podem continuar acontecendo atualizações de segurança depois dos 3 anos, de acordo com a necessidade.

Outras preocupações são com relação as IDEs e bibliotecas da linguagem, que nem sempre vão estar atualizadas e preparadas para as novidades tão frequentes.

É um risco e um custo para você que, assim como eu, gosta de sempre usar os novos recursos e se manter atualizado! No time da Alura fazemos isso não só com Java, mas também muitas de suas bibliotecas. Usamos a última versão do Hibernate, CDI (weld), entre outros.

### Quer se aprofundar mais no assunto?

Eu sempre gosto de indicar a página oficial da nova versão, que contém a lista das específicações que entraram com suas motivações e alguns exemplos:

[http://openjdk.java.net/projects/jdk/10/](http://openjdk.java.net/projects/jdk/10/)

Se você ainda não se atualizou com as versões anteriores, talvez queira dar uma olhada em meu livro [Java 9: Interativo, reativo e modularizado](https://www.casadocodigo.com.br/products/livro-java9) e nos [diversos posts sobre o assunto aqui do blog](https://blog.caelum.com.br/?s=turini).

E você, qual versão tem usado em seus projetos Java?
