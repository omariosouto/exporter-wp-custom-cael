---
title: "JPA com Hibernate: Herança e Mapeamentos"
date: "2007-03-04"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Essas semanas postarei algumas dicas rápidas no uso da JPA com Hibernate. São alguns pontos que sempre aparecem no desenvolvimento, referentes a performance, elegância e facilidades. Alguns tópicos já são de amplo conhecimento dos usuários do Hibernate, porém na JPA alguns deles são utilizados diferentemente.

**Cuidado com herança por `InheritanceType.JOINED`**

Aqui na Caelum, [conforme discutido anteriormente](https://blog.caelum.com.br/jpa-com-hibernate-heranca-e-mapeamentos/), usamos herança com muito critério. Herança na JPA é mapeada com `@Inheritance(strategy=InheritanceType.SINGLE_TABLE)` por padrão, isto é, ele vai utilizar uma única tabela para guardar todos os dados de todas as classes filhas: não há normalização e uma coluna (o `discriminator`, por default `DTYPE` no Hibernate) será utilizada para distinguir entre as possíveis subclasses.

Muitos administradores de banco de dados reclamam dessa estratégia, sendo que a mais elegante é a `InheritanceType.JOINED`, onde cada classe terá uma tabela, mas sem repetir colunas. As tabelas que representam as classes filhas possuem uma chave estrangeira para a tabela que representa a mãe, normalizando o banco nesse aspecto.

O grande problema dessa estratégia são as queries polimórficas: no caso de você possuir uma classe mãe `Pessoa` e duas filhas `PessoaFisica` e `PessoaJuridica`, ao procurar por uma `Pessoa` pela sua chave primária o hibernate vai gerar um join entre **todas** essas tabelas (pode variar um pouco de acordo com o dialeto usado), já que não possuímos o `discriminator` nesse caso.

**Mapear resultados em vez de trabalhar com `List<Object[]>`**

Se executamos uma Query em que nosso select não escolhe apenas um tipo de valor a ser retornado, recebemos uma `List<Object[]>` como resultado. Por exemplo, um `select e1.atributoInteiro, e2.atributoString from Entidade1 as e1, Entidade2 as e2 where...` retornaria uma `List<Object[]>` em que, para cada item da lista, a primeira posição da array é um `Integer` referente ao `atributoInteiro` da `Entidade1` e a segunda posição é uma `String` referente ao `atributoString` da `Entidade2`.

Tanto a JPA quanto o Hibernate permitem retornar qualquer tipo de objeto através da [clausula de select, utilizando um construtor](http://docs.jboss.org/hibernate/core/3.3/reference/en/html/queryhql.html#queryhql-tuple). Podemos mudar a query anterior para `select new br.com.caelum.Bean(e1.atributoInteiro, e2.atributoString) from Entidade1 as e1, Entidade2 as e2 where...` para receber um `List<AlgumBean>` como resultado, desde que a classe `br.com.caelum.AlgumBean` possua um construtor que receba um `Integer` e uma `String`. Muito mais elegante que precisar percorrer uma `List<Object[]>` e encher nosso código de castings. Excelente parar gerar relatórios e já devolver os dados organizados em beans específicos.

Para mapeamentos mais avançados o Hibernate possui a interface `ResultTransformer` e sua factory `Transformers`. A JPA define a `SQLResultSetMapping` e outras anotações, mas para o uso de native queries.
