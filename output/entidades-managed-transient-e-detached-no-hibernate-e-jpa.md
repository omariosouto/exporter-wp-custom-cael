---
title: "Entidades Managed, Transient e Detached no Hibernate e JPA"
date: "2006-11-23"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Distinguir entre os estados de uma entidade no JPA/Hibernate é difícil no início. Um objeto é dito transiente quando não tem representação no banco de dados e nem o `EntityManager` o conhece, como abaixo:

\[java\] Cliente c = new Cliente(); \[/java\]

Aqui, qualquer mudança no objeto referido por `c` não gerará nenhum tipo de insert ou update no banco de dados. O oposto é quando o objeto existe no banco de dados e o `EntityManager` em questão possui uma referência para ele, essa entidade está _managed_, gerenciada pelo `EntityManager`. Considere `em` uma referência a um `EntityManager` no seguinte exemplo:

\[java\] Cliente c = new Cliente(); // transiente em.persist(c); // gerenciado \[/java\]

Ou ainda:

\[java\] Cliente c = em.find(Cliente.class, 1); // gerenciado \[/java\]

Quando uma entidade está managed, qualquer mudança em seu estado (como uma chamada de setter) resultará em uma atualização no banco de dados no momento do commit.

O último caso é quando a entidade representa algo que possivelmente está no banco de dados, mas o `EntityManager` o desconhece: a entidade está fora do contexto, _detached_. Exemplo:

\[java\] Cliente c = new Cliente(); c.setId(1); \[/java\]

Uma entidade também está _detached_ quando o `EntityManager` de onde tiramos esse `Cliente` (por exemplo, quando fizemos um `find` ou vindo de uma `Query`) já não está mais aberta. Qualquer mudança nessa referência obviamente não surtirá efeito no banco de dados. Para que essa mudança faça efeito, isto é, para _reattach_ o entidade, antes precisamos amarrá-la ao contexto de persistência. Repare que no `EntityManager` já pode existir uma entidade `Cliente` com esse mesmo id, imagine então o que aconteceria se tivéssemos um método que se chamasse `reattach` ou `update`?

Por isso o método é o `merge`. Ele junta a possível entidade com mesmo id que se encontra no `EntityManager` com a passada como argumento, e devolve a que está _managed_. O método `merge` **não** faz _reattach_. Então:

\[java\] Cliente c = new Cliente(); c.setId(1); em.merge(c); c.setNome("Cliente com nome alterado"); \[/java\]

Não surtirá efeito! Aqui você precisava antes ter pego o que o merge devolveu. Repare na pequena alteração:

\[java\] Cliente c = new Cliente(); c.setId(1); c = em.merge(c); c.setNome("Cliente com nome alterado"); \[/java\]

Pronto. Uma pequena introdução sobre o ciclo de vida de uma entidade em relação a um `EntityManager`: _transient_ (a especificação chama de _new_), _managed_ e _detached_! Ainda temos o estado _removed_, quando uma entidade está marcada para a remoção.
