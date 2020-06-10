---
title: "Adequar o banco às entidades ou o contrário?"
date: "2011-08-09"
author: "erich.egert"
authorEmail: "erich.egert@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma das features interessantes do [Hibernate](http://www.hibernate.org/ "Hibernate") é gerar a [Data Definition Language](http://en.wikipedia.org/wiki/Data_Definition_Language "Data Definition Language") para criação da estrutura de tabelas do banco de dados (schema). Tudo que precisamos fazer é pensar em nosso modelo Orientado a Objeto que o banco é gerado automaticamente a partir das entidades. Aparentemente o processo de pensar na modelagem do banco de dados tornou-se obsoleto: é aí que mora o perigo.

[![mapeamento de banco de dados a orientacao a objetos](https://blog.caelum.com.br/wp-content/uploads/2011/08/database-application.jpg "database-application")](https://blog.caelum.com.br/wp-content/uploads/2011/08/database-application.jpg)

Na orientação a objetos é preferível favorecer especialização através da divisão de responsabilidades. Em vez de criar uma classe gigantesca devemos quebrá-la em pequenas classes.

Quando o assunto é banco de dados a análise é bem diferente. No modelo relacional, a preocupação é normalização dos dados e a modelagem ao mesmo tempo tende a ser pensada de forma a evitar a necessidade de alguns joins que seriam mais caros. Muitas vezes é preferível criar uma tabela com várias colunas com todas as informações pertinentes a criar várias tabelas especializadas. Para ilustrar, considere as entidades:

\[java\] @Entity public class Produto { @Id @GeneratedValue private Long id; private String nome; private BigDecimal preco; private Calendar inicioPeriodoPromocional; private Calendar fimPeriodoPromocional; // outros atributos e metodos }

@Entity public class Promocao { private Calendar inicio; private Calendar fim; //outros atributos e metodos } \[/java\]

Suponha que gostaríamos de saber se uma promoção é aplicável a um produto e por quantos dias podemos aproveitar uma promoção. Colocar esse código dentro de um controller ou managed bean não parece uma boa idéia. Esse trecho rapidamente reaparecerá em outro lugar. Onde então podemos criar métodos para essa análise? Poderíamos começar criando a classe `Intervalo`:

\[java\] public class Intervalo { private Calendar inicio; private Calendar fim; } \[/java\]

E em vez de `Produto` e `Promocao` guardarem referências para duas datas, guardariam para um `Intervalo`, removendo os respectivos `Calendars`:

\[java\] @Entity public class Produto { @Id @GeneratedValue private Long id; private String nome; private BigDecimal preco; private Intervalo periodoPromocional; //... }

@Entity public class Promocao { private Intervalo vigencia; //... } \[/java\]

A criação da classe `Intervalo` pode trazer benefícios muito maiores do que a eliminação da repetição de atributos. Adotando a boa prática de unir dados a comportamentos, podemos isolar todo comportamento relacionado a um intervalo de datas nessa nova classe:

\[java\] public class Intervalo { private Calendar inicio; private Calendar fim;

public int getDias() {...} public Intervalo interseccaoCom(Intervalo outro) {...} } \[/java\]

Para saber quantos dias temos para aproveitar a promoção faríamos:

\[java\] Intervalo janelaDeOportunidade = produto.getPeriodoPromocional().interseccaoCom(promocao.getVigencia());

int diasParaAproveitar = janelaDeOportunidade.getDias(); \[/java\]

Mas como mapeamos a classe `Intervalo`? Criar uma tabela e referenciar seus registros através de chaves estrangeiras nas tabelas `Produto` e `Promocao` só trará a necessidade de joins. A JPA nos oferece uma alternativa: criar uma classe `Embeddable`, um dos assuntos básicos abordados no [FJ-25](http://www.caelum.com.br/curso/fj-25-persistencia-jpa2-hibernate/ "FJ-25 Persistência com JPA2 e Hibernate"). Quando anotamos uma classe com `@Embeddable`, seus atributos serão colunas na tabela da entidade que tem o atributo anotado com `@Embedded`.

\[java\] @Embeddable public class Intervalo { //... }

@Entity public class Promocao { @Embedded private Intervalo vigencia; // a tabela Promocao terá uma coluna inicio e outra fim // ... } \[/java\]

A classe `Intervalo` não precisa de um identificador único: ela é definida pelo seu valor e pode ser encarada como um [Value Object](http://martinfowler.com/bliki/ValueObject.html "Value Object"). É importante conhecer os diversos recursos do Hibernate que nos dão a possibilidade de não precisar adequar nossas entidades às nossas tabelas, nem de adequar nossas tabelas às nossas entidades. Por exemplo, tabelas secundárias oferecem o serviço inverso: uma entidade mapeada em duas tabelas. O próprio `@ManyToMany` evita a criação de uma classe a mais apenas para manter referências para as duas classes relacionadas. Há ainda o suporte a chaves compostas, herança, nomenclatura das tabelas, namespaces e colunas, e até mesmo tipos definidos pelo próprio usuário para cada banco de dados diferente.
