---
title: "TransientObjectException, LazyInitializationException e outras famosas do Hibernate"
date: "2006-11-02"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Para quem desenvolve com Hibernate, sem dúvida as exceptions que aparecem mais são a `TransientObjectException` (TOE), `LazyInitializationException` (LIE) e a `PersistentObjectException` (POE). Semana passada tive o prazer de ministrar um treinamento de EJB3 e JSF para o pessoal da Petrobras de 5 cidades diferentes e durante o curso várias TOEs, POEs e LIEs apareceram. Vamos ver quando cada uma ocorre.

Estou usando aqui a API do Java Persistence, mas a relação é direta com o Hibernate, já que este é o meu provider. Considere duas entidades, `Autor` e `Livro`, cada uma com atributos triviais, um `id` que é `@GeneratedValue` e uma relação `@ManyToMany` entre elas, sendo `Livro` que possui o lado `mappedBy`. Vamos ao código: \[java\] Autor a = new Autor(); Livro l = new Livro(); a.setLivros(Collections.singleton(l)); manager.persist(a); \[/java\]

Resultado:

`org.hibernate.TransientObjectException: object references an unsaved transient instance - save the transient instance before flushing: br.com.caelum.hibernate.testes.Livro`

Essa exception não ocorreria se você tivesse chamado `manager.persist(l);` dentro dessa mesma transação, ou utilizasse `cascade=CascadeType.PERSIST` no relacionamento. Um objeto transiente, ao ser persistido, não pode se referenciar a outros objetos transientes, a não ser que haja cascade!

Agora considere: \[java\] Autor a = new Autor(); a.setId(1L); a.setNome("paulo"); manager.persist(a); \[/java\]

Resultado:

`org.hibernate.PersistentObjectException: detached entity passed to persist: br.com.caelum.hibernate.testes.Autor`

O id de `Autor` está anotado com `@GeneratedValue`, quando ele percebe que há um id setado ele imagina que provavelmente esse objeto já deve existir no banco de dados (repare que ele não faz select, se você colocar um `id` que não existe, a mesma exception ocorrerá). `persist` não aceita objetos detached, apenas transient e managed. Essa exception não ocorreria se seu id não fosse `@GeneratedValue`, ou se você tivesse puxado esse `Autor` através do `manager.find`, por exemplo.

Vamos passar para o método `merge`. Porque o utilizamos tanto? Pois na web, quando recebemos os parâmetros e populamos nosso `Entity` para atualizá-lo, ele está detached: não foi pego através do `EntityManager`/`Session`, porém possui um `id` setado (o que anteriormente gerou a `PersistentObjectException`). O `merge` tem um detalhe importantíssimo: ele não vai passar o estado daquela entidade para managed (como faz o `saveOrUpdate` do Hibernate), e sim devolver uma versão da mesma entidade que seja managed (como fazia o velho `saveOrUpdateCopy`), em outras palavras, futuras mudanças na entidade passada não surtirão efeito. A outra grande diferença é que agora podemos passar como argumento um objeto transiente que já possua um `id` setado:

\[java\] Autor a = new Autor(); a.setId(1L); a.setNome("livro1"); manager.merge(a);

a.setNome("livro2"); \[/java\]

O código irá atualizar o nome do `Livro` de `id` 1 para _livro1_. Repare que a mudança para _livro2_ não surtirá efeito, já que o `merge` não faz attach do objeto passado como argumento, então nesse caso ele continua detached. Uma opção seria você pegar o retorno do método `merge`, que é o mesmo `Autor`, porém agora managed. A [documentação do hibernate sobre o `merge`](http://docs.jboss.org/hibernate/entitymanager/3.5/reference/en/html/objectstate.html) parece ser melhor que [a especificação](http://jcp.org/en/jsr/detail?id=220).

Aqui temos de ter muito cuidado, como não puxamos esse `Autor` pelo `entityManager`, caso ele possua alguns livros no seu relacionamento, nesse `merge` perderíamos todas essas informações!

Parece fácil evitar todas essas exceptions, então porque eu disse que ocorreram tanto no curso? Bem, estávamos usando a JPA de dentro de um container, não standalone. Considere então um session bean de granularidade fina que esteja agindo apenas como um dao (esse não é o ideal, mas fica para os testes): \[java\] @Remote interface SessionBeanRemote { void persiste(Autor a); void persiste(Livro l); Livro buscaLivroPorNome(String nome); } \[/java\]

E considere o cliente:

\[java\] Livro l = new Livro(); sessionBeanRemoto.persiste(l);

Autor a = new Autor(); a.setLivros(Collections.singleton(l)); manager.persiste(a); \[/java\]

Repare como esse código é **muito parecido** com o primeiro desse post, porém já persistindo o `Livro` anteriormente, para evitar a `TransientObjectException`. Mas adivinhe, aqui é lançada uma TOE! Isso ocorre porque, apesar do `Livro` ter sido persistido, no cliente remoto ele estará transiente, pois sua chave primária não foi populada, já que o objeto foi serializado e só no servidor se encontra uma versão desse `Livro` com sua respectiva chave! Se fosse no EJB 2.x, onde o Entity bean também é um componente, isso não ocorreria. Mas aqui, fora do container, o Entity bean age realmente como um valeu object: não há ligação dele com o servidor.

Para resolver isso temos alguns idiomismos, mudaríamos nosso bean remoto para: \[java\] @Remote interface SessionBeanRemote { Autor persiste(Autor a); Livro persiste(Livro l); Livro buscaLivroPorNome(String nome); } \[/java\]

Um tanto estranho, e na nossa implementação devolveríamos o próprio argumento:

\[java\] @Stateless class SessionBean implements SessionBeanRemote { @PersistentContext private EntityManager manager;

public Livro persiste(Livro l) { manager.persist(l); return l; } // outros metodos } \[/java\]

Dessa maneira passaríamos de volta ao cliente uma versão detached do novo livro: agora com ID! Nosso cliente ficaria:

\[java\] Livro l = new Livro(); l = sessionBeanRemoto.persiste(l); // agora pegamos o retorno!

Autor a = new Autor(); a.setLivros(Collections.singleton(l)); manager.persiste(a); \[/java\]

Existem outras alternativas, como retornar apenas a chave primária, utilizar session beans com maior granularidade, etc.

E a `LazyInitializationException` que mencionei? Usando hibernate ou JPA standalone estamos cansados de saber como evitá-la: basta [manter a sessão aberta durante a renderização na camada de visualização](http://community.jboss.org/wiki/OpenSessioninView). Mas com EJB a história é outra, sendo muito mais sutil: chame o método `buscaPorNome`, ele vai retornar ao cliente um `Livro` detached. Adivinhe o que acontece ao invocar o `getAutores()` no cliente?
