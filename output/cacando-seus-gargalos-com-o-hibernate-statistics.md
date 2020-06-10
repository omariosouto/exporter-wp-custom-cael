---
title: "Caçando seus gargalos com o Hibernate Statistics"
date: "2011-06-08"
author: "gmoreira"
authorEmail: "guilherme.moreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![](https://blog.caelum.com.br/wp-content/uploads/2011/06/graficos_geral-300x145.png "graficos_geral")](https://blog.caelum.com.br/wp-content/uploads/2011/06/graficos_geral.png) Uma reclamação frequente dos novos usuários do Hibernate é o excesso de queries realizadas pelo framework, afetando não só a performance mas também a escalabilidade da aplicação. Será realmente culpa do framework? Práticas simples podem ajudar a [enfrentar esses gargalos ao usar o Hibernate](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/), mas como detectá-los numa aplicação que já foi mapeada sem muito cuidado? Uma das ferramentas mais úteis para o desenvolvedor na hora de otimizar e corrigir problemas com o Hibernate, além dos logs, são as estatísticas. Através do _Hibernate Statistics_ poderemos dizer quantas vezes cada entidade, coleção (relacionamento) e query está sendo carregada/executada, quantas vezes o cache foi usado, tempo médio de cada query, etc. Em mão desses dados, temos mais pistas de onde colocar cache, configurar batchsize e fazer joins com fetch eager.

Para habilitar o uso das estatísticas do Hibernate temos duas formas. A primeira através do arquivo de configuração do hibernate e a segunda programaticamente. Ao escolher a maneira programática temos a vantagem de poder habilitar e desabilitar a coleta de estatísticas a qualquer momento. Mas não se preocupe, pois podemos usar as duas ao mesmo tempo!

Para habilitar através do arquivo de configuração basta adicionar a seguinte propriedade no hibernate.cfg.xml:

\[xml\] <property name="hibernate.generate\_statistics">true</property> \[/xml\]

Programaticamente usamos o `SessionFactory`:

\[java\] sessionFactory.getStatistics().setStatisticsEnabled(true); \[/java\]

Passada etapa de configuração vamos ver um exemplo de ajuda que o hibernate statistics pode dar. Para o exemplo abaixo temos uma classe utilitária para facilitar a criação de `Session`s e das `Statistics`:

\[java\] public class HibernateUtil {

private static SessionFactory factory;

static { factory = new Configuration().configure().buildSessionFactory(); }

public static Session openSession() { return factory.openSession(); }

public static Statistics getStatistics() { if (!factory.getStatistics().isStatisticsEnabled()) { factory.getStatistics().setStatisticsEnabled(true); } return factory.getStatistics(); } } \[/java\]

O indicado nesses casos seria usar algum container de Injeção de Dependências para fazer este controle, mas esse é um outro assunto.

O caso abaixo é o exemplo `Pessoa` que possui uma lista de `Telefone`. Nesse código estamos simulando a criação de 10 pessoas e seus respectivos telefones. Porém se notarmos bem o código, estamos salvando primeiro o telefone e depois a pessoa. Essa ordem acabará gerando (dependendo do dialeto hibernate utilizado no banco) um `update` para cada par de Pessoa e Telefone que inserirmos, pois o hibernate ao salvar o telefone ainda não tem o id da pessoa para enviar no `insert` de telefone.

Com o Hibernate Statistics podemos conferir quantos updates foram disparados através do método `getEntityUpdateCount` e conferir se realmente estamos disparando as operações da maneira correta:

\[java\] public class TesteQuantidadeUpdatesOrdemErrada { public static void main(String\[\] args) { Session session = HibernateUtil.openSession(); Statistics statistics = HibernateUtil.getStatistics(); long antes = statistics.getEntityUpdateCount(); for (int i = 0; i < 10; i++) { Transaction transaction = session.beginTransaction(); Pessoa p = new Pessoa(); p.setNome("Caelum "+1); Telefone t1 = new Telefone(); t1.setValor("5571-2751"); t1.setTipo("comercial"); t1.setPessoa(p); session.save(t1); session.save(p); transaction.commit(); } long depois = statistics.getEntityUpdateCount(); System.out.println(depois-antes); } } \[/java\]

