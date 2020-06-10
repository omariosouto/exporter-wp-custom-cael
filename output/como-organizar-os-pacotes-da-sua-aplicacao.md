---
title: "Como organizar os pacotes da sua aplicação?"
date: "2014-02-18"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Abra seu projeto atual e dê uma olhada na divisão de pacotes. Você consegue entendê-la? Ela faz sentido? Agrupar classes em pacotes pode ser sim um desafio. E justamente por isso, é comum a dúvida: **quais pacotes devo criar?**

Essa é uma pergunta bastante difícil de ser respondida, e gera discussão entre desenvolvedores de todos os níveis de experiência. Utilizamos pacotes porque sentimos a necessidade de agrupar classes de acordo com algum critério. É bem comum então vermos pacotes como _br.com.caelum.dao_, onde colocamos todos nossos DAOs, ou mesmo, _br.com.caelum.dominio,_ onde colocamos todas as nossas classes de domínio.

Não há uma "verdade" sobre como criar ou nomear pacotes. Mas quais são as boas práticas na hora de se criar um pacote?

Um bom pacote é aquele que facilita o reuso. Mas como isso acontece? A ideia é que **todas as classes em um pacote sejam reutilizáveis, ou que nenhuma seja.** Ou seja, não misture classes que são reutilizáveis com classes que não são. Com essa separação, conseguimos levar pacotes específicos de um projeto para outro, sem levar classes "inúteis".

**Evite também dependências cíclicas.** Uma dependência cíclica acontece quando um pacote "depende" do outro. Ou seja, temos classes no pacote1 que dependem de classes do pacote2, e vice-versa. Mas qual o problema disso? Manutenção e reuso. A partir do momento que pacotes passam a depender entre si, o reuso diminui, afinal, precisamos reutilizar ambos sempre juntos. A manutenção também torna-se mais complicada, afinal mudanças em um pacote podem impactar em mudanças também no outro pacote.

![](http://s3.amazonaws.com/caelum-online-public/blog/dependencia-ciclica.png)

**Pacotes devem ser totalmente estáveis ou totalmente instáveis.** Por estável, quero dizer que o pacote tende a mudar menos. Pacotes estáveis são geralmente aqueles em que você coloca suas interfaces ou classes abstratas. Pacotes voláteis (ou instáveis) são aqueles onde queremos mudar. Geralmente guardamos as nossas classes de domínio, serviços e outras regras de negócio concretas lá. Essa separação é importante para facilitar a implementação do próximo princípio.

**Pacotes devem sempre depender de pacotes mais estáveis do que ele próprio.** A ideia é que cada pacote dependa sempre de pacotes mais estáveis do que ele próprio. Dessa forma, a propagação de mudanças é reduzida, e a manutenção torna-se mais fácil. Ou seja, os seus pacotes que contém regras de negócio (e é um pacote mais volátil) deve depender de pacotes mais estáveis, como o pacote onde você põe suas interfaces de acesso a dados (repositórios) ou mesmo as interfaces dos serviços que essas regras consomem (pacotes esses que são compostos por interfaces e, por consequência, são mais estáveis).

**Resumindo, pense em deixar classes perto, ou para facilitar o reuso do pacote entre outros projetos, ou para agrupar classes que mudam juntas.**

Veja que essas ideias também fazem sentido para classes, e foi o que discuti em [meu post anterior sobre os princípios SOLID](https://blog.caelum.com.br/principios-do-codigo-solido-na-orientacao-a-objetos/). Os princípios que comentei acima, aliás, também são discutidos pelo [Robert Martin em seus livros e artigos](http://www.objectmentor.com/resources/articles/granularity.pdf#6).

**Gerenciar dependências entre pacotes é tão trabalhoso quanto gerenciar dependências entre classes.** Mas, sem dúvida, a longo prazo, o esforço se paga. Discutimos boas práticas de código em nosso [curso presencial de arquitetura e design](http://www.caelum.com.br/curso-arquitetura-java/), em [nosso livro](http://www.casadocodigo.com.br/products/livro-arquitetura-java) sobre o assunto, e mesmo no meu [livro](http://www.tddnomundoreal.com.br) e [curso online](http://www.alura.com.br/cursos-online-agile/tdd) sobre TDD.

E você, como tem separado seus pacotes? Usa alguma regra diferente das que mencionei aqui?
