---
title: "Voando nas nuvens com VRaptor 4: Heroku e OpenShift"
date: "2015-02-05"
author: "luiz.real"
authorEmail: "luiz.real@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Hospedar sua aplicação numa nuvem é uma alternativa cada vez mais interessante. Os serviços são cada vez mais baratos, versáteis e fáceis de usar. Esse cenário fica ainda mais interessante quando podemos usar um framework como o [VRaptor](http://www.vraptor.org/pt/) para escrever nossa aplicação.

Já escrevemos anteriormente aqui [como colocar uma aplicação escrita com VRaptor 3 no Heroku](https://blog.caelum.com.br/do-zero-ao-deploy-com-o-vraptor-console/) de forma bem simples. Na sua quarta versão, o VRaptor passou por algumas mudanças arquiteturais, entre elas o uso da especificação [CDI](http://cdi-spec.org/) para controlar e injetar as dependências da aplicação. Isso significa que o VRaptor consegue aproveitar melhor os recursos de um servidor de aplicação como [Glassfish](https://glassfish.java.net/) ou [Wildfly](http://wildfly.org/). Porém isso significa também que precisamos mudar um pouco a forma de fazer o deploy da aplicação num servidor na nuvem.

Neste post, veremos como criar uma aplicação simples do VRaptor e colocá-la para funcionar em dois serviços populares de hospedagem na nuvem: o Heroku e o [Openshift](https://www.openshift.com/), do qual [já falamos aqui anteriormente](https://blog.caelum.com.br/screencast-sua-app-no-cloud-com-openshift/). Para facilitar o gerenciamento de dependências da aplicação, usaremos o [Maven](https://blog.caelum.com.br/processo-de-build-com-o-maven/).

## A aplicação base

Vamos usar uma aplicação bem simples para realizar os testes. Ela vai ter um único método que responde "Olá" em texto puro:

\[code language="java"\] @Controller public class OlaController { @Inject private Result result;

@Get("/") public void ola() { result.use(Results.http()).body("Olá").setStatusCode(200); } } \[/code\]

O arquivo `pom.xml` com as dependências do projeto precisa basicamente apontar para o VRaptor e para o javax.inject, que contém as anotações da especificação CDI:

\[code language="xml"\] <dependencies> <dependency> <groupId>br.com.caelum</groupId> <artifactId>vraptor</artifactId> <version>4.1.4</version> </dependency> <dependency> <groupId>javax.inject</groupId> <artifactId>javax.inject</artifactId> <version>1</version> <scope>provided</scope> </dependency> </dependencies> \[/code\]

Repare que a dependência javax.inject está declarada como `provided`. Declaramos ela assim pois, quando colocarmos a aplicação no Wildfly, por exemplo, ela já estará lá. Precisamos dela apenas para compilar o projeto.

Com isso, já conseguimos executar nossa aplicação localmente ou mesmo num servidor configurado por nós mesmos. Porém para que ela funcione no Openshift, precisamos fazer alguns ajustes.

## Executando no Openshift

O Openshift já executa as aplicações Java usando o Wildfly como servidor. E mais: usa o Maven para compilar o projeto e gerar o WAR. Bastam alguns pequenos ajustes na configuração do Maven para que o Openshift consiga executar nossa aplicação corretamente.

O primeiro passo é criar uma aplicação no Openshift usando o _cartridge_ com o Wildfly já instalado. Se você tiver a ferramenta de linha de comando `rhc` instalada, basta executar o comando

```
rhc create-app <nome-da-app> jboss-wildfly-8
```

Isso vai criar uma pasta com o nome da sua aplicação com um projeto Java já configurado lá dentro. Podemos usar esse projeto como base para a nossa aplicação com o VRaptor simplesmente adicionando-o como dependência no arquivo `pom.xml` e colocando nossas classes dentro de `src/main/java`.

Se quiser, também é possível adaptar o projeto criado anteriormente. Você só precisa garantir que o Maven gerará um arquivo chamado `ROOT.war` dentro da pasta `deployments` ao final do build. Para isso, você pode adicionar o seguinte trecho ao seu `pom.xml`:

\[code language="xml"\] <profiles> <profile> <id>openshift</id> <build> <finalName>vr4wildflytest</finalName> <plugins> <plugin> <groupId>org.apache.maven.plugins</groupId> <artifactId>maven-war-plugin</artifactId> <version>2.3</version> <configuration> <failOnMissingWebXml>false</failOnMissingWebXml> <outputDirectory>deployments</outputDirectory> <warName>ROOT</warName> </configuration> </plugin> </plugins> </build> </profile> </profiles> \[/code\]

E pronto, simples assim! Basta um commit e um `git push` para que sua aplicação usando VRaptor 4 esteja rodando no Openshift.

## Executando no Heroku

No Heroku, também temos o Maven construindo nosso projeto a cada `git push`. Porém, nossas facilidades acabam por aí: não temos nenhum servidor instalado lá para nós, apenas o Java (o Maven só está disponível durante a construção do WAR).

Nesse cenário, podemos recorrer a um servidor embarcado na nossa aplicação (o [Jetty](http://eclipse.org/jetty/) tem uma API muito boa para isso). Conseguimos rodar o VRaptor 4 no Jetty com alguns ajustes na nossa aplicação ([a documentação explica como](http://www.vraptor.org/pt/docs/dependencias-e-pre-requisitos/)), mas o ideal seria não precisar fazer nenhum ajuste na nossa aplicação para que ela seja colocada na nuvem.

Uma outra alternativa nesse cenário seria **instalar o Wildfly** no Heroku. Isso é possível e na verdade o próprio Maven pode cuidar disso para a gente. Basta adicionarmos algumas linhas à seção de plugins do `pom.xml` instruindo-o a baixar e descompactar o Wildfly na fase `package` da construção da nossa aplicação:

\[code language="xml"\] <plugin> <groupId>org.apache.maven.plugins</groupId> <artifactId>maven-dependency-plugin</artifactId> <version>2.10</version> <executions> <execution> <id>unpack</id> <phase>package</phase> <goals> <goal>unpack</goal> </goals> <configuration> <artifactItems> <artifactItem> <groupId>org.wildfly</groupId> <artifactId>wildfly-dist</artifactId> <version>8.2.0.Final</version> <type>zip</type> <overWrite>false</overWrite> <outputDirectory>target</outputDirectory> </artifactItem> </artifactItems> </configuration> </execution> </executions> </plugin> \[/code\]

Com isso, já teremos o Wildfly disponível. Falta apenas fazer com que nossa aplicação seja executada dentro desse Wildfly. Para isso, podemos configurar o plugin do Maven que gera o WAR para que ele gere esse WAR dentro da pasta `deployments` do Wildfly. Os arquivos `.war` dentro dessa pasta são automaticamente processados pelo Wildfly.

\[code language="xml"\] <plugin> <groupId>org.apache.maven.plugins</groupId> <artifactId>maven-war-plugin</artifactId> <version>2.3</version> <configuration> <failOnMissingWebXml>false</failOnMissingWebXml> <outputDirectory>target/wildfly-8.2.0.Final/standalone/deployments</outputDirectory> <warName>ROOT</warName> </configuration> </plugin> \[/code\]

No código acima, também configuramos o Maven para que ele gere nosso arquivo com o nome `ROOT.war`. Dessa forma, nossa aplicação subirá com o contexto `/` dentro do servidor. Ou seja, localmente, conseguiremos testar nossa aplicação acessando http://localhost:8080.

Por fim, o Heroku precisa saber qual comando executar para subir nossa aplicação. Dizemos isso a ele no arquivo `Procfile`, que fica na raiz do nosso projeto. Para o nosso caso, o conteúdo desse arquivo será:

```
web: target/wildfly-8.2.0.Final/bin/standalone.sh -Djboss.http.port=$PORT -b 0.0.0.0
```

Repare que passamos dois parâmetros ao Wildfly: a porta na qual ele deve subir (o Heroku muda essa porta a cada vez que a aplicação sobe) e o parâmetro `-b`, dizendo que o Wildfly deve receber requisições de qualquer endereço (por padrão ele recebe requisições apenas da própria máquina).

Pronto! Agora, a cada `git push`, nossa aplicação será atualizada automaticamente no Heroku. E mais: novos desenvolvedores no time não precisarão se preocupar com a instalação e a configuração do servidor na própria máquina, já que um simples `mvn package` já vai baixar o servidor e colocar a aplicação no lugar certo para que eles comecem a trabalhar!

## Outras nuvens

Em alguns casos, usar um serviço como Heroku ou Openshift pode não ser uma opção. Às vezes sua aplicação precisa de uma infraestrutura muito específica, que esses serviços mais automatizados não podem prover. Nesses casos, podemos seguir uma estrutura bem parecida com a que fizemos para executar a aplicação no Heroku, mas teremos que cuidar da atualização da aplicação e dos outros serviços dos quais nossa aplicação depender, como bancos de dados ou servidores de e-mail.

Uma nuvem que te dá esse poder é a nuvem da Amazon, que você pode conhecer melhor na [trilha de infraestrutura do Alura](http://www.alura.com.br/cursos-online-devops). Lá também você pode conhecer outras ferramentas que facilitam o trabalho de manutenção numa infraestrutura desse tipo, ou mesmo nos seus próprios servidores!
