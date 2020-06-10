---
title: "Screencast - Hibernate e Concorrência Otimista na Web com VRaptor"
date: "2008-03-05"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

É com prazer, depois de tanto tempo, que anuncio o **segundo screencast** da Caelum.

O vídeo trata de um dos recursos pouco explorados no Hibernate: **[Controle de Concorrência Otimista](http://docs.jboss.org/hibernate/core/3.3/reference/en/html/transactions.html#transactions-optimistic)**, para lidar com problemas de edição simultânea (concorrência) nos registros. O fato curioso é que tenho observado em diversos projetos a preferência por [Locks Pessimistas](http://docs.jboss.org/hibernate/core/3.3/reference/en/html/transactions.html#transactions-locking), que em grande parte dos casos não são a melhor escolha. Da própria documentação do Hibernate:

> The only approach that is consistent with high concurrency and high scalability is optimistic concurrency control with versioning.
> 
> (...)
> 
> It is not intended that users spend much time worring about locking strategies. Its usually enough to specify an isolation level for the JDBC connections and then simply let the database do all the work.

_Como podemos nos beneficiar do controle de concorrência otimista (também conhecido como lock otimista)?_ Para explorar o assunto, durante o screencast vamos enriquecendo uma aplicação Web existente, adicionando funcionalidades de edição simples de registros e resolvendo o problema de edição simultânea.

A aplicação Web utilizada usa e abusa do [VRaptor](http://www.vraptor.org) como controlador MVC e várias dicas sobre o framework são abordadas durante o vídeo. Porém, a mensagem vale para qualquer aplicação Web. Mesmo as que não usam [VRaptor](http://www.vraptor.org).

Como sempre, o screencast mostra apenas uma das alternativas para solução do problema. Fica como lição de casa, testar outras possibildades:

1) Para não fazer o controle transacional dentro do Dao e forçar a checagem de versão (_version check_), no lugar do `transaction.commit()` poderia ter sido usado o `session.lock(objeto, LockMode.READ)`. O método lock serve principalmente para a abordagem pessimista, porém `LockMode.READ` serve justamente para forçar a checagem de versão no caso otimista.

2) No lugar de tirar a entidade do cache de primeiro nível com `session.evict(entidade)` e consultá-la novamente para ter a versão mais nova do banco, bastaria usar o método `session.refresh(entidade)` que atualiza a instancia, passando direto pelos caches.

<iframe src="http://player.vimeo.com/video/18800828" width="440" height="330" frameborder="0"></iframe>

_[Acesse diretamente o screencast no Vimeo.](http://vimeo.com/18800828)_

Não deixe de colocar o seu comentário com outras alternativas!
