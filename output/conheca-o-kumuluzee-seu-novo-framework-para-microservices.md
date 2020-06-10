---
title: "Conheça o KumuluzEE - seu novo framework para Microservices"
date: "2016-07-05"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Parece que a nova buzzword é realmente Microservices. A definição do [Adam Bien](http://www.adam-bien.com/roller/abien/entry/the_perfect_javaee_microservice) parece perfeita: é um serviço que tem máxima coesão e mínimo acoplamento. A palavra principal parece ser **decomposição**. Um serviço para tratar REST, outro para WSDL, JMS, JPA e assim por diante, ganhando assim uma melhor escalabilidade, manutenibilidade e tornando os serviços mais fáceis de serem gerenciados e atualizados. Relacionei serviços com especificações, mas obviamente pode ser algo referente ao seu domínio: pagamento, estoque, vendas, geração de e-books, autenticação, notificação, usuários, dentre outros.

[Já discutimos sobre a diferença arquitetural entre microservices e serviços monolíticos](https://blog.caelum.com.br/arquitetura-de-microservicos-ou-monolitica/). Martin Fowler também já resumiu os [pré-requisitos](http://martinfowler.com/bliki/MicroservicePrerequisites.html) para uma arquitetura microservice: monitoração ([prometheus](https://prometheus.io/), [icinga](https://www.icinga.org/), [pagerduty](https://www.pagerduty.com/)), provisionamento ([mesos](http://mesos.apache.org/), [aurora](http://aurora.apache.org/), [kubernetes](http://kubernetes.io/)) e deploy ([travis](https://travis-ci.org/), [jenkins](https://jenkins.io/), [docker](https://www.docker.com/)) . Além disso, o [Philip Calçado](https://www.infoq.com/presentations/soundcloud-microservices) contou a [saga da migração](https://developers.soundcloud.com/blog/category/architecture) de um [sistema monolítico para microservices](https://www.infoq.com/news/2014/06/soundcloud-microservices) no SoundClound e mostrou o [porquê do mercado](http://philcalcado.com/2015/09/08/how_we_ended_up_with_microservices.html) se interessar tanto por esse "novo" estilo arquitetural.

 

> As much as I keep repeating that the term _microservices_ doesn’t mean much, the one thing you can be sure when somebody uses this word to describe their architecture is that there will be a lot of services.

 

De 2014 até o presente momento, o termo cresceu exponencialmente em [pesquisas no google](https://www.google.com/trends/explore#q=microservice). Por quê? Porque desenvolvedor prefere trabalhar com unidades menores, commits menores e a probabilidade de gerar um bug será menor em um sistema menor. E a desvantagem? Bom, agora não existe apenas um sistema, é um ecossistema de aplicações que devem ser devidamente orquestradas. (_Eu particularmente ando me [perdendo bastante na quantidade de terminais abertos](https://blog.caelum.com.br/aumentando-a-produtividade-com-o-git/) com repositórios [gits e branches](https://github.com/nvie/gitflow) diferentes por projeto que tenho que lidar)_

## e os frameworks...

Então você optou por fazer uma arquitetura baseada em microservices, contudo, onde deployar? Em um application server JavaEE? Tomcat? Mas será mesmo que um war é necessário?

Bom, vou mostrar o que de interessante está acontecendo com as implementações [no mundo Java](https://www.infoq.com/news/2016/02/javaee-microservices?utm_source=infoq&utm_medium=popular_widget&utm_campaign=popular_content_list&utm_content=homepage) relacionadas a esse tema. Busquei no mercado algumas opções para tentar avaliar vantagens e desvantagens, mas no geral todas são bem parecidas. São elas: [SparkJava](http://sparkjava.com/), [SpringBoot](http://projects.spring.io/spring-boot/#quick-start), [Vertx.IO](http://vertx.io/) e por fim o [KulumuzEE](https://ee.kumuluz.com/).

Darei uma atenção especial a esse último pois aqui já entra um diferencial dele. Dentre os frameworks de mercado que estudei, ele é o primeiro voltado para o uso da API JavaEE. Portanto, implementamos nosso serviço em um container microservice utilizando especificações já conhecidas como JAX-RS, JPA, CDI. Além disso, ele foi o vencedor do [Duke's Choice Award 2015](https://www.oracle.com/corporate/pressrelease/dukes-award-102815.html). [Há outros também](http://blog.eisele.net/2015/10/wildfly-swarm-jax-rs-microservice-on-docker.html), como por exemplo o [Swarm](http://wildfly-swarm.io/) e o [RESTlet](https://restlet.com/technical-resources/restlet-framework/guide/2.3/introduction/features).

Não há muita dificuldade em fazer o HelloWorld dos frameworks, todos eles têm um exemplo na página do produto, além de serem mavenizados. Fiquei um pouco mais perdido na documentação do Vertx.IO, mas eles têm um [GitHub com exemplos que ajuda bastante](https://github.com/vert-x3/vertx-examples/blob/master/web-examples/src/main/java/io/vertx/example/util/Runner.java).

Muita [gente boa](http://www.devmedia.com.br/dominando-o-spring-boot/33654) já [fez artigo sobre esses caras](http://blog.arungupta.me/wildfly-swarm-microservices-javaee/), então a intenção aqui é apresentar uma visão geral sobre cada um e o código está no [meu GitHub](https://github.com/raphaelLacerda/microservice). A ideia básica da maioria dos frameworks é ter uma classe com um [main](http://sparkjava.com/) e conseguir executá-la. A partir daí, temos um servidor e uma aplicação "deployada".

## vamos para o Kumuluz...

O kumuluz possui uma [documentação muito bem detalhada](https://ee.kumuluz.com/tutorial/2015/06/04/microservices-with-java-ee-and-kumuluzee.html), trazendo diversos exemplos das API's, das quais irei focar em CDI, [JPA](https://blog.caelum.com.br/jpa-hibernate-ou-eclipselink/) e JAX-RS. A execução do Kumuluz é um pouco diferente dos outros pois temos que fazer o clean install da aplicação mavenizada e subir a aplicação por linha de comando

\[code\] java -cp target/classes:target/dependency/\* com.kumuluz.ee.EeApplication \[/code\]

Tive uma dificuldade inicial em configurar o [pom](https://github.com/raphaelLacerda/microservice/blob/master/kumuluz/pom.xml) com as versões adequadas. No nosso projeto, como ele não gera um WAR ao final, as páginas [HTML ficam dentro do src/main/resources/webapp](https://github.com/raphaelLacerda/microservice/tree/master/kumuluz/src/main/resources/webapp). De resto, é tudo igual a uma aplicação JavaEE convencional, [persistence.xml e beans.xml dentro do META-INF](https://github.com/raphaelLacerda/microservice/tree/master/kumuluz/src/main/resources/META-INF) e pronto, temos a configuração base da aplicação.No meu código de exemplo usei o [H2](https://github.com/raphaelLacerda/microservice/blob/master/kumuluz/src/main/resources/META-INF/persistence.xml) como banco para facilitar os testes.

Dividi o projeto em três pacotes para facilitar o aprendizado: [servlet](https://github.com/raphaelLacerda/microservice/tree/master/kumuluz/src/main/java/br/com/bb/servlet), [rest](https://github.com/raphaelLacerda/microservice/tree/master/kumuluz/src/main/java/br/com/bb/rest) e [jpa](https://github.com/raphaelLacerda/microservice/tree/master/kumuluz/src/main/java/br/com/bb/jpa). Siga as instruções

1. Baixe o projeto - git clone https://github.com/raphaelLacerda/microservice.git
2. Faça o clean install
3. Entre na pasta do Kumuluz e rode o comando para subir o projeto
4. Acesse localhost:8080

Incrível não? Não tenho mais o que ensinar pois ele usa as principais especificações do JavaEE, facilitando muito o aprendizado. Ele não tem suporte ainda para EJB's, mas você pode usar o [deltaspike](https://blog.caelum.com.br/integre-o-deltaspike-ao-seu-projeto-javaee/) para fazer alguns controles, como o transacional por exemplo.

Falarei mais sobre os outros em próximos posts...

## e o futuro...

Esses dias no trabalho fui surpreendido com a possibilidade do [PostgreSQL expor os dados via REST](https://github.com/begriffs/postgrest). Isso deu um nó enorme na minha cabeça pensando se fazia sentido ou não _(WTF, cadê meu JPA?)_. Há outros questionamentos também, estamos entrando em um mundo no qual o EAR já deixou de ser necessário e talvez agora o WAR. Diante disso, a tendência é que os _applications servers_ mais robustos passem a ter sua utilização repensada também. O lema que muitos apontam ["One JAR to rule them all"](http://www.lordofthejars.com/2014/07/rxjava-java8-java-ee-7-arquillian-bliss.html) pode ser a nova "_buzzphrase_". Aguardem os próximos!!

 

 

 

 

## Atualização

Para usuários Windows, seguir [este link](https://github.com/TFaga/KumuluzEE/issues/16) para subir o projeto.
