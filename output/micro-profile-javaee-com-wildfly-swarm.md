---
title: "Micro Profile JavaEE com Wildfly Swarm"
date: "2016-10-19"
author: "fernando.furtado"
authorEmail: "fernando.furtado@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Foi-se o tempo em que desenvolver uma aplicação JavaEE era algo extremamente burocrático e que tínhamos um servidor de aplicação inchado e com um consumo excessivo de recursos.

Hoje temos servidores que foram pensado na reutilização dos recursos e ainda temos a possibilidade de escolher quais módulos vamos inicializar junto com o servidor de aplicação, os chamados **profiles**. Mas e quando pensamos em micro-serviço? Como podemos trabalhar com micro-serviços no JavaEE?

A ideia do micro-serviço é de ter serviços isolados e independentes. Até aí tudo bem, podemos criar aplicações JavaEE separadas(isto é cada uma com seu `.war`). Mas como podemos fazer o deploy dessas aplicações?

Podemos ter um único contêiner (servidor de aplicação) para onde efetuamos o deploy de todas as aplicações. Mas com isso estamos indo de frente com a ideia de micro-serviço. Pois estamos gerando um ponto único de falha ([SPOF](https://en.wikipedia.org/wiki/Single_point_of_failure)).

Em outras palavras se precisarmos por alguma razão interromper o contêiner, todas as aplicações penduradas nele cairão também.

Outra alternativa seria cada serviço ter seu próprio contêiner, mas dessa forma estaríamos subutilizando o próprio contêiner. Pois nele temos diversos serviços rodando e que nossas aplicações não iriam precisar.

\[caption id="attachment\_7870" align="aligncenter" width="750"\][![Fonte: http://wildfly-swarm.io](https://blog.caelum.com.br/wp-content/uploads/2016/10/monolithic-as-300x73.png)](https://blog.caelum.com.br/wp-content/uploads/2016/10/monolithic-as.png) Fonte: http://wildfly-swarm.io \[/caption\]

Se não tivessemos usando servidor de aplicação poderiamos usar um contêiner embutido na aplicação (Tomcat, Jetty, Undertown e etc..). Porém dessa forma perderiamos as facilidades que o servidor de aplicação nos proporcionam.

E aí que entra uma ferramenta nova para nos ajudar, [Wildfly-Swarm](http://wildfly-swarm.io/).

Com ele podemos declarar quais "módulos" da plataforma JavaEE queremos utilizar.

A partir daí é desenvolver sua aplicação JavaEE normalmente e ao empacotar nossa aplicação, o wildfly-swarm pegará nosso pacote `.war` e irá embrulhar em um pacote dele com um contêiner micro para rodar nossa aplicação. Daí o nome de **Micro-Profile**.

Esse artefato final (o pacote que foi gerado a partir do nosso `.war`) é um arquivo `.jar` e podemos executar com um simples comando `java -jar` no nosso terminal.

Wildfly-Swarm usa o conceito de _UberJar_ para gerar o artefato final. Que nada mais é do que um arquivo `.jar` que contém além do seu `.war` todas as dependências necessárias para que o wildfly-swarm consiga rodar.

Logo um _UberJar_ é um arquivo `.jar` um pouco mais inchado mas somente com o necessário para rodar a aplicação.

\[caption id="attachment\_7871" align="aligncenter" width="300"\][![Fonte: http://wildfly-swarm.io](https://blog.caelum.com.br/wp-content/uploads/2016/10/swarm-uberjar-300x300.png)](https://blog.caelum.com.br/wp-content/uploads/2016/10/swarm-uberjar.png) Fonte: http://wildfly-swarm.io\[/caption\]

O mínimo necessário para rodar o wildfly-swarm é _JDK8_ e _Maven_ ou _Gradle_.

No wildfly-swarm temos o conceito de **fraction**, que nada mais é do que uma funcionalidade/configuração do servidor de aplicação. Na maioria das vezes um _fraction_ pode ser comparado (mapeado) com um subsystem do servidor de aplicação (ex.: Datasource, Driver, Pool, socket-binding e etc...), temos outros casos em que um _fraction_ é uma funcionalidade que antes não tínhamos (nativamente) no servidor de aplicação (Ex.: Jolokia, Spring, NetflixOSS ).

Para usar o wildfly precisamos importar um `pom.xml` do wildfly-swarm no nosso projeto, e adicionar um plugin que fará a geração do _UberJar_.

Nesse post vamos construir uma simples aplicação rest usando o wildfly-swarm. Vamos lá!

Com o projeto (web) _maven_ criado precisamos importar o `pom.xml` do wildfly-swarm ao nosso projeto. Esse `pom.xml` é importado declarando uma denpendencia gerenciada e declarando que o escopo da mesma será **import**.

Para não ficar espalhando a versão do wildfly-swarm estamos utilizando por todo nosso `pom.xml` vamos declarar uma propriedade com ela. Além disso já vamos definir a versão do Java que iremos utilizar, e como se trata de um projeto web teoricamente precisariamos de um arquivo `web.xml` porém não é necessário para nosso caso. Então vamos definir que não deve ser gerado um erro caso não exista o arquivo `web.xml`.

\[code language="xml"\] <properties> <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> <maven.compiler.source>1.8</maven.compiler.source> <maven.compiler.target>1.8</maven.compiler.target> <failOnMissingWebXml>false</failOnMissingWebXml> <version.wildfly.swarm>2016.9</version.wildfly.swarm> </properties> \[/code\]

Agora vamos importar o arquivo `pom.xml` do wildfly-swarm que tem o nome de **BOM** (Bill of Materials).

\[code language="xml"\] <dependencyManagement> <dependencies> <dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>bom</artifactId> <version>${version.wildfly.swarm}</version> <scope>import</scope> <type>pom</type> </dependency> </dependencies> </dependencyManagement> \[/code\]

Além disso precisamos adicionar o plugin que irá gerar o _UberJar_ baseado no nosso `war`.

\[code language="xml"\] <plugin> <groupId>org.wildfly.swarm</groupId> <artifactId>wildfly-swarm-plugin</artifactId> <version>${version.wildfly.swarm}</version> <executions> <execution> <goals> <goal>package</goal> </goals> </execution> </executions> </plugin> \[/code\]

Precisamos também adicionar a dependência referente à api do JavaEE e esta será provida pelo wildfly-swarm.

\[code language="xml"\] <!--Java EE Api --> <dependency> <groupId>javax</groupId> <artifactId>javaee-api</artifactId> <version>7.0</version> <scope>provided</scope> </dependency> \[/code\]

E esse é o setup para utilizar o wildfly-swarm, ao final teriamos o nosso arquivo `pom.xml` mais ou menos da seguinte maneira:

\[code language="xml"\] <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"> <modelVersion>4.0.0</modelVersion> <groupId>br.com.caelum</groupId> <artifactId>livraria</artifactId> <version>1.0</version> <packaging>war</packaging>

<properties> <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding> <maven.compiler.source>1.8</maven.compiler.source> <maven.compiler.target>1.8</maven.compiler.target> <failOnMissingWebXml>false</failOnMissingWebXml> <version.wildfly.swarm>2016.9</version.wildfly.swarm> </properties>

<dependencyManagement> <dependencies> <dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>bom</artifactId> <version>${version.wildfly.swarm}</version> <scope>import</scope> <type>pom</type> </dependency> </dependencies> </dependencyManagement>

<dependencies>

<!--Java EE Api --> <dependency> <groupId>javax</groupId> <artifactId>javaee-api</artifactId> <version>7.0</version> <scope>provided</scope> </dependency>

</dependencies>

<build> <finalName>livraria</finalName> <plugins> <plugin> <groupId>org.wildfly.swarm</groupId> <artifactId>wildfly-swarm-plugin</artifactId> <version>${version.wildfly.swarm}</version> <executions> <execution> <goals> <goal>package</goal> </goals> </execution> </executions> </plugin> </plugins> </build>

</project> \[/code\]

Agora vamos adicionar as dependências que queremos do servidor de aplicação, para o nosso caso vamos adicionar, **jax-rs**, **cdi**, **ejb**, **jpa**, **datasources**.

\[code language="xml"\]

<!--Swarm Dependencies -->

<dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>jaxrs-cdi</artifactId> </dependency>

<dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>ejb</artifactId> </dependency>

<dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>jpa</artifactId> </dependency>

<dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>datasources</artifactId> </dependency>

\[/code\]

Vamos começar a "codar" nosso projeto de livraria.

Vamos iniciar criando nossas classes de domínio **Livro** e **Autor** e seus respectivos **DAOs**

\[code language="java"\] @Entity public class Livro { @Id @GeneratedValue(strategy=GenerationType.IDENTITY) private Long id; private String titulo; private String descricao; @ManyToMany private List<Autor> autores; @Deprecated private Livro(){} public Livro(Long id, String titulo, String descricao, List<Autor> autores) { this.id = id; this.titulo = titulo; this.descricao = descricao; this.autores = autores; } public Long getId() { return id; } public String getTitulo() { return titulo; } public String getDescricao() { return descricao; }

public List<Autor> getAutores() { return autores; }

public void adicionaAutor(Autor autor) { this.autores.add(autor); } } \[/code\]

\[code language="java"\] @Entity public class Autor { @Id @GeneratedValue(strategy=GenerationType.IDENTITY) private Long id; private String nome; @Deprecated private Autor(){} public Autor(Long id, String nome) { this.id = id; this.nome = nome; }

public Long getId() { return id; }

public String getNome() { return nome; } } \[/code\]

\[code language="java"\] public class LivroDAO {

@PersistenceContext private EntityManager manager;

public Livro buscaLivroComId(Long id) {

Livro livro = manager.createQuery("select l from Livro l left join fetch l.autores a where l.id = :id", Livro.class) .setParameter("id", id).getSingleResult();

return livro; }

public List<Livro> listaTodos() { return manager.createQuery("select l from Livro l left join fetch l.autores a", Livro.class).getResultList(); }

public void adicionar(Livro livro) { manager.persist(livro); }

public void atualiza(Livro livro) { manager.merge(livro); }

} \[/code\]

\[code language="java"\] public class AutorDAO {

@PersistenceContext private EntityManager manager;

public Autor buscaAutorComId(Long id) { return manager.find(Autor.class, id); }

public void adiciona(Autor autor) { manager.persist(autor); } } \[/code\]

Vamos criar uma classe para configurar o **JAX-RS** para receber requisições a partir da raiz:

\[code language="java"\] import javax.ws.rs.ApplicationPath; import javax.ws.rs.core.Application;

@ApplicationPath("/") public class JaxRsConfiguration extends Application{ } \[/code\]

Agora que já temos como receber requisições vamos criar nossos recursos que serão expostos pela nossa api **LivroResource** e **AutorResource** além disso já vamos marcar-los como **EJB Stateless** para que já tenhamos transação entre outras coisas disponíveis.

\[code language="java"\] @Path("livros") @Stateless public class LivroResource {

@Inject private LivroDAO livroDao;

@Path("{id:\\\\d+}") @GET @Produces(MediaType.APPLICATION\_JSON) public Livro buscaPorId(@PathParam("id") Long id){ return livroDao.buscaLivroComId(id); } @GET @Produces(MediaType.APPLICATION\_JSON) public List<Livro> listaDeLivros(){ return livroDao.listaTodos(); } @POST @Consumes(MediaType.APPLICATION\_JSON) public Response adicionaLivro(Livro livro){ livroDao.adicionar(livro); return Response .created(URI.create("/livros/" + livro.getId())) .entity(livro) .type(MediaType.APPLICATION\_JSON) .build(); } } \[/code\]

Nesse recurso temos as seguintes _URIs_

- _/livros_ - _GET_ (Retorna uma lista com todos os livros)
- _/livros/id_ - _GET_ (Retorna o livro com ID especificado)
- _/livros_ - _POST_ (Cria um novo livro)

\[code language="java"\] @Path("livros/{livroId:\\\\d+}/autores") @Stateless public class AutorResource { @Inject private LivroDAO livroDao; @Inject private AutorDAO autorDao; @GET @Produces(MediaType.APPLICATION\_JSON) public List<Autor> listaDeAutoresDoLivro(@PathParam("livroId") Long livroId){ return livroDao.buscaLivroComId(livroId).getAutores(); } @GET @Path("{autorId:\\\\d+}") @Produces(MediaType.APPLICATION\_JSON) public Autor autorDoLivro(@PathParam("autorId") Long autorId){ return autorDao.buscaAutorComId(autorId); } @POST @Consumes(MediaType.APPLICATION\_JSON) public Response adicionaAutor(@PathParam("livroId") Long livroId, Autor autor){ Livro livro = livroDao.buscaLivroComId(livroId); autorDao.adiciona(autor); livro.adicionaAutor(autor); livroDao.atualiza(livro); return Response .created(URI.create("/livros/" + livroId + "/autores/" + autor.getId()) ) .entity(autor) .type(MediaType.APPLICATION\_JSON) .build(); }

} \[/code\]

Nesse recurso temos as seguintes _URIs_

- _/livros/idDoLivro/autores_ - _GET_ (retorna todos os autores de um livro especifico)
- _/livros/idDoLivro/autores/idDoAutor_ - _GET_ (retorna um autor especifico de um livro especifico)
- _/livros/idDoLivro/autores_ - _POST_ (cria um novo autor associado ao livro)

Agora que temos nosso projeto pronto temos que registrar um **datasource** e associa-lo no nosso arquivo `persistence.xml` . Para o nosso caso, esta configuração será feita em uma classe com um método _main_.

Porém para configurar o **datasource** precisamos dizer qual o driver, e no nosso caso será um driver para mysql. para não precisarmos registrar um driver, podemos adicionar a dependência para esse driver diretamente no `pom.xml`

\[code language="xml"\] <dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>mysql</artifactId> <version>${version.wildfly.swarm}</version> </dependency> \[/code\]

Para que seja adicionado corretamente o driver para mysql precisamos excluir o driver default que vem quando adicionamos a dependência para **JPA** que é o **H2**. Para isso vamos alterar a dependência de **JPA** e remover o **H2**.

\[code language="xml"\] <dependency> <groupId>org.wildfly.swarm</groupId> <artifactId>jpa</artifactId> <exclusions> <exclusion> <groupId>org.wildfly.swarm</groupId> <artifactId>h2</artifactId> </exclusion> </exclusions> </dependency> \[/code\]

(Se não fizermos essa configuração ao subirmos o wildfly-swarm iremos receber um erro, informando que esta rolando um conflito entre essas dependências **H2**, **MYSQL**).

Pronto agora vamos registrar nosso **datasource** dentro de um método main:

\[code language="java"\] public class Boot { public static void main(String\[\] args) throws Exception { Swarm swarm = new Swarm(args); swarm.fraction( new DatasourcesFraction() .dataSource("swarmDs", (ds) -> { ds.driverName("mysql"); ds.connectionUrl("jdbc:mysql://localhost/livraria\_swarm?createDatabaseIfNotExist=true&&useSSL=false"); ds.userName("root"); })); swarm.start(); } } \[/code\]

Como estamos sobrescrevendo o comportamento default do wildfly-swarm precisamos ensinar ele como ele deve fazer o deploy da nossa aplicação (ou seja dentro do nosso `.war` quais classes devem estar disponíveis, quais arquivos e etc...).

Para fazer isso iremos usar uma biblioteca chamada **ShrinkWrap** da própria **JBoss** que serve para criar um pacote programaticamente, ela é muito utilizada quando estamos usando o **Arquillian** para testes de aceitação/integração.

Com ele podemos criar _JARArchive_ (`.jar`) e _WARArchive_ (`.war`). Além desses temos outros tipos mais especificos por exemplo _RibbonArchive_ um archive especifico que pode ser registrar em aplicações baseadas em Ribbon, _Secured_ que já injeta o arquivo keycloak.json e já configura a parte de seguraça.

Temos também um outro tipo especifico que é _JAXRSArchive_ que é um archive que já configura o **jax-rs** e faz o binding para classe de configuração `Application/@ApplicationPath`. Vamos utilizar esse archive.

Além disso precisamos dizer que ao fazer o deploy da aplicação seja levado os arquivos `persistence.xml` e `beans.xml` necessários para o funcionamento da **JPA** e **CDI**.

O arquivo `beans.xml` deve estar dentro do diretório `WEB-INF` a partir do classpath e o arquivo `persistence.xml`, deve estar em `META-INF` a partir do classpath também. E precisaremos configurar isso também.

\[code language="java"\] public class Boot { public static void main(String\[\] args) throws Exception { Swarm swarm = new Swarm(args); swarm.fraction( new DatasourcesFraction() .dataSource("swarmDs", (ds) -> { ds.driverName("mysql"); ds.connectionUrl("jdbc:mysql://localhost/livraria\_swarm?createDatabaseIfNotExist=true&&useSSL=false"); ds.userName("root"); })); swarm.start(); JAXRSArchive deployment = ShrinkWrap.create(JAXRSArchive.class); ClassLoader classLoader = Boot.class.getClassLoader(); deployment.addAsWebInfResource(classLoader.getResource("beans.xml"),"beans.xml"); deployment.addAsWebInfResource(classLoader.getResource("persistence.xml"),"classes/META-INF/persistence.xml"); deployment.addPackages(true, Package.getPackage("br.com.caelum.livraria")); deployment.addAllDependencies(); swarm.deploy(deployment); } } \[/code\]

Como ultima configuração precisamos indicar ao plugin do wildfly-swarm que ele não deve usar sua classe padrão para iniciar. Ele deve usar nossa classe **Boot** e com isso alterar o arquivo de manifesto para usar essa classe como ponto inical da nossa aplicação. Vamos alterar a declaração do plugin no arquivo `pom.xml` e adicionar a configuração \`\`.

\[code language="xml"\] <plugin> <groupId>org.wildfly.swarm</groupId> <artifactId>wildfly-swarm-plugin</artifactId> <version>${version.wildfly.swarm}</version> <executions> <execution> <goals> <goal>package</goal> </goals> </execution> </executions> <configuration> <mainClass>br.com.caelum.livraria.Boot</mainClass> </configuration> </plugin> \[/code\]

Para que seja gerado o _UberJar_ devemos executar o goal `package` do _maven_ (Ex.: `mvn package`). Ao termino dessa execução no diretório target teremos um arquivo `livraria.war` e um `livraria-swarm.jar` (entre outros arquivos).

O arquivo `livraria-swarm.jar` é o nosso _UberJar_.

Para executarmos podemos faze-lo pelo plugin do _maven_ através de `mvn wildfly-swarm:run` ou executando o comando `java -jar livraria-swarm.jar`.

Dessa forma temos uma aplicação JavaEE somente com o mínimo necessário e um contêiner bem mais leve para rodar a mesma.

## Aprendendo mais

Quer aprender mais sobre como utilizar a especificação **JavaEE**?

Não deixe de conferir nosso curso [Plataforma JavaEE](https://www.caelum.com.br/curso-plataforma-java-ee/), nele abordamos **jax-rs**, **jpa**, **cdi**, **ejb** entre outros recursos da especificação **JavaEE**.

E aí o que você achou do **wildfly-swarm**?
