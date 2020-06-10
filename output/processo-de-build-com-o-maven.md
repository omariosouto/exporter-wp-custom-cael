---
title: "Processo de build com o Maven"
date: "2017-02-07"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O [Maven](http://maven.apache.org/) é uma ferramenta de gerenciamento, construção e implantação de projetos muito interessante, que te ajuda no processo de gerenciamento de dependências e no de build, geração de relatórios e de documentação. Na Caelum esta é a ferramenta usada em todos os projetos internos e nas consultorias.

> _Este post, escrito em 2008, foi atualizado neste ano de 2017 tanto na formatação como no conteúdo._

Muitas pessoas migram seus projetos para o Maven, mas acabam arrumando mais problemas que soluções, pois não conseguem configurá-lo corretamente, e acabam desistindo e fazendo tudo na mão, ou voltando para o Ant.

Mas se você conseguir ajustar as configurações, o Maven vai te ajudar muito e vai compensar todos os (poucos) problemas que ele eventualmente causa. No início do uso do Maven, espere formar com ele uma relação de amor e ódio.

## Instalando o Maven

Para começar a usar o Maven, tudo o que você precisa fazer é [baixá-lo e configurar umas poucas variáveis de ambiente](http://maven.apache.org/download.html). Depois de ter feito isso, é só digitar `mvn [target]` na linha de comando. Alguns sistemas operacionais já te oferecem essa instalação através do `macport` ou `apt-get`.

## O básico do Maven

A unidade básica de configuração do Maven é um arquivo chamado [pom.xml](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html), que deve ficar na raiz do seu projeto. Ele é um arquivo conhecido como _Project Object Model_: lá você declara a estrutura, dependências e características do seu projeto.

A idéia é bem parecida com o build.xml do Ant: você deixa o pom.xml na raiz do seu projeto para poder chamar as targets de build do seu projeto. O [menor arquivo pom.xml](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Minimal_POM) válido é o seguinte: `<project>   <modelVersion>4.0.0</modelVersion>   <groupId>br.com.caelum</groupId>   <artifactId>teste</artifactId>   <version>1.0</version> </project>`

Que contém apenas a identificação do projeto, e uma informação a mais: `modelVersion`, que é a identificação da versão do arquivo pom.xml e deve ser sempre 4.0.0. A identificação do projeto consiste em três informações:

- `groupId`: um identificador da empresa/grupo ao qual o projeto pertence. Geralmente o nome do site da empresa/grupo ao contrário. Ex: `br.com.caelum`.
- `artifactId`: o nome do projeto. Ex: `teste`.
- `version`: a versão atual do projeto. Ex: `1.0-SNAPSHOT`.

## Gerenciamento de dependência

Essas informações são usadas em muitos lugares, como o controle de dependências que é, na minha opinião, a funcionalidade mais útil do Maven. Por exemplo, para dizer que o log4j 1.2.15 é uma dependência da sua aplicação é só acrescentar no seu pom as linhas: `<project> ...   <dependencies>     <dependency>       <groupId>log4j</groupId>       <artifactId>log4j</artifactId>       <version>1.2.15</version>     </dependency>   </dependencies> ... </project>` Quando necessário, o Maven vai baixar pra você o jar do log4j 1.2.15, e todas as suas dependências, e vai colocá-las no classpath da sua aplicação durante os builds, testes, etc. Ou seja, você não precisa mais entrar no site do log4j, baixar um zip com vários jars e ter que procurar quais jars devem ser colocados no classpath!

## Como o Maven realiza seus processos

No [Repositório de Bibliotecas do Maven](http://mvnrepository.com) você encontra os jars que você pode colocar como dependência do seu projeto, e o pedaço de xml que você deve copiar e colar dentro da tag dependencies do seu pom para incluir essas bibliotecas.

Todos os jars baixados pelo Maven são guardados na pasta `repository` dentro da `M2_HOME` que você configurou quando instalou o Maven. Assim, se mais de um projeto seu depende do mesmo jar, ele não é baixado de novo.

A **grande** diferença entre o `build.xml` do Ant e o `pom.xml` do Maven é o paradigma. No Ant usamos esse XML praticamente como uma linguagem de programação, onde você da comandos em relação ao build do projeto.

No Maven usamos o XML para definir a estrutura do projeto, e a partir dessas declarações o Maven possui targets bem definidos que usam essas informações para saber como realizar aquela tarefa.

Um exemplo: para compilar com o Ant criamos um target que chama o javac, mas para compilar com o Maven usamos um target já existente (não o criamos), e ele vai usar a informação que define onde está o código fonte e para onde ele deve ser compilado (sendo que muitas dessas informações possuem convenções e defaults, e nem precisam ser configuradas).

## Utiização de plugins

Além dos principais targets do Maven, você pode executar targets de plugins. Você só precisa digitar na linha de comando:

`mvn [nomedoplugin]:[target]`

E então o Maven baixa o plugin, se necessário, e executa a target pra você. Existe uma lista bem grande de [plugins do Maven](http://maven.apache.org/plugins/index.html) e uma boa parte desses plugins podem ser usados sem nenhuma configuração adicional no seu _pom_.

Para dar um exemplo de plugin do Maven nada melhor do que o plugin que cria um protótipo de projeto do Maven: o [Archetype](http://maven.apache.org/plugins/maven-archetype-plugin/). É bem parecido com o scaffold do Ruby: ele cria um protótipo de projeto a partir de um modelo escolhido. O jeito mais fácil de usar esse plugin é digitando na linha de comando:

`mvn archetype:create`

E então o Archetype [vai perguntar](http://maven.apache.org/plugins/maven-archetype-plugin/usage.html) qual é o tipo de projeto que você deseja, o groupID, artifactID, version e o pacote referentes ao seu projeto. Depois disso você terá uma estrutura de projeto pronta para ser usada.

Por exemplo se você escolheu o tipo de projeto maven-archetype-quickstart, o Archetype vai criar uma estrutura de pastas parecidas com a seguinte:

```

teste
|-- pom.xml
`-- src
    |-- main
    |   `-- java
    |       `-- br
    |           `-- com
    |               `-- caelum
    |                   `-- teste
    |                       `-- App.java
    `-- test
        `-- java
            `-- br
                `-- com
                    `-- caelum
                        `-- teste
                            `-- AppTest.java
```

E então é só continuar o seu projeto a partir daí. O código de teste já vem separado do código principal, e o JUnit já vem como dependência da aplicação. Você também pode criar as pastas `src/main/resources` e `src/test/resources` para colocar os recursos (arquivos de configuração, de teste, e etc) do código principal e do de testes, respectivamente.

Tudo que estiver dentro dessas pastas é copiado diretamente para o diretório onde as classes são compiladas, sem que seja necessário fazer nenhuma configuração adicional.

## Ajustando a estrutura do projeto gerado pelo Maven

Se você, por algum motivo, não gostou da estrutura que o Maven criou, ou está querendo migrar um projeto para o Maven que não segue essa estrutura, você pode configurar os diretórios do projeto [acrescentando algumas linhas no pom](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html#Super_POM):

```
<project>
...
<build>
    <sourceDirectory>
      ${project.basedir}/src/java/main
    </sourceDirectory>
    <testSourceDirectory>
      ${project.basedir}/src/java/test
    </testSourceDirectory>
    <resources>
          <resource>
                 <directory>
                   ${project.basedir}/src/resources/main
                 </directory>
          </resource>
    </resources>
    <testResources>
          <testResource>
                 <directory>
                   ${project.basedir}/src/resources/test
                 </directory>
          </testResource>
    </testResources>
</build>


...
</project>
```

Nesse exemplo o diretório principal de código e de recursos estarão em `src/java/main` e  `src/resources/main` respectivamente, e os diretorios de teste em `src/java/test` e `src/resources/test`.

## O ciclo de vida do Maven

Agora com um projeto Maven já preparado, vamos para a principal funcionalidade: o build. O build do Maven é baseado no conceito de [ciclo de vida](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html): o processo de construção e distribuição da sua aplicação é dividido em partes bem definidas chamadas fases, seguindo um ciclo. O ciclo padrão é o seguinte:

- **compile** - compila o código fonte do projeto
- **test** - executa os testes unitários do código compilado, usando uma ferramenta de testes unitários, como o junit.
- **package** - empacota o código compilado de acordo com o empacotamento escolhido, por exemplo, em JAR.
- **integration-test** - processa e faz o deploy do pacote em um ambiente onde os testes de integração podem ser rodados.
- **install** - instala o pacote no repositório local, para ser usado como dependência de outros projetos locais
- **deploy** - feito em ambiente de integração ou de release, copia o pacote final para um repositório remoto para ser compartilhado entre desenvolvedores e projetos

Você pode invocar qualquer dessas fases na linha de comando, digitando:

`mvn [fase]`

Por exemplo se você digitar `mvn package` o Maven vai executar todas as fases anteriores do ciclo até a fase `package`. Uma lista completa das fases do ciclo de vida possíveis pode ser encontrada [aqui](http://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html#Lifecycle_Reference).

Algumas das fases do ciclo possuem plugins associadas a elas, e esses plugins são executados assim que a fase é chamada para ser executada. Você pode também registrar plugins para rodarem em qualquer fase do ciclo, conseguindo, assim, personalizar o build do seu projeto facilmente.

Por exemplo, se você quiser criar um jar com o código fonte do projeto, e que esse jar seja gerado depois que o projeto foi empacotado, é só acrescentar no seu pom:

```
<project>
  ...
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-source-plugin</artifactId>
        <executions>
          <execution>
            <id>attach-sources</id>
            <phase>package</phase>
            <goals>
              <goal>jar</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>
  ...
</project>
```

Assim, o plugin [Source](http://maven.apache.org/plugins/maven-source-plugin/) vai executar seu goal `jar` na fase `package` do ciclo de vida. É como se fosse chamado `mvn source:jar` quando o build passa pela fase de package. A fase package já possui um plugin associado a ela: o `jar:jar` (supondo que é um projeto jar), então o plugin source só será executado depois do jar:jar.

Em geral se você registrar mais de um plugin pra mesma fase, eles serão executados na ordem em que eles forem declarados. O jeito de configurar o plugin para colocá-lo dentro de uma fase do ciclo geralmente está no site principal do plugin, na seção **Usage**.

## Mais plugins úteis no dia a dia

O Maven possui ainda outras funcionalidades interessantes, como geração de relatórios. Alguns plugins também merecem uma atenção especial, como:

- O [Eclipse](http://maven.apache.org/plugins/maven-eclipse-plugin/) que gera informações de projeto para o eclipse (`.classpath` e `.project`)
- O [Antrun](http://maven.apache.org/plugins/maven-antrun-plugin/) que te permite executar código Ant dentro do Maven
- O [Cobertura](http://maven-plugins.sourceforge.net/maven-cobertura-plugin/) que gera um relatório mostrando a cobertura de testes no seu projeto
- O [Jetty](https://maven.apache.org/plugins/maven-war-plugin/examples/rapid-testing-jetty6-plugin.html) que sobe uma instância do Jetty com sua aplicação deployed
- O [Selenium](http://www.seleniumhq.org/download/maven.jsp) que sobe uma instância do servidor do Selenium para poder fazer os testes de aceitação do selenium

Enfim, existem vários plugins interessantes e é relativamente fácil achar o plugin que faz o que você precisa. É igualmente fácil, também, fazer um plugin para o Maven, o chamado [Mojo](http://maven.apache.org/plugin-developers/index.html).

Aqui na Caelum, além do Maven e JUnit, usamos muito o [Selenium](http://selenium.openqa.org/), juntamente com o SeleniumDSL, para os testes de integração, e o [Cruise Control](http://cruisecontrol.sourceforge.net/) para o controle da integração contínua. Esperamos colocar tutoriais e vídeos sobre essas ferramentas também.
