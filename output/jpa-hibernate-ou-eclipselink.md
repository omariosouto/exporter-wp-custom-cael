---
title: "JPA 2: Hibernate ou EclipseLink?"
date: "2015-04-28"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Qual implementação de JPA você utiliza? Bom, durante muito tempo achei que a resposta automática para esta pergunta seria Hibernate, porém este ano fiz uma migração de um sistema grande que estávamos usando Hibernate para o EclipseLink. A escolha para o Hibernate era clara, experiência da equipe, aplicação rodando em WildFly, a princípio, melhor desempenho e por aí vai. Agora, por que trocamos de implementação? Vou responder esta pergunta com uma série de posts. Neste vou abordar sobre curiosidades nas diferenças das duas implementações. Os testes foram feitos usando Ubuntu, MySql, EclipseLink 2.6 e Hibernate 4.3.8 em **um ambiente JavaSE**. Mas não deixe de conhecer outras como [OpenJPA](http://openjpa.apache.org/) e [Batoo](http://batoo.org/), sendo que esta [última](http://highscalability.com/blog/2012/10/9/batoo-jpa-the-new-jpa-implementation-that-runs-over-15-times.html) possui [benchmarks até melhores que o Hibernate](http://www.theserverside.com/discussions/thread.tss?thread_id=69395).

O primeiro item que muitos alunos me perguntam é [relacionado à perfomance](http://www.espweb.uem.br/site/files/tcc/2012/Aurelio%20Vargas%20Ramos%20Junior%20-%20Analise%20de%20Desempenho%20dos%20Frameworks%20de%20Persistencia%20Hibernate%20e%20EclipseLink.pdf). Bom, quanto a isso [não vou me](http://blog.eisele.net/2009/01/jpa-implementations-comparison.html   ) aprofundar muito pois já [existem inúmeros trabalhos](http://www2.virtual.ufc.br/smd/images/docanexos/tcc_Dmora_Bruna.pdf), maioria até mesmo [acadêmicos, como TCC´s](http://www.espweb.uem.br/site/files/tcc/2010/Jader%20dos%20Santos%20Teles%20Cordeiro%20-%20Estudo%20comparativo%20entre%20frameworks%20de%20mapeamento%20objeto-relacional%20Hibernate%20e%20TopLink.pdf). Existe até mesmo artigo comparado [JPA com o finado JDO.](http://www.cin.ufpe.br/~tg/2009-2/nln.pdf) Em suma, o [hibernate costuma ter um desempenho melhor](http://stackoverflow.com/questions/3035308/jpa-2-0-implementations-comparison-hibernate-3-5-vs-eclipselink-2-vs-openjpa-2), mas com certeza [vale a pena](http://terrazadearavaca.blogspot.com.br/2008/12/jpa-implementations-comparison.html) dar uma lida nesta [coleção](http://stackoverflow.com/questions/2569522/hibernate-or-eclipselink) .

Para mostrar algumas curiosidades, vamos usar a relação entre Conta e Movimentação. O pseudo-código abaixo representa a relação.

\[code language="java"\] @Entity class Conta{

@Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Integer id; @OneToMany (mappedBy="conta") private List<Movimentacao> movimentacoes; } @Entity class Movimentacao{

@Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Integer id; @ManyToOne private Conta conta; } \[/code\]

Vamos lá.

**\- Como habilitar os Logs?**

Esta é uma propriedade que ainda não foi especificada, logo cada implementação tem a sua.

**Hibernate:**

<property name="hibernate.show\_sql" value="true"></property> <property name="hibernate.format\_sql" value="true"></property>

**EclipseLink:** <property name="eclipselink.logging.level.sql" value="FINE" /> <property name="eclipselink.logging.parameters" value="true" />

**\- Devo declarar <class> no persistence.xml?**

Bom, aqui já tem diferença. O Hibernate deixa que o mapeamento seja feito apenas através do @Entity, porém o EclipseLink exige que a classes também sejam mapeadas via <class> no persistence.xml.

 

![](http://www.flacom.com/content/uploads/2013/10/JPAHibernate.jpg)

 

**\- Como gerar a base a partir do modelo?**

No JPA 2.1 já [houve uma padronização deste parâmetro,](http://antoniogoncalves.org/2014/12/11/generating-database-schemas-with-jpa-2-1/) agora basta usar a propriedade **javax.persistence.schema-generation.database.action.** Ela possui 4 valores: create, drop-create, none, drop.

E além disso, foi especificado também a propriedade **javax.persistence.schema-generation.scripts.create-target**, que permite gerar os arquivos de DDL.Agora

As duas implementações oferecem suporte, entretanto possuem comportamentos diferentes.

**EclipseLink**: com o banco de dados vazio, ao usar a opção create e rodar o programa, o resultado é a criação da base de dados. Ao inserir uma nova @Entity e rodar novamente o programa, a implementação atualiza a base.

**Hibernate**: Nestas mesmas condições, ele cria a base na primeira execução, mas na segunda execução ele falha pois tenta criar novamente as mesmas tabelas e falha pois já estão criadas.

A [especificação não deixa claro qual](https://jcp.org/en/jsr/detail?id=338) deveria ser o comportamento.

Aqui também é importante ressaltar uma outra diferença que impacta diretamente na portabilidade. O EclipseLink gera as tabelas com letras maiúsculas e o Hibernate seguindo o padrão de nome da Classe.

Um outro ponto a ressaltar é que a DDL do EclipseLink é muito mais amigável que a do Hibernate.

**EclipseLink**: `CREATE TABLE MOVIMENTACAO (ID INTEGER AUTO_INCREMENT NOT NULL, VALOR DOUBLE, CONTA_ID INTEGER, PRIMARY KEY (ID)) ALTER TABLE MOVIMENTACAO ADD **CONSTRAINT FK_MOVIMENTACAO_CONTA_ID** FOREIGN KEY (CONTA_ID) REFERENCES CONTA (ID)` **Hibernate**: `create table MOVIMENTACAO (id integer not null auto_increment, valor double precision not null, conta_id integer, primary key (id)) alter table MOVIMENTACAO **add constraint FK_pf1tg6j429i17rjrp0pmbw6a3** foreign key (conta_id) references CONTA (id)`

 

Bom, esta foi a primeira parte, na segunda parte vou discutir sobre diferenças ao persistir objetos, pesquisas, relacionamentos LAZY e como [lidar com o weaving](http://eclipse.org/eclipselink/documentation/2.5/concepts/app_dev007.htm). Aqui na [Caelum já falamos](https://blog.caelum.com.br/enfrentando-a-lazyinitializationexception-no-hibernate/) muito sobre [JPA](https://www.caelum.com.br/curso-persistencia-java-jpa-hibernate/), [Lazy Loading](https://blog.caelum.com.br/entendendo-o-lazy-e-o-eager-load-da-jpa/), trabalhos [Batch,](https://blog.caelum.com.br/trabalhando-com-batch-processing-de-maneira-eficaz-utilizando-a-jpa/) boas [práticas e muitos](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/) outros [artigos](https://blog.caelum.com.br/?s=jpa), não [deixe de ler](https://blog.caelum.com.br/?s=hibernate), assim como acompanhar apresentações [que sempre acontecem sobre a tecnologia](http://www.slideshare.net/rponte/hibernate-efetivo-qconsp2012) e o excelente livro do [Hebert](http://uaihebert.com/) (desenvolvedor da DSL [Easy Criteria](http://easycriteria.uaihebert.com/)) publicado pela [casa do código](http://www.casadocodigo.com.br/products/livro-jpa-eficaz). Não deixe de comentar e até o próximo post.

 

ps: Bom, hoje é uma data especial pra mim, por isso aproveito o espaço para lançar um desafio aos companheiros da Caelum e quem tiver afim de embarcar nesta. #façaUmPostNoSeuAniversário