Ao rodar o código temos a saída **"Updates disparados:10"**. Graças ao Hibernate Statistics foi fácil de ver esses updates a mais, sem ter de ficar verificando os enormes logs de queries que uma aplicação pode gerar. Para resolver essa pendência basta inverter a ordem dos `saves` que teremos **0** _updates_ no hibernate statistics.

O código abaixo insere dez pessoas no banco:

\[java\] public static void main(String\[\] args) { Statistics statistics = HibernateUtil.getStatistics();

long abertas = statistics.getSessionOpenCount(); long fechadas = statistics.getSessionCloseCount(); System.out.println("Sessoes nao fechadas:"+(abertas-fechadas)); for (int i = 0; i < 10; i++) { Session session = HibernateUtil.openSession(); Transaction transaction = session.beginTransaction(); try{ Pessoa p = new Pessoa(); p.setNome("Caelum "+1); session.save(p); transaction.commit(); session.close(); }catch (Exception e) { System.err.println(e); transaction.rollback(); } } abertas = statistics.getSessionOpenCount(); fechadas = statistics.getSessionCloseCount(); System.out.println("Sessoes nao fechadas:"+(abertas-fechadas)); } } \[/java\]

Em uma execução normal teríamos todas as sessões fechadas sem problema algum, porém vamos simular um erro a cada 3 `save`s:

\[java\] Pessoa p = new Pessoa(); p.setNome("Caelum " + 1); if (i%3 == 0) { throw new RuntimeException(); } session.save(p); transaction.commit(); session.close(); \[/java\]

Agora vemos através do hibernate statistics que nosso `try/catch` está falho e que, caso algo saia errado, esquecemos de fechar a sessão no `catch`. O certo neste caso era fechar a sessão no bloco `finally`, evitando que por causa de esquecimento alguém não tenha fechado a sessão.

\[java\] }catch (Exception e) { System.err.println(e); transaction.rollback(); }finally{ session.close(); } \[/java\]

Agora o hibernate statistics mostrará que todas as sessões foram fechadas para esse caso. Vale lembrar que ainda devemos verificar se o `close` terminou com sucesso, eliminando as possibilidades de uma sessão ou transação permanecer aberta (em último caso, deve ser logado com prioridade).

Podemos até coletar informações sobre as transações, adicionando as linhas de código abaixo, mostrando a quantidade de transações totais e as que deram certo:

\[java\] System.out.println("Quantidade total de Transacoes:" + statistics.getTransactionCount()); System.out.println("Quantidade de Transacoes ok:" + statistics.getSuccessfulTransactionCount()); \[/java\]

É de grande ajuda que os sistemas tenham uma página que possa mostrar algumas dessas informaçãoes a qualquer hora. Inclusive o hibernate tem um método que mostra no log um resumo de todas as estatísticas importantes, para ver esse imprimi-lo basta invocar `logSummary` da classe `Statistics`.

O Hibernate Statistics está cheio de métodos uteis, uma pequena lista dos principais:

- **`getEntity[Delete,Insert,Load,Update]Count():long`** _serve para obter informações sobre os CRUD das entidades_
- **`getQueryExecutionMaxTimeQueryString():String`** _retorna a consulta mais demorada_
- **`getQueryExecutionMaxTime():long`** _retorna o tempo de execução da consulta mais demorada_
- **`getSecondLevelCacheHitCount():long`** _retorna quantas vezes o hibernate conseguiu a informação que queria através do cache_
- **`getSecondLevelCacheMissCount():long`** _retorna quantas vezes o hibernate teve que ir no banco buscar a informação que queria pois ela não estava no cache_

Existem muitos outros métodos interessantes que podem ser conferidos no [javadoc da classe `Statistics`](http://docs.jboss.org/hibernate/core/3.6/javadocs/org/hibernate/stat/Statistics.html). Suas informações vai te ajudar a decidir que queries devem ser reescritas, colocadas no second level cache, quais relacionamentos devem ser carregados em modo batch, etc.

Apenas as estatísticas do Hibernate não vão ajudar a resolver todos os problemas da aplicação, porém facilitará muito quando você tiver que achar pequenas falhas, inconsistências, lentidão e excessos na comunicação aplicação-banco. [Monitorar muito bem sua aplicação é essencial](https://blog.caelum.com.br/logar-e-preciso-debugar-nao-e-preciso/), para não depender apenas de utilizar profilings e debuggers, que deveriam ser ferramentas apenas nos casos mais graves.
