---
title: "Hibernate Search com Lucene"
date: "2007-06-14"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O [Hibernate Search](http://www.hibernate.org/410.html) é uma "full text search engine" que tem sua implementação escrita em cima do Apache [Lucene](http://lucene.apache.org/java/docs/index.html).

O Lucene por si só já é bem famoso pelas [funcionalidades oferecidas](http://Lucene.apache.org/java/docs/features.html#Features), como buscas aproximadas, índices em memória, e velocidade. A parte mais complicada é a configuração e preparação (indexação) dos dados que são necessários para executar a busca. Aqui entra o Hibernate Search: ele te poupa desse trabalho e integra o Lucene muito bem com o Hibernate, dada a arquitetura de listeners e callbacks deste projeto. Com ele podemos buscar textualmente pelas entidades, usando as funcionalidades do Lucene sem a dor de cabeça da criação de `Documents`, definição dos tipos de Fields, controle de acesso concorrente aos índices e outros pequenos detalhes que aparecem ao usar o projeto diretamente. Vamos ver um exemplo simples como configurar e usar o Hibernate Search.

**Anotações**

Tanto nas Hibernate Annotations quanto no Hibernate Search você passa a maioria das configurações usando anotações no seu modelo de domínio. Para poder busca uma entidade você deve anota-lá com `@Indexed`. Usando esta anotação o Lucene irá analisar os dados da entidade e indexa-los, pois um interceptador do Hibernate fará esse trabalho por de trás dos panos. Quais dados você pretende indexar e como deve ser especificado anotando o atributo com `@Field`. Se você não usar nenhuma anotação nenhum dado será indexado.

\[java\] @Entity @Indexed public class Dvd {

@Id @GeneratedValue private Long id;

@Field(index=Index.TOKENIZED,store=Store.YES) private String title;

@Field(index=Index.TOKENIZED) private String description; // possivelmente alguns getters e setters } \[/java\] **Index**

Usamos no exemplo os atributos `index` e `store` na anotação `@Field`. Quando Lucene indexa um campo, ele salva os dados num formato especial para ele poder executar suas buscas. Por exemplo, se você usa o título _"Rambo 2"_, o Lucene indexa _Rambo_ e _2_ separado. Esse tipo de indexação se chama `TOKENIZED`. Outros tipos são `Index.NO` (não será indexado e não tem como buscar) e `Index.UN_TOKENIZED` (não será "separado", útil para campos considerado chaves). Essa nomenclatura é bem familiar para quem já usou o Lucene diretamente.

**Store**

Indica que Lucene deve gravar os dados (o título do DVD, no caso) no índice, de tal forma que esse valor possa ser recuperado integralmente diretamente pelos indices. Quando Lucene procura o título, ele pode devolver diretamente o valor, porque usamos Store.YES. É mais rápido, pois o hibernate não precisará consultar o banco para buscar aquele atributo, mas precisa mais espaço no disco e de certo forma é uma redundância de informação.

**Eventos e diretórios**

Hibernate Search encapsula o trabalho de indexação. Somente precisamos avisar o Hibernate Search em que momento nós desejaremos indexar as entidades em questão. Para isso existem listeners que podem ser configurados no `hibernate.cfg.xml`:

\[xml\] <session-factory> ... <event type="post-update"> <listener class="org.hibernate.search.event.FullTextIndexEventListener"/> </event> <event type="post-insert"> <listener class="org.hibernate.search.event.FullTextIndexEventListener"/> </event> <event type="post-delete"> <listener class="org.hibernate.search.event.FullTextIndexEventListener"/> </event> ... </session-factory> \[/xml\]

Definimos 3 listeners globais. Hibernate os ativa automaticamente quando o tipo de evento acontecer. Nesse caso qualquer alteração numa entidade faz com que o Lucene altere o índice.

Além disso temos que definir um provedor que cria os arquivos e diretórios que serão usados para os índices do Lucene:

\[xml\] <session-factory> ... <property name="hibernate.search.default.directory\_provider"> org.hibernate.search.store.FSDirectoryProvider </property> <property name="hibernate.search.default.indexBase"> /home/nico/workspace/mydvds/webapp/indexes </property> ... </session-factory> \[/xml\]

Existem outros provedores como `RAMDirectoryProvider` que somente grava na memória.

**A busca**

Configurado o Hibernate Search, vamos criar a busca. Você também usa um objeto session/query para fazer suas buscas, na verdade a sessão do Hibernate Search encapsula uma do hibernate. Para criar uma sessão usamos o método estático `createFullTextSession` da classe `org.hibernate.search.Search` e passamos a sessão do Hibernate como argumento:

\[java\] FullTextSession LuceneSession = Search.createFullTextSession(hibernateSession); \[/java\]

Com a sessão na mão podemos criar um `org.hibernate.search.FullTextQuery` do hibernate search:

\[java\] FullTextQuery fullTextQuery = LuceneSession.createFullTextQuery(LuceneQuery); \[/java\]

`FullTextQuery` estende `Query` do hibernate. Podemos invocar `fullTextQuery.list()` para receber a lista de objetos (neste caso dvds) baseado na query do Lucene.

Para criar essa query, usamos a api do Lucene. Ela oferece uma [sintaxe simples e muito poderosa](http://Lucene.apache.org/java/docs/queryparsersyntax.html) para definir nosso critério de busca. Essa sintaxe é verificada pelo `QueryParser` do Lucene:

\[java\] String\[\] stopWords = new String\[\]{"de","do","da","dos","das","a","o","na","no","em"}; QueryParser parser = new QueryParser("title",new StopAnalyzer(stopWords));)); \[/java\]

Queremos procurar no campo `title` e passamos um objeto `Analyzer`. Ele é responsável por filtrar palavras insignificante na busca que foram definidos no array `stopWords`. Por padrão o `StopAnalyzer` somente conhece "stop words" do idioma inglês. É claro que o array deveria ser bem maior. Existem outros analyzers, como o `BrazilianAnalyzer`, que podem te ajudar bastante na tarefa de separar o que deve e como deve ser indexado.

Com o parser na mão podemos finalmente criar a query:

`Query LuceneQuery = parser.parse("Deus");`

procura por _"deus"_ ou _"Deus"_ e encontra o título _"Cidade de Deus"_ por exemplo. Uma busca por _"de"_ encontra nada, porque definimos _"de"_ como uma stopword. Você pode fazer buscas muito mais interessantes, como _"+cidade -Deus"_, que busca por todos os dvds que possuem _"cidade"_ mas não possuem _"Deus"_ como título.

Interessante também é a possibilidade de fazer uma [busca aproximada](http://lucene.apache.org/java/docs/queryparsersyntax.html#Fuzzy%20Searches), as tão faladas _fuzzy searches_. Se você buscar por _"cidadi~"_, a query também devolverá o mesmo dvd como resultado da query! O código final ficaria:

\[java\] public void search(Dvd dvd) throws ParseException {

Session session = this.factory.getSession(); FullTextSession LuceneSession = Search.createFullTextSession(session); String\[\] stopWords = {"de","do","da","dos","das","a","o","na","no","em"}; QueryParser parser = new QueryParser("title",new StopAnalyzer(stopWords));

Query LuceneQuery = parser.parse(dvd.getTitle()); FullTextQuery fullTextQuery = LuceneSession.createFullTextQuery(LuceneQuery, Dvd.class); this.dvds = fullTextQuery.list(); } \[/java\]

Repare que mesmo a query sendo do Hibernate Search, ela devolve uma lista de objetos _managed_ de DVDs. Qualquer alteração deles acarretará em uma mudança no banco de dados e reindexação dos campos!

**Conclusão**

Essa foi simente uma introdução do Hibernate Search e Lucene. Hibernate Search facilita ainda mais a configuração e indexação pelo Lucene: possibilita, por exemplo, usar JMS para guardar os índices de maneira clusterizada, além de indexar os documentos assíncronamente. Também é simples fazer ordenações e projeções, combinações com `criteria`, mapeamento de relacionamento, boost factors, otimização dos índices, e muitos mais. Você pode também utilizar o Hibernate Lucene através do `EntityManager` da JPA.
