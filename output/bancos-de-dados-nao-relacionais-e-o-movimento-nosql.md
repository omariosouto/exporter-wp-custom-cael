---
title: "Bancos de dados não relacionais e o movimento NoSQL"
date: "2009-10-31"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Nas grandes aplicações web é cada vez mais comum a quantidade de informações ser enorme, e ainda temos uma certeza: amanhã teremos mais dados para armanezar. Como lidar com isso de maneira eficiente?

Muito se fala ultimamente sobre os novos bancos **não** relacionais. Houve um [encontro inicial](http://nosql.eventbrite.com/ "meeting") e a [segunda conferência](https://nosqleast.com/2009/ "NoSQL East") também já aconteceu. O movimento acabou ganhando o nome de **NoSQL**, até mesmo por que cada banco de dados tem uma maneira diferente de se escrever queries.

A grande motivação para NoSQL é resolver o [problema de escalabildade](http://www.slideshare.net/jbellis/what-every-developer-should-know-about-database-scalability "problema de escalabildade") dos bancos tradicionais. Pode ser [muito caro](http://www.oracle.com/technology/products/database/clustering/index.html "muito caro") ou/e [complexo escalar um banco SQL](http://bret.appspot.com/entry/how-friendfeed-uses-mysql "complexo").

Esse movimento está bastante enraizado no open source. Dá para perceber isso até mesmo pelos curiosos nomes dos projetos: [Voldemort](http://project-voldemort.com/ "Voldemort"), [MongoDB](http://www.mongodb.org/ "MongoDB"), [Tokyo](http://1978th.net/tokyocabinet/ "Tokyo Cabinet") [Tyrant](http://1978th.net/tokyotyrant/ "Tyrant") e [CouchDB](http://couchdb.apache.org/ "CouchDB").

Apesar de grande quantidade desses bancos serem open source, o movimento ganhou muita força com a publicação de dois papers sobre implementações proprietárias: o [Google Bigtable](http://labs.google.com/papers/bigtable.html "Bigtable") ([que a Caelum usa atualmente](https://blog.caelum.com.br/o-cloud-computing-e-inevitavel/)) e o [Amazon Dynamo](http://www.allthingsdistributed.com/2007/10/amazons_dynamo.html "Dynamo"). Não por acaso são duas empresas que lidam com uma quantidade enorme informações. Outros [grandes nomes participam do movimento NoSQL](http://www.computerworld.com/s/article/9135086/No_to_SQL_Anti_database_movement_gains_steam_ "movimento NoSQL"): Yahoo! ([Hadoop](http://hadoop.apache.org/) com [HBase](http://hadoop.apache.org/hbase/), [Sherpa](http://developer.yahoo.net/blog/archives/2009/06/sherpa.html "Sherpa")), Facebook e Digg ([Cassandra](http://blog.digg.com/?p=966 "Cassandra")), LinkedIn ([Voldemort)](http://blog.linkedin.com/2009/03/20/project-voldemort-scaling-simple-storage-at-linkedin/ "Voldemort)"), Mixi (Facebook do Japão) ([Tokyo Cabinet](http://www.scribd.com/doc/12016121/Tokyo-Cabinet-and-Tokyo-Tyrant-Presentation "Tokyo Cabinet")) e a Engine Yard ([MongoDB](http://www.engineyard.com/blog/2009/key-value-stores-in-ruby/ "MongoDB")).

Muitos acham que esses bancos de dados escalam simplesmento por causa da ausência de um schema (schema free), logo não há verificação de integridade e de relacionamentos. Mas seria só isso? O MySQL, nos seus primórdios, quando não fazia tais verificações, ainda assim não era rápido como esses novos competidores. Quais são então os segredos para tanta escalabilidade?

O Amazon Dynamo se destacou por causa da forma como o sistema escala. Cada nó no cluster comunica com outros nós (p2p) e faz ativamente parte da partição/replicação. Não tem um [single-point-of-failure](http://en.wikipedia.org/wiki/Single_Point_of_Failure), mas essa facilidade de escalar [não vem sem custo](http://www.allthingsdistributed.com/2008/12/eventually_consistent.html "não vem sem custo").

Todos os novos bancos tem em comun que eles são [key-value stores](http://themindstorms.blogspot.com/2009/05/quick-reference-to-alternative-data.html "key-value stores"), ou seja salvam, como o nome sugere, um conjunto de enradas formadas por [uma chave associada a um valor](http://pt.wikipedia.org/wiki/Vetor_associativo "chave associado com um valor") e o valor poderia ser de qualquer tipo, um binário ou string que está sendo salvo de forma denormalizada (schema-free). Diferentemente dos bancos SQL **não** existe uma esquema forte. Essa abordagem facilita a distribuição dos dados entre vários servidores onde cada servidor possui apenas uma fatia dos dados (_[shard](http://highscalability.com/unorthodox-approach-database-design-coming-shard "shard")_).

O CouchDB é um dos mais famosos no time dos key-value stores. Ele usa _documento_s para definir uma estrutura no banco, armazenando uma chave associada ao um documento. Um documento é apresentado como [JSON](http://json.org/). Por exemplo:

`{
  "Subject": "Bancos não relacionais"
  "Author": "Nico Stepat"
  "PostedDate": "10/15/2009"
  "Tags": ["database", "nosql", "rest"]
}` 

Repare a estrutura dos dados é definido através da aplicação, o CouchDB não exige nada, apenas um documento JSON.

Talvez o CouchDB ficou famoso por causa da simples API REST e do uso do JSON, ou da [interface grafica](http://couchdb.apache.org/screenshots.html "interface grafica") bonita ou por causa dos [views](http://wiki.apache.org/couchdb/Using_Views "views") interessantes usando [Map-Reduce](http://en.wikipedia.org/wiki/Map_reduce "Map-Reduce") ou da [replicação](http://en.wikipedia.org/wiki/Replication_%28computer_science%29#Database_replication) [Multi-Master](http://wiki.apache.org/couchdb/How_to_replicate_a_database) ou por que foi escrito em Erlang (como [esse](http://www.zib.de/CSR/Projects/scalaris/ "scalaris") e [esse](http://riak.basho.com/ "riak") também). Seja que for, a promessa principal do NoSQL - sendo escalável - o CouchDB não compriu ainda. Ele não é [distribuído](http://spyced.blogspot.com/2008/12/couchdb-not-drinking-kool-aid.html "distribuido") [sozinho](http://wiki.apache.org/couchdb/Configuring_distributed_systems "sozinho"), e precisa de [ajuda](http://code.google.com/p/couchdb-lounge/ "ajuda") externa para tal.

Outra forma de dar alguma estrutura aos dados ficou famosa por causa do Google Bigtable. A idéia é não salvar os dados em _linhas_ como estamos acustomados pelos bancos relacionais. Os dados serão salvos através de **colunas**. Veja a diferença:  

Row-Oriented (3 rows presentes - Nome, Salário, Data):

`João,1432.00,15/10/2009
Maria,1511.00,13/10/2009
Pedro,1721.00,01/10/2009` 

Column-Oriented (mesmo exemplo):

`João,Maria,Pedro
1432.00,1511.00,1721.00
15/10/2009,13/10/2009,01/10/2009` 

No _column-oriented_ vem primeiro TODOS os dados da primeira coluna _Nome_, depois a segunda coluna _Salario_ e por último a coluna _Data_.

E isso altera algma coisa? Para o desenvolvedor que vai utilizar o banco de dados, a idéia é que isso seja transparente, mas para quem desenvolveu o banco, há enormes melhorias.

Isso não é muito vantajoso quando for salvo apenas um registro, como cada coluna tem que ser accessada separadamente. Também complica mais na recuperação de um registro específico (random-access) pelo mesmo motivo. Aqui a abordagem _row-oriented_ tem vantagens.

Por outro lado, usando colunas, podemos empacotar os dados melhor já que os dados semelhantes, de mesmo formato, estão próximos um do outro. [Gravando dados empacotados em BDs](http://en.wikipedia.org/wiki/Column-oriented_DBMS#Storage_efficiency_vs._random_access "dados empacotados") traz grandes vantagens, porque podemos recuperar e armanezar mais informações em menos tempo.  

Com colunas também podemos aplicar projeções sobre os dados mais fácil. A segunda vantagem é importante principalmente para sistemas [OLAP](http://pt.wikipedia.org/wiki/OLAP "OLAP") (online analytic process) que usam esse tipo de [pesquisas pesadamente](http://databasecolumn.vertica.com/2008/03/supporting-column-store-perfor.html "queries pesadamente").

Banco de dados orientados a coluna vão além de um simples key-value store. Eles representam normalmente um array/hash de 4 ou 5 dimensões, [usando](http://www.slideshare.net/hmisty/20090713-hbase-schema-design-case-studies "copiando") ou [adaptando](http://wiki.apache.org/cassandra/DataModel "adaptando") o [modelo proposto](http://jimbojw.com/wiki/index.php?title=Understanding_HBase_and_BigTable) pelo [BigTable](http://labs.google.com/papers/bigtable.html).

Primeiramente, também temos tabelas só que eles chamem _column-families_. Como o nome indica um _column-family_ são váras colunas. Quais são essas colunas, a aplicação define.

Cada coluna salva um valor. O grupo de colunas dentro de uma familia é acessível atraves de uma chave (_row-key_). O esquema fica:

- **columnFamily** - parecido com uma tabela (tabela de colunas)
- **rowKey** - ID do grupo de colunas
- **column** - nome da coluna
- **value** - valor a salvar

<columnFamily>.<rowKey>.<column> = <value>

Por exemplo, para apresentar dados sobre um DVD com a ID 234:

 `<dvds>.<234>.<nome> = 'Beleza americana'
    <dvds>.<234>.<ator_principal> = 'Kevin Spacey'
    <dvds>.<234>.<ano> = '1999'` 

ou para apresentar um usuário com joao.silva:

 `<usuarios>.<joao.silva>.<nome> = 'Joao Silva'
    <usuarios>.<joao.silva>.<email> = 'joao.silva@foo.com'` 

e o relacionamento:

   <usuarios\_dvds>.<joao.silva>.<234> = 'data que ele comprou'

Normalmente é salvo uma data automaticamente, ou seja, a estrutura completa fica:

   <columnFamily>.<rowKey>.<column>.<timestamp> = <value>

Os bancos orientados a coluna não oferecem joins entre _column-families_ nem chaves compostas. A Google AppEngine, por exemplo, não aceita chaves compostas porque usa o BigTable por baixo. Existem [pequenas diferenças](http://arin.me/code/wtf-is-a-supercolumn-cassandra-data-model "SuperColum") entre implementações orientadas a coluna.

Seria o ["início do fim"](http://www.infoq.com/news/2009/08/NoSQL-and-the-End-of-RDBMS-Era "inicio do fim") dos [bancos relacionais](http://www.infoq.com/news/2007/09/row-vs-column-dbs "bancos relacionais legados")? Aderindo a um banco de dados não relacional muita da responsibilidade de cuidar dos dados fica a cargo da aplicação. É ela que define como funcionam e como se relacionam os documentos. O banco fica com certeza mais simples, escalavél e rápido, mas perdemos as conhecidas garantias dos bancos relacionais. **Como toda decisão arquitetural, escolher por bancos de dados não relacionais apresenta um [trade](http://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf "Brewer's CAP Theorem") [off](http://www.julianbrowne.com/article/viewer/brewers-cap-theorem "Brewer's CAP Theorem"): [ACID](http://www.eaipatterns.com/docs/IEEE_Software_Design_2PC.pdf "Your Coffee Shop Doesn't Use Two-Phase Commit") ou [BASE](http://queue.acm.org/detail.cfm?id=1394128) ou alguma coisa no meio.** Vale a pena? Cada caso deve ser estudado com cuidado. Algumas aplicações usam bancos de dados não relacionais para uma leitura e escrita temporária, atualizando um banco relacional de tempos em tempos, tirando vantagem das duas estratégias.

Semana que vem, no [Caelum Day in Rio](http://www.caelum.com.br/caelumday/ "CaelumDay") conversaremos sobre muitos outros detalhes do movimento NoSQL. Nos encontramos lá!
