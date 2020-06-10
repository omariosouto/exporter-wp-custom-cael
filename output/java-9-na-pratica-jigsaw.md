---
title: "Java 9 na prática: Jigsaw"
date: "2016-09-15"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Há muito tempo se diz sobre [modularizar a plataforma Java](http://mreinhold.org/blog/modular-java-platform). É um plano que começou desde antes do Java 7, foi [uma possibilidade no Java 8](http://mreinhold.org/blog/plan-b) e por fim, para permitir mais tempo de desenvolvimento, revisão e testes, foi movido para o Java 9.

O projeto Jigsaw, como foi chamado, é composto por [uma série de JEPs](http://openjdk.java.net/projects/jigsaw/). Algumas delas inclusive já disponíveis no Java 8, como os conhecidos [Compact Profiles](https://blog.caelum.com.br/compact-profiles-no-java-8/). A idéia por trás do projeto não é só criar um sistema de módulos, que poderemos usar em nossos projetos, mas também aplicá-lo em toda a plataforma e JDK em busca de melhor organização e desempenho.

> we propose to design and implement a standard module system for the Java SE Platform and to apply that system to the Platform itself, and to the JDK. The module system should be powerful enough to modularize the JDK and other large legacy code bases, yet still be approachable by all developers.

Neste novo post da serie Java 9 prático não só veremos que o projeto já existe e está integrado aos últimos builds, como também vamos explorar e implementar nosso próprio projeto modular. Tudo ao estilo hands on, claro -- preparem seus compiladores!

### Preparando o seu ambiente para o Java 9 modular

O Jigsaw foi integrado ao build do JDK 9 desde a versão ea+111, portanto tudo que você precisa para usá-lo é baixar uma versão igual ou superior a ela. Eu recomendo bastante que, mesmo que você já tenha uma versão relativamente atual, baixe sempre a última antes de qualquer teste. Você pode [fazer o download aqui](https://jdk9.java.net/download/). A versão que estou usando neste post é do build 134 (mais atual de agora).

### Criando um hello world tradicional

No lugar de já começar criando o projeto modular com Jigsaw, vamos criar um projeto da forma tradicional e depois migrá-lo, assim a diferença entre as duas abordagens deve ficar bastante clara. Nos exemplos do post vou fazer tudo pela linha de comando e ir compartilhando os snippets, mas para acompanhar, você pode e deve usar o gerenciador de arquivos do seu sistema operacional e qualquer editor de sua preferência.

Os passos são simples, começando pela criação dos diretórios do projeto e de seus pacotes. O projeto se chamará _hello-jigsaw_ e as classes Java devem ficar no pacote `br.com.caelum.jigsaw`. Podemos fazer tudo com o comando:

\[code\] mkdir -p hello-jigsaw/src/br/com/caelum/jigsaw \[/code\]

Claro que, se você preferir, pode criar usando o gerenciador de arquivos do seu sistema operacional favorito. O importante é que, no final, a estrutura de diretórios fique assim:

\[code\] hello-jigsaw └── src └── br    └── com    └── caelum    └── jigsaw \[/code\]

Tudo ok. Então agora podemos criar a classe `JigSaw.java`, dentro de `src/br/com/caelum/jigsaw`:

\[code language="java"\] package br.com.caelum.jigsaw;

class JigSaw {

public static void main (String... args) { System.out.println("Olá Java 9 modular!"); } } \[/code\]

Aqui tem [um vídeo](https://asciinema.org/a/bqumry6xwol1s7i6nhrf8hwao?speed=2) com todos os passos desse processo, caso queira dar uma olhada. E também vou deixar um link para [fazer download](https://github.com/Turini/hello-jigsaw/archive/hello-world-tradicional.zip) ou [navegar pelos arquivos](https://github.com/Turini/hello-jigsaw/find/hello-world-tradicional) com esse estado do post.

### Compilando e executando, ainda da forma atual

Para compilar o projeto podemos usar o comando `javac` e pronto. Nada de especial aqui.

\[code\] javac src/br/com/caelum/jigsaw/JigSaw.java \[/code\]

Feito isso, caso não exista nenhum erro, podemos executar com:

\[code\] java -cp src/ br.com.caelum.jigsaw.JigSaw \[/code\]

Repare que, como já criamos dentro de um pacote e da pasta _source_, foi preciso passar o parâmetro _\-cp (classpath)_ apontando para ela e também usar o nome completo da classe, que inclui o pacote. Talvez você não esteja tão acostumado com isso, afinal a maioria de nós não compila classes na linha de comando no dia a dia, mas não tem nenhuma novidade por enquanto.

Ao executar, o output, conforme esperado, será a mensagem: _Olá Java 9 modular!_.

Você pode [ver aqui](https://asciinema.org/a/dsr6vneja6p0a8ryigui72966?speed=2) o vídeo do processo de compilação e execução.

Agora finalmente vamos para a parte divertida, que será migrar o projeto para que seja modular!

### Migrando para um projeto modular

A migração não é nada complicada. Você precisa basicamente criar uma nova pasta com o nome do seu módulo, que por convenção, é o pacote base do seu projeto. Você pode pensar no nome do módulo como algo parecido com o `groupId` de um projeto do Maven. É aberto, você pode escrever qualquer coisa, mas idealmente deve seguir uma convenção.

Em nosso caso o módulo será chamado de `br.com.caelum.jigsaw`:

\[code\] mkdir src/br.com.caelum.jigsaw \[/code\]

Ok. O próximo passo será mover todos os arquivos do projeto, de dentro da pasta src, para dentro deste novo diretório do módulo. Você pode fazer isso com o comando _mv_ do terminal ou simplesmente mover a pasta _br_ pelo seu gerenciador de arquivos com tudo que tem dentro para o diretório do módulo. Na linha de comando seria assim:

\[code\] mv src/br/ src/br.com.caelum.jigsaw/ \[/code\]

Agora falta criar um arquivo chamado `module-info.java`, também dentro deste diretório do módulo -- tudo ficará nele a partir de agora. Esse arquivo será o responsável pela declaração do módulo e futuramente as suas dependências. A configuração é mínima:

\[code language="java"\] module br.com.caelum.jigsaw { // não precisa escrever nada aqui, pelo menos por enquanto } \[/code\]

Tudo pronto, no final de todos os passos nosso projeto deve ficar assim:

\[code\] hello-jigsaw └── src └── br.com.caelum.jigsaw ├── br │   └── com │   └── caelum │   └── jigsaw │   └── JigSaw.java └── module-info.java \[/code\]

Vou deixar aqui os links do post nesta fase também, caso queira acompanhar:

\[[video](https://asciinema.org/a/936modvz9getr3c3eib7851gs?speed=2), [download do código](https://github.com/Turini/hello-jigsaw/archive/agora-modular.zip), [navegar pelo projeto](https://github.com/Turini/hello-jigsaw/tree/agora-modular)\]

Nosso projeto agora é modular! Nada complicado, não é mesmo? Vamos ver agora como compilar e executar um projeto com esse novo formato.

### Compilando um projeto modular

O comando `javac` para um projeto modular é um pouco diferente. Ele terá a seguinte estrutura:

\[code\] javac -d <1> <2> <3> \[/code\]

Onde <1> é o diretório onde ficará o código compilado, <2> é o caminho para o seu arquivo `module-info.java`, e <3> é o caminho completo para o seu arquivo .java.

O comando completo para compilar esse nosso projeto ficará assim:

\[code\] javac -d mods/br.com.caelum.jigsaw src/br.com.caelum.jigsaw/module-info.java src/br.com.caelum.jigsaw/br/com/caelum/jigsaw/JigSaw.java \[/code\]

Execute e, se nenhum erro de compilação for exibido, você verá que a nova pasta `mods` foi criada com todo o código compilado. Inclusive os metadados do module-info. O projeto ficou assim:

\[code\] hello-jigsaw └── mods │   └── br.com.caelum.jigsaw │   ├── br │   │   └── com │   │   └── caelum │   │   └── jigsaw │   │   └── JigSaw.class │   └── module-info.class └── src └── br.com.caelum.jigsaw ├── br │   └── com │   └── caelum │   └── jigsaw │   └── JigSaw.java └── module-info.java \[/code\]

Agora temos o diretório src, com o código fonte, e o diretório `mods`, com o código compilado.

### Executando um projeto modular

Para executar você usará o comando `java`, como sempre, mas agora com esse formato:

\[code\] java –module-path <1> -m <2> \[/code\]

Onde <1> é o nome do diretório onde está o código compilado do nosso módulo, ou seja, a pasta _/mods_. E <2> o nome completo (_full qualified name_) da classe que será executada. Um detalhe importante é que, agora que estamos trabalhando de forma modular, o nome da classe recebe também o nome do módulo como prefixo, logo antes do nome do pacote.

O comando para executar a classe JigSaw ficará assim:

\[code\] java –module-path mods/ -m br.com.caelum.jigsaw/br.com.caelum.jigsaw.JigSaw \[/code\]

E a saída, assim como esperado, será o print: _Olá Java 9 modular!_

Aqui vão os links de \[[video](https://asciinema.org/a/d0q3ddlq8uwjfyqg37xl65ezd?speed=2), [download do código](https://github.com/Turini/hello-jigsaw/archive/agora-modular-compilado.zip), [navegar pelo projeto](https://github.com/Turini/hello-jigsaw/tree/agora-modular-compilado)\]

### Adicionando um alert do Swing

Por enquanto usamos apenas um `System.out` no projeto, apenas recursos do pacote padrão `java.lang`. Vamos experimentar usar um importe de alguma outra API do próprio Java pra ver o que acontece? Uma forma simples seria usando um _alert_ do _Swing_ para imprimir a mensagem:

\[code language="java"\] package br.com.caelum.jigsaw;

import javax.swing.JOptionPane;

class JigSaw {

public static void main (String... args) { JOptionPane.showMessageDialog(null,"Olá Java 9 modular!"); } } \[/code\]

Maravilha, agora basta compilar utilizando o mesmo comando de antes e ops...

\[code\] src/br.com.caelum.jigsaw/br/com/caelum/jigsaw/JigSaw.java:3: error: package javax.swing does not exist import javax.swing.JOptionPane; ^ src/br.com.caelum.jigsaw/br/com/caelum/jigsaw/JigSaw.java:8: error: cannot find symbol JOptionPane.showMessageDialog(null,"Olá Java 9 modular!"); ^ symbol: variable JOptionPane location: class JigSaw 2 errors \[/code\]

Você pode [ver aqui um video](https://asciinema.org/a/b6wbvji6vh9a26otdznkk7aun?speed=2) com esses passos e o erro gerado.

O que houve? A mensagem diz que o pacote `javax.swing` não existe! Claro que o _Swing_ não foi removido no Java 9... mas a partir do momento em que compilamos nosso projeto modular, o compilador só usará os módulos que declararmos como dependência no arquivo `module-info.java`. Até então não tinhamos nenhuma.

### O JRE modular

A primeira coisa que eu fiz ao baixar o build do Java 9 com Jigsaw foi abrir o diretório onde ele está instalado e ver o que mudou. Se você fizer isso aí na sua casa, vai perceber que no lugar da pasta _jre/lib_, onde ficavam todos os `.jar`s da JRE, você encontrará um diretório `jmods` com todos os módulos. E não são poucos.

> The original goal of this Project was to design and implement a module system focused narrowly upon the goal of modularizing the JDK, and to apply that system to the JDK itself. We expected the resulting module system to be useful to developers for their own code, and it would have been fully supported for that purpose, but it was not at the outset intended to be an official part of the Java SE Platform Specification.

O pensamento foi: se vamos modularizar o próprio JDK e esse sistema de módulos pode ser útil aos desenvolvedores, por que não torná-lo parte oficial do Java SE? E assim foi feito, todo o código foi reorganizado em módulos. Você pode listar todos eles executando o seguinte comando:

\[code\] java --list-modules \[/code\]

Você pode ver todos [na imagem deste link](https://blog.caelum.com.br/wp-content/uploads/2066/08/jdk-modules.png). Um detalhe que você deve ter percebido é que, assim como não era preciso importar o `java.lang` para usar as classes `System`, `String` e outras, você também não precisa declarar nenhum módulo. Essas classes estão presentes no módulo padrão, `java.base`. Outro detalhe importante é que você não é obrigado a usar Java modular, claro. Você só precisa declarar de quais módulos a sua aplicação depende se ela for modular... caso exista o arquivo `module-info`.

### Declarando a dependência entre módulos

As classes do `Swing`, como o `JOptionPane` que usamos, estão em um módulo chamado `java.desktop`. Para ensinar ao compilador que precisamos desse módulo em nosso projeto, basta declará-lo no arquivo `module-info.java`. A mudança é simples, o código ficará assim:

\[code\] module br.com.caelum.jigsaw { requires java.desktop; } \[/code\]

E pronto! Você não precisa de nenhuma configuração adicional, basta usar o mesmo comando para compilar e agora ele saberá de onde vem o pacote `javax.swing`.

\[code\] javac -d mods/br.com.caelum.jigsaw src/br.com.caelum.jigsaw/module-info.java src/br.com.caelum.jigsaw/br/com/caelum/jigsaw/JigSaw.java \[/code\]

Zero erros. Agora executamos o código e o _alert_ será exibido:

\[code\] java –module-path mods/ -m br.com.caelum.jigsaw/br.com.caelum.jigsaw.JigSaw \[/code\]

Aqui vão os links de \[[video](https://asciinema.org/a/cd5jcw6ho0crng9z06l4qmt3k?speed=2), [download do código](https://github.com/Turini/hello-jigsaw/archive/usando-swing.zip), [navegar pelo projeto](https://github.com/Turini/hello-jigsaw/tree/usando-swing)\]

É claro que, assim como você não precisa executar os comandos `javac` e `java` manualmente na linha de comando hoje, passando o _classpath_ e caminho para as dependências do projeto, você também não vai precisar fazer isso com o projeto modular. As IDEs vão adicionar suporte para que isso fique transparente e para que você não se preocupe tanto com os detalhes.

O post foi um overview rápido sobre um assunto bastante abrangente, então, sem dúvida alguma novos posts entrando em detalhes que não foram discutidos aqui virão em breve. Não deixe de deixar suas perguntas e sugestões de assuntos aqui como comentário, são elas que estão guiando os posts da serie Java 9.

E aí, o que achou do Jigsaw? Tem outros assuntos que gostaria de ler aqui no blog sobre Java 9?
