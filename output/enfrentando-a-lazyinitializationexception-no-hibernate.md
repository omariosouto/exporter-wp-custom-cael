---
title: "Enfrentando a LazyInitializationException no Hibernate"
date: "2009-10-13"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Sem dúvida o primeiro balde de água fria que levamos ao começar a trabalhar com o Hibernate é a `LazyInitializationException`. Afinal, quando e por que ela acontece?

Para chegar lá precisamos de um exemplo de relacionamento: uma nota fiscal tem vários itens de compra, um produto tem uma categoria:

\[java\] @Entity class NotaFiscal { … @OneToMany List<Item> items; } \[/java\]

Depois de mapeadas nossas entidades, podemos facilmente percorrer esse relacionamento através do acesso a um getter:

\[java\] NotaFiscal nf = (NotaFiscal) session.load(NotaFiscal.class, 42); List<Item> items = nf.getItems(); \[/java\] Nesse caso o Hibernate fará dois `select`s: um para pegar os dados da `NotaFiscal` com _id_ 42, e outro procurando todos os items `where nota_fiscal_id=42`. Há também a possibilidade de indicar para o Hibernate de que tudo deva ser feito em um único `join`, para isso basta configurar que esse relacionamento deve ser pego (_fetched_) de maneira ansiosa, prontamente (_eager_):

\[java\] class NotaFiscal { ... @OneToMany(fetch=FetchType.EAGER) List<Item> items; } \[/java\]

Vale relembrar os defaults do Hibernate: todos os relacionamentos `*ToOne` são `EAGER`, e os `*ToMany` são `LAZY`, isso porque relações `*toMany` são provavelmente mais custosas, trazendo mais objetos para a memória.

É muito interessante ter relacionamentos `LAZY`: os dados são puxados apenas quando realmente necessários. Ao mesmo tempo deve-se tomar cuidado: pode gerar o problema das _[n+1 queries](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/)_, e muitas vezes sabemos que tal relacionamento será tão utilizado, que deve ser feito de maneira `EAGER`, evitando mais uma query ser disparada ao banco de dados.

Como exatamente funciona o relacionamento lazy? O Hibernate tira proveito de proxies dinâmicas: ele te devolve objetos que fingem ser listas nesse caso, e quando você invoca algum método deles, o Hibernate então faz a respectiva query para carregar o relacionamento. E há um momento em que isso falha: quando a sessão que carregou o objeto já estiver fechada, como no seguinte código caso o relacionamento seja `LAZY`:

\[java\] Session session = sessionFactory.openSession(); NotaFiscal nf = (NotaFiscal) session.load(NotaFiscal.class, 42); session.close();

List<Item> items = nf.getItems(); System.out.println("numero de pedidos dessa nota:" + items.size()); \[/java\] Esse código tem o seguinte resultado:

`**org.hibernate.LazyInitializationException: failed to lazily initialize a collection - no session or session was closed.**`

Ao invocar o método `size()` a proxy dinâmica devolvida pelo `getItems()` tenta se conectar ao banco para puxar todos os itens dessa nota, porém a sessão já foi fechada! Por que o Hibernate não reabre a sessão? Como [eles mesmos afirmam](http://community.jboss.org/wiki/OpenSessioninView#Why_cant_Hibernate_just_load_objects_on_demand), não faria mais sentido ter demarcação de transação e nem de abertura e fechamento de sessões! E essa responsabilidade seria demais para um framework ORM, pois não é tão óbvio decidir sobre o ciclo de vida de objetos caros, como a `Session` e a `Connection`, que poderiam acabar sendo abertas inúmeras vezes para a renderização de uma única página! Outros frameworks de mapeamento objeto relacional usam essa outra abordagem e reconectam a sessão e conexão caso necessário, que eu também considero uma má escolha.

Para resolver este caso acima parece simples: sempre fechar a sessão ao término do trabalho. Mas e quando estamos trabalhando na Web? Após pegarmos os dados necessários no nosso controlador, fechamos a sessão e passamos os objetos ao JSP através de atributos. O JSP, ao acessar um getter do seu objeto para fazer um loop, como `${notaFiscal.items}`, recebe `LazyInitializationException` da mesma forma. Em um primeiro momento o desenvolvedor muda o relacionamento para `EAGER`, mas isso gera uma enorme sobrecarga, pois provavelmente em muitos lugares não era necessário carregar todos os itens da compra sempre que uma determinada nota fiscal é requisitada.

Como então evitar a `LazyInitializationException` sem modificar o relacionamento para `EAGER`?

**Open Session In View**

A solução é manter a session aberta durante a renderização da camada de visualização, ou como o Hibernate chama esse pequeno padrão: [open session in view](http://community.jboss.org/wiki/OpenSessioninView). A idéia é bastante simples: a sessão deve ser mantida aberta até o fim da renderização do JSP (ou de qualquer outra camada de apresentação). Isso pode ser obtido através da implementação de um Servlet Filter, algum tipo de interceptador do seu framework preferido ou [até mesmo aspectos](http://community.jboss.org/wiki/SessionhandlingwithAOP).

O Spring foi sem dúvida um dos primeiros frameworks a já trazer classes para isso embutidas (assim como também foi o primeiro a fazer o wrap da `HibernateException` dentro de uma exceção _unchecked_, pois até o Hibernate 2.x essa exceção era _checked_). Há as classes [OpenSessionInViewInterceptor](http://static.springsource.org/spring/docs/2.5.6/api/org/springframework/orm/hibernate3/support/OpenSessionInViewInterceptor.html) e sua análoga [OpenEntityManagerInViewInterceptor](http://static.springsource.org/spring/docs/2.5.6/api/org/springframework/orm/jpa/support/OpenEntityManagerInViewInterceptor.html). No VRaptor, além de você pode usar os componentes embutidos do Spring, já [existem componentes que fazem o mesmo trabalho](http://vraptor.caelum.com.br/documentacao/componentes-utilitarios-opcionais/) (ver componentes embutidos).

Aproveite para ler aqui no blog da Caelum a respeito de outras [exceptions frequentes no Hibernate](https://blog.caelum.com.br/transientobjectexception-lazyinitializationexception-e-outras-famosas-do-hibernate/), sobre os [estados de uma entidade na JPA](https://blog.caelum.com.br/entidades-managed-transient-e-detached-no-hibernate-e-jpa/), [hábitos importantes para todo desenvolvedor Hibernate](https://blog.caelum.com.br/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/) e [mapeamento de herança](https://blog.caelum.com.br/jpa-com-hibernate-heranca-e-mapeamentos/), além de muitos [outros artigos relacionados ao framework](https://blog.caelum.com.br/category/hibernate/). O nosso [curso FJ-25 trata bastante de Hibernate e JPA2](http://www.caelum.com.br/curso/fj-25-persistencia-jpa2-hibernate/) com detalhes importantes do dia a dia como esses.
