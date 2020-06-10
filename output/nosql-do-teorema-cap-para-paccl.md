---
title: "NoSQL - Do teorema CAP para P?(A|C):(C|L)"
date: "2011-12-07"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[Existem muitas](http://highscalability.com/blog/2010/12/6/what-the-heck-are-you-actually-using-nosql-for.html) [motivações](http://highscalability.com/blog/2011/6/15/101-questions-to-ask-when-considering-a-nosql-database.html) para [os bancos NoSQL](http://nosql-database.org/), como por exemplo usar um [modelo](http://www.thoughtworks.com/articles/nosql-comparison#Types-of-NoSQL-datastores) [mais adequado](https://blog.caelum.com.br/trabalhando-com-relacionamentos-bancos-de-dados-baseados-em-grafos-e-o-neo4j/) para os seu dados ou [facilitar alterações de schema](http://stackoverflow.com/questions/2117372/what-are-the-advantages-of-using-a-schema-free-database-like-mongodb-compared-to); ou ainda além, melhorar o desempenho e simplificar a replicação para ter a [tão sonhada](https://blog.caelum.com.br/bancos-de-dados-nao-relacionais-e-o-movimento-nosql/) [escalabilidade linear](https://blog.caelum.com.br/quando-muitos-dados-passam-a-atrapalhar-replicacao-e-sharding/).

**O teorema CAP**

Claro que todos os benefícios não vem sem custo, comparado com os bancos de dados tradicionais vamos perder alguma funcionalidade/garantia para ganhar outra. O tradeoff arquitetural é [descrito](http://www.julianbrowne.com/article/viewer/brewers-cap-theorem) no [bem conhecido](http://en.wikipedia.org/wiki/CAP_theorem) [CAP theorem](http://blog.nahurst.com/visual-guide-to-nosql-systems).

![](http://3.bp.blogspot.com/-jt3tZEGkvaw/TbzFI6DZjMI/AAAAAAAAAAw/RyvAOEpqBWo/s640/CAP_Diagram_dist+copy.jpg "CAP Theorem")

A [palestra famosa do Dr. Eric Brewer](http://www.cs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) introduz o teorema e explica que em qualquer sistema distribuído _stateful_ é preciso escolher entre **consistência forte (C - Consistency)**, **alta disponibilidade (A - availability)** e **tolerância a particionamento dos dados na rede(P - Network Partition Tolerance)**. Segundo o teorema CAP, entre as três propriedades**, somente duas** podem ser garantidas ao mesmo tempo:

**Partition-Tolerance**

Poder particionar nossos dados em diferentes nós de um cluster é um dos recursos que aparecem com frequência nos bancos NoSQL. Saber lidar com a separação/particionamento das dados devido uma falha na rede é conhecido como _Partition-Tolerant._ No entanto, segundo o teorema CAP, em troca eles irão sacrificar a consistência forte _ou_ a alta disponibilidade. Isso é diferente dos bancos tradicionais, que não possuem essa característica no design do sistema ou delegam isso para o filesystem.

**NoSQL 1: Sistemas CP**

Para sistemas que precisam da **consistência** forte e **tolerância a** **particionamento** **(CP)** é necessário abrir a mão da disponibilidade (_um pouco_). Pode acontecer, caso haja particionamento e o sistema não entre em consenso, que uma escrita seja rejeitada. Claro que os sistemas tentam evitar isso ao máximo, tanto que não costuma existir, por exemplo, uma transação distribuída e sim um [protocolo de consensos](http://en.wikipedia.org/wiki/Paxos_(computer_science)) para garantir a consistência forte. Exemplos desses sistemas CP são [BigTable](http://labs.google.com/papers/bigtable.html), [HBase](http://hbase.apache.org/) ou [MongoDB](http://www.mongodb.org/) entre vários outros.

**NoSQL 2: Sistemas AP**

Por outro lado existem sistemas que jamais podem ficar offline (24/7), portanto não desejam sacrificar a disponibilidade. Para ter alta **disponibilidade** mesmo com um **tolerância a** **particionamento** **(PA)** é preciso prejudicar a consistência ([eventual-consistency](http://www.allthingsdistributed.com/2007/12/eventually_consistent.html)). A ideia aqui é que os sistemas aceitam escritas sempre e tentam sincronizar os dados em algum momento depois (_[read-consistency](http://aws.amazon.com/simpledb/faqs/#What_read_consistency_options_does_Amazon_SimpleDB_provide)_). Então pode ter uma _janela de inconsistência_. Exemplos aqui são [Amazon Dynamo](http://www.allthingsdistributed.com/2007/10/amazons_dynamo.html), [Cassandra](http://cassandra.apache.org/) ou [Riak](http://wiki.basho.com/).

**Sistemas CA**

Os sistemas com **consistência** forte e alta **disponibilidade** **(CA)** (alta disponibilidade de um nó apenas) não sabem lidar com a possível falha de uma partição. Caso ocorra, sistema inteiro pode ficar indisponível até o membro do cluster voltar. Exemplos disso são algumas configurações clássicas de bancos relacionais.

**Qual é a diferença entra CA e CP?**

Vimos brevemente o teorema CAP e a escolha que os sistemas NoSQL fazem (CP ou AP) comparado com os bancos tradicionais (CA). É importante mencionar que para o desenvolvedor não haverá tantas diferenças entre CA ou CP. **SEMPRE teremos consistência forte**, no entanto, um sistema fica _indisponível_ (CA) quando há particionamento - pois tem apenas alta disponibilidade por nó - e o outro sistema (CP) tente chegar a um consenso se aceita uma escrita ou não, que no pior dos casos também pode significar a _indisponibilidade_ para uma parte dos dados. Seguindo desse raciocínio podemos perceber que a **consistência e disponibilidade são extremos quando há particionamento**. [Isso foi uma dúvida](http://pl.atyp.us/wordpress/index.php/2009/11/availability-and-partition-tolerance/) que me incomodou bastante antes da [minha palestra no Caelumday 2009](http://www.slideshare.net/steppat/nosql-caelum-day-2009). _Podemos concluir que quando há particionamento (P) terá alta disponibilidade (A) ou consistência (C) forte: P?(A|C)_

**Mas o que acontece se NĀO há particionamento?**

É uma pergunta que o CAP não responde. A primeira resposta poderia ser: claro que vai ser consistente já que ninguém gosta de lidar com dados desatualizados. Mas olhando para os sistemas NoSQL nem sempre isso é verdade. Existem sistemas que **SEMPRE são eventually-consistent**. _Mas porque_?

**Consistência ou Latência**

Há mais um motivo porque poderia fazer sentido sacrificar a consistência: O tempo da resposta ou a **latência**. Da mesma maneira que um sistema offline pode custar caro, [um sistema lento também](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/) pode. Por isso pode fazer sentido abrir a mão da consistência para diminuir a latência.

**De CAP para PAC/CL**

O [artigo do blog](http://dbmsmusings.blogspot.com/2010/04/problems-with-cap-and-yahoos-little.html) do Prof. [Daniel Abadi](http://www.twitter.com/daniel_abadi) explica o tradeoff com partições e sem. Ele sugere substituir a sigla CAP com **PAC/CL** (ou P?(A|C):(C|L)), traduzindo levemente modificado do artigo dele:

> “... se há particionamento (P), o sistema pode valorizar a disponibilidade (A) ou a consistência (C), senão, quando o sistema roda sem partições, o sistema pode favorecer o tempo da resposta/latência (L) ou a consistência (C).”

**Exemplos de PAC/CL**

Seguindo dessa linha PC/C significa que o sistema valoriza a consistência sempre, com ou sem partições. Banco de dados tradicionais são sempre fortemente consistentes, ou seja PC/C. Amazon Dynamo ou Cassandra são sempre fracamente consistente, favorecendo a alta disponibilidade e o tempo da resposta (latência), ou seja PA/L. Mas existem misturas como o [GenieDB](http://www.geniedb.com/wp-content/uploads/2011/04/beating-the-cap-theorem-revised.pdf) (PA/C), que só trabalha consistente em caso de nenhuma partições. Quando há partições valoriza a alta disponibilidade. Exemplo contrário disso é o [Yahoo Sherpa](http://en.wikipedia.org/wiki/Yahoo_Sherpa), que usa PC/L, ou seja com partições favorece consistência, sem partições diminuir a latência.

Durante o curso [FJ-91](http://www.caelum.com.br/curso/fj-91-arquitetura-design-projetos-java/), diversas questões e decisões arquiteturais são abordadas e discutidas, sendo que uma delas envolve o teorema CAP e os bancos de dados NoSQL.
