---
title: "Acessando múltiplos bancos de dados com JPA e CDI"
date: "2015-02-03"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

É bem comum que uma aplicação precise acessar vários bancos de dados para prover suas funcionalidades. Um exemplo clássico dessa situação encontramos em empresas que possuem muitos sistemas, e para evitar ter tabelas e registros duplicados em várias bases de dados distintas, criam as chamadas **bases corporativas**, responsáveis por conter as informações comuns a todos os sistemas.

Supondo que temos uma aplicação `Java`, em um ambiente JavaEE, utilizando a `JPA` para fazer acesso aos bancos de dados, considere as seguintes configurações no arquivo `persistence.xml`:

\[xml highlight="7,9,18,20"\] <?xml version="1.0" encoding="UTF-8"?> <persistence version="2.1" xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence\_2\_1.xsd">

<persistence-unit name="app" transaction-type="JTA"> <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider> <jta-data-source>java:/appDS</jta-data-source> <properties> <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5InnoDBDialect" /> <!-- outras configuracoes... --> </properties> </persistence-unit>

<persistence-unit name="corporativo" transaction-type="JTA"> <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider> <jta-data-source>java:/corporativoDS</jta-data-source> <properties> <property name="hibernate.dialect" value="org.hibernate.dialect.MySQL5InnoDBDialect" /> <!-- outras configuracoes... --> </properties> </persistence-unit> </persistence> \[/xml\]

Repare que temos dois `persistence-unit's` configurados, sendo um chamado `app`, que utiliza o `DataSource` da nossa aplicação, e o outro chamado `corporativo`, este utilizando o `DataSource` corporativo.

Agora considere que temos a seguinte classe `DAO` em nossa aplicação:

