---
title: "As novidades do Hibernate 4"
date: "2012-02-07"
author: "hannelita"
authorEmail: "hannelita@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Um dos mais badalados frameworks de ORM no mundo Java (e popular também no .NET), o Hibernate recentemente ganhou sua versão 4.0 Final, que chega para [arrebentar de novidades](http://in.relation.to/Bloggers/WhatsNewInHibernateCore40). O framework surgiu em 2001, por iniciativa de Gavin King, e logo se tornou amplamente utilizado devido a uma grande diversidade de recursos para mapeamento objeto relacional. Com a especificação da JPA (Java Persistence API), solidificou-se como  a implementação mais utilizada. Diversos projetos também passaram a ser desenvolvidos pela equipe do Hibernate a fim de aprimorar os recursos existentes no framework. Muitos deles, em vez de serem anexados ao projeto Core, tornaram-se plugins, tais como [Hibernate Search](http://hibernate.org/subprojects/search.html), o [Hibernate OGM](http://hibernate.org/subprojects/ogm.html) e o [Hibernate Validator](http://hibernate.org/subprojects/validator.html). Este último, inclusive, teve fortes influências para a criação de uma nova especificação, a Bean Validation (JSR 303).

A partir da [versão 3.5](https://blog.caelum.com.br/as-dependencias-do-hibernate-3-5/), o Hibernate tornou-se uma implementação certificada para a JPA2 ([JSR 317](http://jcp.org/en/jsr/detail?id=317)), lançada oficialmente no final de 2009. Várias annotations surgiram e muitos recursos foram aprimorados. Em dezembro de 2011, a versão 4.0 Final veio à tona, trazendo algumas importantes novidades que gostaríamos de destacar:

## Redesign da SessionFactory

Possivelmente essa é uma das grandes mudanças no Hibernate 4. Estávamos habituados a obter uma `SessionFactory` da seguinte forma:

\[java\] private static final SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory(); \[/java\]

Entretanto, agora o método `buildSessionFactory()` está listado como deprecated, bem como as classes de `Configuration`: `org.hibernate.cfg.Configuration` e `AnnotationConfiguration`. A nova forma de se obter uma `SessionFactory` é através do `ServiceRegistry`, que explicaremos adiante, lançado para esta versão 4:

\[java\] ServiceRegistry serviceRegistry = new ServiceRegistryBuilder() .configure().buildServiceRegistry(); MetadataSources metadataSources = new MetadataSources(serviceRegistry); metadataSources.addResource("algum.hbm.xml") .addAnnotatedClass(SuaEntidade.class); Metadata metadata = metadataSources.buildMetadata(); SessionFactory sessionFactory = metadata.buildSessionFactory(); \[/java\]

Para que possamos obter uma `SessionFactory`, precisamos de um `ServiceRegistry`, seguido de um `MetadataSources` (eventualmente podemos adicionar recursos, classes anotadas, entre outros, de forma similar ao `Configuration`); a partir deste obtemos um `Metadata` e então finalmente conseguimos uma `SessionFactory`.

Para obter uma `EntityManagerFactory` nada muda, podemos usufruir da classe `javax.persistence.Persistence`.

## ServiceRegistry

Uma nova forma foi implementada para que o Hibernate gerencie seus serviços. Services são classes que fornecem ao Hibernate diversos tipos de funcionalidades, tais como `ClassLoader`, conexão com o banco (`ConnectionProvider`), descoberta de dialetos apropriados, entre outros. Você deve estar se perguntando por que isso é um ponto interessante. O fato chave é que agora todos os serviços possuem uma interface. Caso queira, você pode mudar a forma com que o Hibernate realiza algumas funcionalidades de uma maneira muito simples, apenas fazendo com que suas classes implementem a interface de serviço do Hibernate. É uma forma de desacoplar seu código interno e se tornar mais maleável.

## Suporte a Multi-Tenancy

Para que não conhece o termo, aí vai um pequeno resumo - em um modelo multi-tenant, é possível compartilhar os mesmos recursos físicos para clientes/empresas diferentes, mas ao mesmo tempo permite-se que fiquem logicamente isolados. A frase anterior pode parecer abstrata, então imagine diversas aplicações de e-commerce. Muitas delas possuem um core muito similar e diferem apenas em questões de layout e UI. Imagine então que fosse criada uma base de código igual para todas essas lógicas de e-commerce e apenas o design de cada loja diferente fosse mudado; cada uma delas teria seu próprio `namespace` e um endereço na web; contudo a base de código seria a mesma. Esses websites de lojas de e-commerce seriam cada um, um tenant.

Podemos implementar o Multi-Tenancy de algumas formas: Instâncias de bancos separadas (cada tenant possui sua prórpia instância de banco); Schema separados (os tenants compartilham o mesmo banco físico, entretanto cada um deles possui um schema); e também podemos implementar o Multi-Tenancy através de Particionamento (usamos o mesmo banco físico e o mesmo Schema), onde os tenants são particionados de acordo com algum discriminator value (uma chave que apareceria em toda as tabelas) e uma única tabela guarda os dados de cada um deles. O Hibernate 4 dá suporte a esta última opção de maneira fácil. Para este caso, tomemos como exemplo uma tabela no banco denominada Cliente:

\[code\] CLIENTE ( ID BIGINT, NOME VARCHAR, ... TENANT\_ID VARCHAR (ou uma chave estrangeira) ) \[/code\]

Note que precisamos do campo `TENANT_ID`, pois ele define a qual tenant nos referimos. Em um exemplo real, esse ID vai dizer a qual loja pertence determinado produto, por exemplo. Essa abordagem via Particionamento ainda dá suporte a cache de segundo nível.

Mas será que sempre precisaremos nos lembrar de criar um TENANT\_ID em nossas tabelas? Como poderíamos ter algo como:

\[code\] CLIENTE ( ID BIGINT, NOME VARCHAR, ... ) \[/code\]

sem termos um `TENANT_ID`? Poderíamos ter schemas separados e criarmos uma `SessionFactory` para cada tenant. Entretanto, isso seria inviável se tivéssemos muitos tenants, pois muitas `SessionFactories` podem ser extremamente custosas para a memória. Uma outra ideia seria utilizarmos uma abordagem conhecida como [application-supplied connections](http://relation.to/Bloggers/MultitenancyInHibernate), onde abrimos uma Session em cada SessionFactory através de uma Connection específica que fornecemos. Podemos dizer ao Hibernate qual Connection utilizar em cada contexto. Lembra-se de quando explicamos o ServiceRegistry? Um dos Services, é o ConnectionProvider. Separando os schemas evitamos a necessidade de um `TENANT_ID` em cada tabela, mas perdemos a possibilidade de usar o cache de segundo nível de maneira fácil, além de ser mais trabalhoso realizar relatórios e estatísticas com mais de um cliente.

O Hibernate 4 fornece essas alternativas básicas para o cenário multi-tenant, de forma quase transparente.

## Usando o Hibernate 4

Quer ver o Hibernate 4 em ação? A maneira mais fácil de colocá-lo em seu projeto é através do Maven:

\[xml\] <dependency> <groupId>org.hibernate</groupId> <artifactId>hibernate-core</artifactId> <version>4.0.1.Final</version> </dependency> <dependency> <groupId>org.hibernate</groupId> <artifactId>hibernate-entitymanager</artifactId> <version>4.0.1.Final</version> </dependency> <dependency> <groupId>org.hibernate</groupId> <artifactId>hibernate-annotations</artifactId> <version>3.5.6-Final</version> </dependency> \[/xml\]

Se você utiliza alguma outra ferramenta de build, [aqui você encontra todas as dependências necessárias](http://mvnrepository.com/artifact/org.hibernate) declaradas para Ivy, Gradle, SBT, entre outros.

Você também pode baixar os jars necessários [para usá-los diretamente](http://sourceforge.net/projects/hibernate/files/hibernate4/4.0.0.Final/hibernate-release-4.0.0.Final.zip/download). Caso você use o JBoss AS 7 ([conheça mais sobre essa nova versão do servidor](https://blog.caelum.com.br/jboss-as-7-inovacao-nos-servidores-java-ee/)), a versão 7.1 (ainda em Beta) já vem com o Hibernate 4 Final. Se você preferir o cloud, pode experimentar o Hibernate 4 no Openshift ([aqui há um tutorial no blog da Caelum](https://blog.caelum.com.br/screencast-sua-app-no-cloud-com-openshift/)).

Gostou? Há muito mais nos links abaixo:

Webinar de Hibernate 4 - [link](http://www.youtube.com/watch?v=sBBVwOC9o_Y) Hibernate 4 - post oficial do [in.relation.to](http://relation.to/Bloggers/HibernateCore40IsFinal) Multi-Tenancy - mais informações ([tutorial introdutório](http://www.developer.com/design/article.php/3801931/Introduction-to-Multi-Tenant-Architecture.htm)) Multi-Tenancy e Hibernate 4 - [Perguntas e Respostas](https://community.jboss.org/wiki/Multi-tenancyDesign)

Também não deixe de visitar [os vários posts da Caelum](https://blog.caelum.com.br/tag/hibernate/) sobre Hibernate, inclusive o conhecido [7 hábitos dos desenvolvedores Hibernate e JPA altamente eficazes](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/). Esperamos você no [nosso curso de Hibernate e JPA](http://www.caelum.com.br/curso/fj-25-persistencia-jpa2-hibernate/)!
