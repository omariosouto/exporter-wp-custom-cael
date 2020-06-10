---
title: "A java.net.SocketException Broken Pipe"
date: "2009-10-19"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando começamos a programar com banco de dados, rapidamente aprendemos que devemos sempre usar um pool de conexões para acessa-lo, caso contrário podemos facilmente atrapalhar o bom funcionamento do mesmo, devido o excesso de conexões.

Passamos então a usar um pool de conexões, e ao colocar o sistema em produção, nos deparamos com outro problema: o _broken pipe_:

`java.net.SocketException: Broken pipe
at java.net.SocketOutputStream.socketWrite0(Native Method)
at java.net.SocketOutputStream.socketWrite(SocketOutputStream.java:92)
at java.net.SocketOutputStream.write(SocketOutputStream.java:136)
at java.io.BufferedOutputStream.flushBuffer(BufferedOutputStream.java:65)
at java.io.BufferedOutputStream.flush(BufferedOutputStream.java:123)
at com.mysql.jdbc.MysqlIO.send(MysqlIO.java:2690) 
...` 

No GUJ, são [mais de 100 mensagens a respeito de broken pipes](http://www.google.com.br/search?q=site%3Aguj.com.br+broken+pipe)! Recentemente [Tomaz Lavieri](http://blog.tomazlavieri.com.br/) abriu [um detalhado tópico](http://guj.com.br/posts/list/141603.java) sobre esse mesmo assunto, que me incentivou a escrever esse post, dada sua relevância.

Por que essa exception acontece? São dois motivos principais:

O primeiro é que muitos bancos de dados possuem um timeout para conexões inativas ([o padrão do MySQL é de 8 horas](http://dev.mysql.com/doc/refman/5.0/en/server-system-variables.html#sysvar_interactive_timeout), mas em alguns hosts isso pode estar configurado em segundos!). Depois de determinado tempo, o banco de dados mata essa conexão ociosa numa tentativa de economizar recursos, pois conclui que alguém simplesmente a esqueceu aberta. Quando, no lado do cliente, seu pool decide usá-la, a socket rapidamente percebe que a conexão foi fechada do outro lado, foi quebrada (daí o nome _broken pipe_). Muito comum ao começar a usar um pool!

O [segundo motivo](http://www.mikeschubert.com/archives/2006/08/javanetsocketex.html) é que você pode estar tratando suas transações sem o devido cuidado: esquecendo de fazer o _commit_ ou o _rollback_ em alguns casos. O banco de dados então pode matar essa conexão depois de algum timeout de transação, porém o seu pool não sabe disso, e quando for utilizar essa conexão, ela está quebrada!

Solução rápida? Configurar o seu pool para testar se as conexões continuam válidas. No [C3P0](http://www.mchange.com/projects/c3p0/index.html), pool de conexões que recomendamos fortemente, quando usado com o Hibernate, basta fazer no seu `hibernate.cfg.xml`:

\[xml\] <property name="hibernate.connection.provider\_class"> org.hibernate.connection.C3P0ConnectionProvider </property> <property name="hibernate.c3p0.min\_size">1</property> <property name="hibernate.c3p0.max\_size">20</property> <property name="hibernate.c3p0.timeout">30</property> <property name="hibernate.c3p0.idle\_test\_period">100</property> \[/xml\]

É a configuração `hibernate.c3p0.idle_test_period` que resolve o broken pipe. Nesse caso o C3P0 fara essa verificação de maneira assíncrona: ele cria threads (3 por padrão) que checam de tanto em tanto tempo (100 segundos nesse caso) se alguma das conexões do pool está inválida (broken pipe é um dos casos). Na existência de uma conexão assim, essa será eliminada do seu pool! Você ainda pode ter um azar muito grande, pois uma conexão pode ter algum problema logo depois que a thread a verificou! Se você quer ter uma confiabilidade de 100% em relação a suas conexões, você pode configurar a variável [testConnectionOnCheckout](http://www.mchange.com/projects/c3p0/index.html#testConnectionOnCheckout) no arquivo `c3p0.properties` que deve ser colocado no seu classpath. Isso não é muito recomendado, pois toda vez que uma conexão é pega do pool, alguma forma de ping será feito no banco de dados para saber se ela é válida, perdendo um pouco de performance.

Vale lembrar de que isso não é motivo para você se descuidar no tratamento de transações, centralizando isso dentro de um interceptador/filtro que faça o uso correto do `try`, `catch` e `finally`, precavendo-se de qualquer vazamento. Transações, assim como qualquer outro recurso caro (arquivos, conexões, sockets, threads, etc...), deve ter seu ciclo de vida tratado com atenção, de preferência de maneira isolada.

Caso você não use Hibernate, Jerônimo Mozer mostra [como usar o C3P0 programaticamente](http://netbeando.blogspot.com/2009/03/broken-pipe-solucao-correta.html).

Mais detalhes podem ser vistos nas [configurações de teste de conexões do C3P0](http://www.mchange.com/projects/c3p0/index.html#configuring_connection_testing), [detalhes do seu funcionamento com o Hibernate](http://www.mchange.com/projects/c3p0/index.html#hibernate-specific) e a [página do próprio Hibernate sobre esse pool](http://community.jboss.org/wiki/HowToconfiguretheC3P0connectionpool), mas que se encontra um pouco defasada. Também vemos muitos detalhes como esses no [nosso curso FJ-25](http://www.caelum.com.br/curso/fj-25-persistencia-jpa2-hibernate/).