\[java\] public class ProdutoDao {

private final EntityManager manager;

public ProdutoDao(EntityManager manager) { this.manager = manager; }

public List<Produto> todosOrdenadosPeloNome() { String jpql = "FROM Produto p ORDER BY p.nome"; return manager.createQuery(jpql, Produto.class).getResultList(); }

//outros metodos... } \[/java\]

Repare que a classe possui um construtor esperando como parâmetro uma instância de um `EntityManager`. Se nossa aplicação estiver utilizando também o **CDI**, podemos indicar que ele será o responsável por obter esta instância do `EntityManager`, com o uso da annotation `@Inject`:

\[java highlight="5"\] public class ProdutoDao {

private final EntityManager manager;

@Inject public ProdutoDao(EntityManager manager) { this.manager = manager; }

//restante do codigo... } \[/java\]

Entretanto agora teremos um problema, pois o CDI não sabe como obter uma instância de um `EntityManager`. Podemos resolver este problema utilizando o recurso de **Producer Method** do CDI, que nada mais é do que um método anotado com `@Produces`, para ensiná-lo a como obter uma instância de um objeto do tipo `EntityManager`. Isto pode ser feito criando-se uma classe como a seguinte:

\[java highlight="6,9,10,15"\] @ApplicationScoped public class EntityManagerProducer implements Serializable {

private static final long serialVersionUID = 1L;

@PersistenceUnit private EntityManagerFactory factory;

@RequestScoped @Produces public EntityManager createEntityManager() { return factory.createEntityManager(); }

public void closeEntityManager(@Disposes EntityManager manager) { if (manager.isOpen()) { manager.close(); } }

} \[/java\]

Pronto! Agora o CDI sabe que deverá chamar o método `createEntityManager` para obter uma instância de um `EntityManager`, e que esta instância deverá ser mantida apenas pelo tempo de um `request`.

Note que no código anterior existe também um método chamado `closeEntityManager`, que possui um parâmetro do tipo `EntityManager` anotado com `@Disposes`. Isto indica ao CDI que este método deverá ser chamado sempre que ele for descartar uma instância de um EntityManager.

Mesmo assim ainda teremos problemas, pois utilizamos a annotation `@PersistenceUnit` para injetar uma instância de um `EntityManagerFactory`, mas como temos dois `persistence-unit's` configurados, devemos informar para qual `persistence-unit` a factory será criada. Além disso, nossa aplicação precisará acessar dois bancos de dados distintos, e sendo assim precisaremos de dois `EntityManager's` e duas `EntityManagerFactory's` distintas. Atualizando o código anterior teremos o seguinte:

\[java highlight="6,9"\] @ApplicationScoped public class EntityManagerProducer implements Serializable {

private static final long serialVersionUID = 1L;

@PersistenceUnit(unitName = "app") private EntityManagerFactory appFactory;

@PersistenceUnit(unitName = "corporativo") private EntityManagerFactory corporativoFactory;

@RequestScoped @Produces public EntityManager createAppEntityManager() { return appFactory.createEntityManager(); }

@RequestScoped @Produces public EntityManager createCorporativoEntityManager() { return corporativoFactory.createEntityManager(); }

public void closeEntityManager(@Disposes EntityManager manager) { if (manager.isOpen()) { manager.close(); } }

} \[/java\]

E por fim um último problema a ser resolvido =D Como temos dois métodos `producer's` de `EntityManager's`, ao tentar injetar uma instância de EntityManager em nossa classe DAO, o CDI não saberá qual dos dois métodos `producer's` deverá chamar, e com isso lançará uma `Exception` indicando o problema de ambiguidade.

Este problema pode ser resolvido utilizando outro recurso do CDI chamado **Qualifier**. Um `qualifier` nada mais é do que uma `Annotation` que funciona como uma espécie de `Alias`, para que assim possamos identificar qual `producer` deve ser chamado em um determinado ponto de injeção.

Criaremos uma annotation chamada `@Corporativo`, e para indicar que esta annotation é um qualifier do CDI, basta anotá-la com `@Qualifier`:

\[java highlight="3"\] @Retention(RetentionPolicy.RUNTIME) @Target({ElementType.METHOD, ElementType.FIELD, ElementType.PARAMETER}) @Qualifier public @interface Corporativo { } \[/java\]

Agora devemos usar nosso qualifier no método producer:

\[java highlight="6,13"\] @ApplicationScoped public class EntityManagerProducer implements Serializable {

@RequestScoped @Produces @Default public EntityManager createAppEntityManager() { return appFactory.createEntityManager(); }

@RequestScoped @Produces @Corporativo public EntityManager createCorporativoEntityManager() { return corporativoFactory.createEntityManager(); }

//restante do codigo omitido...

} \[/java\]

Repare que adicionamos nosso qualifier no método `createCorporativoEntityManager`. Note também que no método `createAppEntityManager` adicionamos a annotation **@Default**, que nada mais é do que um `qualifier` do próprio CDI, e serve para indicar que este é o método producer a ser chamado quando não especificarmos um qualifier em algum ponto de injeção.

Agora a classe `DAO` funciona normalmente, sendo que nela será injetado o EntityManager que acessa o banco de dados da aplicação, pois nenhum qualifier foi utilizado. E para injetar o EntityManager que acessa o banco de dados corporativo, basta alterar o construtor da classe DAO adicionando o qualifier `@Corporativo` no parâmetro do construtor:

\[java highlight="6"\] public class ProdutoDao {

private final EntityManager manager;

@Inject public ProdutoDao(@Corporativo EntityManager manager) { this.manager = manager; }

//restante do codigo... } \[/java\]

A injeção também pode ser feita via atributo, ao invés do construtor:

\[java\] public class ProdutoDao {

@Inject @Corporativo private EntityManager manager;

//restante do codigo... } \[/java\]

É possível também injetar os dois `EntityManager's` na mesma classe `DAO`:

\[java\] public class ProdutoDao {

@Inject @Corporativo private EntityManager corporativoManager;

@Inject private EntityManager appManager;

//restante do codigo... } \[/java\]

Estes e outros recursos do CDI e JPA são apresentados em nossos cursos [Web rica com JSF 2, Primefaces 4 e CDI](http://www.caelum.com.br/curso-java-web-jsf-cdi/) e [Persistência com JPA, Hibernate e EJB lite](http://www.caelum.com.br/curso-persistencia-java-jpa-hibernate/).

E você, já precisou utilizar os recursos Producer/Qualifier do CDI para acessar vários bancos de dados na mesma aplicação?
