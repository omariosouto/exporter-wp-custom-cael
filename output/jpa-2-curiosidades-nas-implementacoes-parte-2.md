---
title: "JPA 2: lazyloading do hibernate e weaving do eclipselink"
date: "2015-06-09"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

No [primeiro artigo sobre Hibernate e EclipseLink](https://blog.caelum.com.br/jpa-hibernate-ou-eclipselink/) falamos sobre curiosidades gerais na diferença entre as duas implementações. A discussão nos comentários foi bem interessante, inclusive tratando sobre JDO/JPA por meio do [DataNucleus](http://www.datanucleus.org/), vale a pena ler. Agora, vamos para algo mais específico, que tem um impacto maior no dia a dia do desenvolvedor. Só lembrando o cenário dos testes: EclipseLink 2.6.0, Hibernate 4.3.8, MySQL, Ubuntu, em ambiente Java SE.

**1 - O que acontece ao persistir uma Conta?** 

\[code language="java"\] Conta c = new Conta(); c.setNumero(25); c.setTitular("Raphael"); entityManager.getTransaction().begin();

entityManager.persist(c); //linha 5

System.out.println(entityManager.contains(c));//linha 6 System.out.println(c.getId()); //linha 7

entityManager.getTransaction().commit();

System.out.println(c.getId()); //linha 9 \[/code\]

**Hibernate:**

Após a execução **da linha 5**, um comportamento possível é disparar um INSERT em Conta. Isso varia um pouco, pois depende de qual estratégia foi utilizada para gereção automática das PK's. Ele poderia apenas fazer um select na SEQUENCE para descobrir o próximo valor para a chave primária.

Após chamar o método [PERSIST](http://blog.triadworks.com.br/atualizacoes-indevidas-com-a-jpa), o [Hibernate coloca o objeto no estado MANAGED](https://blog.caelum.com.br/entidades-managed-transient-e-detached-no-hibernate-e-jpa/), logo o retorno do método é true.

Após a **linha 7**, ao inserir o objeto no banco de dados o framework já popula o ID do objeto referenciado pela variável "c", caso o banco esteja vazio, este valor seria 1 por exemplo.

**EclipseLink:**

Após a execução **da linha 5,** há um comportamento mais curioso, pois o eclipseLink não faz nada.

Já na **linha 6**, a despeito de não ter feito nada na linha anterior, aqui a resposta é **TRUE**, pois ele coloca o objeto no estado MANAGED.

Com relação à **linha 7**, como ele ainda não foi ao banco de dados, o resultado aqui é null, que pode deixar o programador um pouco confuso, afinal ele está gerenciado. Mas afinal, quando ele vai ao banco de dados? Pode ser após um **flush** ou **commit**.

Após o commit, o EclipseLink gera duas SQL's

\[code\] \[EL Fine\]: sql: 2015-04-20 18:12:23.239--ClientSession(1175146719)--Connection(485701373)--INSERT INTO CONTA (NUMERO, TITULAR) VALUES (?, ?) bind => \[25, Raphael 25\] \[EL Fine\]: sql: 2015-04-20 18:12:23.268--ClientSession(1175146719)--Connection(485701373)--SELECT LAST\_INSERT\_ID() \[/code\]

Agora sim ele imprime o ID correto gerado pela Conta, mas pra isso ele teve que fazer um outro select.

**2 - Como fica o cache de segundo nível usando o @Cacheable?**

Esta foi uma outra [novidade do JPA 2](http://docs.oracle.com/javaee/6/tutorial/doc/gkjia.html), o cache de segundo nível foi especificado. Basicamente temos que anotar a Entidade que deve ser cacheada usando _@Cacheable._ Ainda existe a possibilidade de fazer uma combinação com os valores da tag _shared-cache-mode_ no [persistence.xml](http://docs.oracle.com/javaee/6/tutorial/doc/gkjio.html).

Então vamos anotar a entidade Conta e executar o código abaixo:

\[code language="java"\]

EntityManager em = emf.createEntityManager(); EntityManager em2 = emf.createEntityManager();

Conta conta = em.find(Conta.class, 1); //linha 3

System.out.println(em2.getEntityManagerFactory().getCache().contains(Conta.class, 1)); //linha 4

Conta conta2 = em2.find(Conta.class, 1); //linha 5

System.out.println(conta == conta2); //linha 6 \[/code\]

**EclipseLink**:

Após executar **a linha 3**, o EclipseLink dispara um SELECT.

Já na **linha 4**, apesar de ser um EntityManager diferente do que foi utilizado para pesquisar, o em2 já consegue identificar que existe uma Conta com ID 1 cacheada, retornando **TRUE**.

Como Conta já está cacheada, a **linha 5** não dispara nenhum select.

Por fim, na **linha 6** o resultado é FALSE, pois apesar do segundo objeto ter sido recuperado do cache, ainda são objetos diferentes.

**Hibernate**: Só colocando as configurações acima, o cache de segundo nível não funciona, o Hibernate dispara dois selects. Nesse, o desenvolvedor ainda terá que configurar um provider de cache como EHCache ou Infinispan.

**3 - Como é o comportamento Lazy? conta.getMovimentacoes() e movimentacao.getConta()**

Sabemos que por default o relacionamento @OneToMany é Lazy, podendo trazer problemas de [LazyInializationException](https://blog.caelum.com.br/enfrentando-a-lazyinitializationexception-no-hibernate/) e [N+1 queries](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/) quando não for bem utilizado. Há [várias soluções](http://blog.triadworks.com.br/hibernate-efetivo-erros-comuns-e-solucoes) para o problema como OpenSessionInView, queries planejas ou até mesmo alterar o comportamento para EAGER. Vamos ao código

\[code language="java"\] Conta conta = em.find(Conta.class, 1); System.out.println(conta.getTitular()); System.out.println(conta.getMovimentacoes()); //linha 3 \[/code\]

**Hibernate**: Executa duas queries. Uma para Conta e outra para buscar as movimentações da Conta.

**EclipseLink**: Se o toString de Movimentacao tiver sido implementado, ele dispara dois select's, caso contrário, dispara apenas um SELECT em Conta e depois irá imprimir: _{IndirectList: not instantiated}_

![](http://www.eclipse.org/eclipselink/logocontest/sapir/bridge_eclipse.gif)

Vamos então alterar o comportamento **default**.

\[code language="java"\]

@OneToMany(mappedBy = "conta", fetch=FetchType.EAGER) // @OneToMany(mappedBy = "conta") private List<Movimentacao> movimentacoes; \[/code\]

**Hibernate**: Executa uma query que faz o **left outer join**, já trazendo Conta e suas respectivas movimentações.

**EclipseLink**: Agora, mesmo que o toString não esteja implementado, ele já busca as movimentações, porém ele faz DOIS selects.

\[code\] \[EL Fine\]: sql: 2015-04-20 19:19:36.642--ServerSession(706277948)--Connection(156127720)--SELECT ID, NUMERO, TITULAR FROM CONTA WHERE (ID = ?) bind => \[1\] \[EL Fine\]: sql: 2015-04-20 19:19:36.656--ServerSession(706277948)--Connection(156127720)--SELECT ID, VALOR, CONTA\_ID FROM MOVIMENTACAO WHERE (CONTA\_ID = ?)</em> bind => \[1\] \[/code\]

"_Apesar de termos mudado o comportamento default, colocando EAGER, o EclipseLink resolveu simplesmente ignorar este fato e ainda continua sendo LAZY_". Esta seria uma conclusão óbvia, mas não é bem assim. Na verdade, o eclipseLink está sim obedecendo o EAGER, basta **comentar a linha 3** e ainda assim as movimentacoes serão retornadas, todavia ainda são feitas as duas consultas.

Outro exemplo, agora pesquisando por Movimentação.

\[code language="java"\] Movimentacao mov = em.find(Movimentacao.class, 1); System.out.println(mov.getValor());

// lembrando que: @ManyToOne private Conta conta; \[/code\]

**Hibernate**: Faz somente uma query trazendo Movimentacao e sua respectiva conta, afinal é **EAGER** por default um relacionamento @ManyToOne

**EclipseLink**: Faz dois selects, busca movimentacao depois conta.

\[code\]\[EL Fine\]: sql: 2015-04-20 19:31:15.621--ServerSession(1956710488)--Connection(2140396878)--SELECT ID, VALOR, CONTA\_ID FROM MOVIMENTACAO WHERE (ID = ?)</em> bind => \[44\] \[EL Fine\]: sql: 2015-04-20 19:31:15.635--ServerSession(1956710488)--Connection(2140396878)--SELECT ID, NUMERO, TITULAR FROM CONTA WHERE (ID = ?) bind => \[1\] \[/code\]

Por que no EAGER o EclipseLink faz duas SELECT's e o Hibernate somente uma? Bom, deixo um trecho [do artigo sobre JPA](http://en.wikibooks.org/wiki/Java_Persistence/Relationships#Object_corruption.2C_one_side_of_the_relationship_is_not_updated_after_updating_the_other_side) da WikiBooks. A recomendação é fazer uma [JOIN FETCH](http://en.wikibooks.org/wiki/Java_Persistence/Relationships#Join_Fetching) ou poderíamos utilizar @JoinFetch.

> One common misconception is that `EAGER` means that the relationship should be join fetched, i.e. retrieved in the same SQL `SELECT` statement as the source object. Some JPA providers do implement eager this way. However, just because something is desired to be loaded does not mean that it should be join fetched.

E se alterássemos o relacionamento para Lazy? @ManyToOne(fetch=FetchType.LAZY)

**Hibernate**: Faz apenas uma query, buscando apenas Movimentação, afinal agora só busca Conta quando for utilizada.

**EclipseLink**: Continua fazendo duas queries. Só que ele gera a seguinte mensagem.

\[code\] \[EL Warning\]: metadata: 2015-04-20 19:48:33.847--ServerSession(706277948)--Reverting the lazy setting on the OneToOne or ManyToOne attribute \[conta\] for the entity class \[class br.modelo.Movimentacao\] since weaving was not enabled or did not occur. \[/code\]

Para habilitarmos o Lazy no EclipseLink temos que usar a propriedade [weaving](http://eclipse.org/eclipselink/documentation/2.5/concepts/app_dev007.htm). Há vários [posts](http://java-persistence-performance.blogspot.com.br/2011/06/how-to-improve-jpa-performance-by-1825.html) comentando esta [situação](http://www.javabeat.net/jpa-lazy-eager-loading/). Para habilitar o weaving, temos que passar no vm arguments o parâmetro **\-javaagent:eclipselink.jar**

Agora ao executar o código com o EclipseLink, o Lazy é acionado e apenas um select é disparado.

\[code\]\[EL Fine\]: sql: 2015-04-20 19:51:32.242--ServerSession(603856241)--Connection(1937575946)--SELECT ID, VALOR, CONTA\_ID FROM MOVIMENTACAO WHERE (ID = ?)</em> bind => \[44\]\[/code\]

Aqui [há um outro artigo que discute](http://vard-lokkur.blogspot.com.br/2010/09/jpa-demystified-episode-1-onetomany-and.html) os mapeamentos Hibernate X EclipseLink, inclusive usando o OpenJPA também.

Finalizando, escolher qual implementação deve ser usada, por incrível que pareça, não é uma tarefa fácil. Apesar da resposta rápida ser Hibernate, cada framework traz vantagens e desvantagens. A dica é tentar seguir o máximo possível a API para facilitar a portabilidade no futuro (mas sabemos que isso é meio utópico). Uma outra diferença é com relação ao tratamento [Multi-Tenancy](http://docs.jboss.org/hibernate/orm/4.2/devguide/en-US/html/ch16.html), entretanto, esse fica para outro post. Não deixem de comentar!
