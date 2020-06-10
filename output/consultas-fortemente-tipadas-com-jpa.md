---
title: "Consultas fortemente tipadas com JPA"
date: "2015-10-20"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Considere que temos uma classe `Venda`, que é uma entidade JPA, conforme a seguir:

\[code language="java"\] @Entity public class Venda { @Id @GeneratedValue private Integer id;

private Double valor;

//getters e setters... } \[/code\]

Também considere que temos um classe `VendaDAO` com uma consulta para listar e outra para somar o valor total das vendas, utilizando JPQL:

\[code language="java" highlight="8,13"\] public class VendaDAO {

@PersistenceContext private EntityManager em;

public List<Venda> lista(){ Query query = em.createQuery("select v from Venda v"); return query.getResultList(); }

public Double total(){ Query query = em.createQuery("select sum(v.valor) from Venda v"); return (Double) query.getSingleResult(); } \[/code\]

Trata-se de um código bem comum para quem já utilizou JPA. Mas as linhas destacadas no código de `VendaDAO` tem alguns problemas: há um _warning_ na última linha do método `lista` e tivemos que fazer um _cast_ no método `total`.

### Porque warning e cast?

No método `lista` de `VendaDAO`, o compilador gera um warning com uma mensagem como: _"The expression of type List needs unchecked conversion to conform to List<Venda>"_. Acontece que o método `getResultList` de `Query` retorna uma `List`, que pode conter qualquer tipo de objeto. Porém, definimos o retorno como List<Venda> e, pelo mecanismo de [type-erasure](https://blog.caelum.com.br/nao-posso-descobrir-nem-instanciar-tipos-genericos-porque/), não há como garantir em tempo de compilação que a lista retornada conterá apenas objetos do tipo `Venda`. O compilador então nos avisa de um trecho de código que poderá gerar um possível erro em tempo de execução. Poderíamos colocar `@SupressWarning("unchecked")` no método ou na classe mas isso não resolve o problema, apenas omite o aviso do compilador.

Já no método `total` de `VendaDAO`, tivemos que fazer um cast porque o método `getSingleResult` de `Query` retorna um `Object` e devemos retornar um `Double`. Com o cast, assumimos o risco do objeto retornado não ser do tipo `Double`, podendo gerar um `ClassCastException` em tempo de execução.

Java é uma linguagem fortemente tipada, com declarações explícitas dos tipos de variáveis e atributos. Mas as consultas com JPQL nos deixam na mão...

### Usando TypedQuery

Desde o JPA 2.0, há uma maneira de evitar warnings ao utilizar `getResultList` e casts ao utilizar `getSingleResult`. Podemos passar a classe esperada ao invocarmos o `createQuery`, de maneira a obter um objeto do tipo `TypedQuery`.

\[code language="java"\] public class VendaDAO {

@PersistenceContext private EntityManager em;

public List<Venda> lista(){ TypedQuery<Venda> query = em.createQuery("select v from Venda v", Venda.class); return query.getResultList(); }

public Double total(){ TypedQuery<Double> query = em.createQuery("select sum(v.valor) from Venda v", Double.class); return query.getSingleResult(); } \[/code\]

O código acima não teria warnings nem necessitaria mais de casts. Mas será que é fortemente tipado? **Não**.

Não há checagem em tempo de compilação dos tipos retornados pelas consultas. Só descobriremos o retorno real em tempo de execução.

No fim das contas, o **JPQL é uma `String`**. Podemos errar o nome dos atributos, o nome das classes e até mesmo a sintaxe do JPQL. E **só descobriremos esses erros em tempo de execução**.

Algo fortemente tipado de verdade resistiria a refatorações como renomear classes e mudar o tipo dos atributos.

### Usando Criteria

Desde o JPA 2.0, há a API de Criteria. Comumente, essa API é utilizada para fazer filtros dinâmicos.

Porém, **a grande vantagem da Criteria do JPA 2.0 é nos impedir de criar consultas com erros de sintaxe**, através de checagem em tempo de compilação. Ao invés de definir as consultas em uma `String`, temos uma definição programática, com código Java.

Para utilizar a API de Criteria, o primeiro passo é obter um objeto do tipo `CriteriaBuilder` a partir do `EntityManager`:

\[code language="java"\] CriteriaBuilder builder = em.getCriteriaBuilder(); \[/code\]

Então, utilizamos o `CriteriaBuilder` para criar um objeto do `CriteriaQuery`, passando o retorno esperado da nossa consulta. Para o cálculo do total vendido, seria algo como:

\[code language="java"\] CriteriaQuery<Double> criteria = builder.createQuery(Double.class); \[/code\]

A partir do `CriteriaQuery`, devemos chamar o método `from` passando a classe da entidade que queremos consultar:

\[code language="java"\] Root<Venda> root = criteria.from(Venda.class); \[/code\]

A chamada do método `from` retorno um objeto do tipo `Root<Venda>`, que representa a raiz da nossa consulta, equivalente ao alias `v` em `from Venda v` de um JPQL. Podemos usá-lo como ponto de partida para acessar atributos da entidade:

\[code language="java"\] Path<Double> valor = root.get("valor"); \[/code\]

O código `root.get("valor")` seria equivalente a `v.valor` em um JPQL. Podemos passar o objeto `Path<Double>` retornado para o método `sum` do `CriteriaBuilder`, que define a função de soma:

\[code language="java"\] Expression<Double> soma = builder.sum(valor); \[/code\]

É retornado um objeto do tipo `Expression<Double>`, que devemos passar para o método `select` de nosso `CriteriaQuery`.

\[code language="java"\] criteria.select(soma); \[/code\]

Finalmente, podemos criar um `TypedQuery<Double>` a partir do `CriteriaQuery`, usando-o para obter o resultado através do método `getSingleResult`.

\[code language="java"\] TypedQuery<Double> query = em.createQuery(criteria); Double total = query.getSingleResult(); \[/code\]

Juntando o código todo, teríamos: \[code language="java"\] CriteriaBuilder builder = em.getCriteriaBuilder(); CriteriaQuery<Double> criteria = builder.createQuery(Double.class); Root<Venda> root = criteria.from(Venda.class); Path<Double> valor = root.get("valor"); Expression<Double> soma = builder.sum(valor); criteria.select(soma); TypedQuery<Double> query = em.createQuery(criteria); Double total = query.getSingleResult(); \[/code\]

O código ficou bem mais complicado. A vantagem é que teríamos uma consulta fortemente tipada. Mas será que é mesmo? Quase...

Como não definimos a consulta em uma `String`, não conseguiríamos cometer erros de sintaxe como trocar `from` por `form` ou `select` por `sletc`. Até aí tudo bem.

Renomear a classe `Venda` para `Negociacao`, por exemplo, não seria um problema, já que nossa IDE provavelmente trocaria sem grandes problemas o código `criteria.from(Venda.class)` por `criteria.from(Negociacao.class)`.

O principal problema acontece ao mudarmos o nome ou o tipo de atributos. Não teríamos um erro de compilação. Só descobriremos possíveis erros quando alguém realmente executar aquele trecho de código. Portanto, **o acesso aos atributos não é fortemente tipado**.

Repare no trecho de código a seguir: \[code language="java"\] Path<Double> valor = root.get("valor"); \[/code\]

Há ainda uma `String` com o nome do atributo `valor`. Ao renomearmos o atributo, teríamos que lembrar de mudar esse trecho de código específico. Além disso, o tipo de `Path<Double>` teve que ser colocado manualmente. Uma mudança do tipo do atributo para `BigDecimal` não geraria nenhum erro de compilação. E a IDE também não nos ajudaria.

Precisamos de alguma maneira de saber o nome e o tipo dos atributos de uma classe de maneira fortemente tipada...

### Static Metamodel

Associada à API de Criteria, a especificação JPA 2.0 definiu o _Static Metamodel_, uma **maneira fortemente tipada de recuperar informações de atributos** de classes anotadas com `@Entity`.

Para cada entidade, é criada uma classe associada que contém informações sobre o tipo dos atributos, tudo acessível maneira estática. Por exemplo, para a entidade `Venda` será criada automaticamente uma classe `Venda_`, no mesmo pacote, com um conteúdo parecido com:

\[code language="java"\] @Generated(value="Dali", date="2015-10-02T16:06:29.157-0300") @StaticMetamodel(Venda.class) public class Venda\_ { public static volatile SingularAttribute<Movimentacao, Integer> id; public static volatile SingularAttribute<Movimentacao, Double> valor; } \[/code\]

Essas classes associadas, chamadas de metamodelo estático, são geradas por meio de processamento de anotações. As implementações de JPA como Hibernate, EclipseLink e OpenJPA devem fornecer classes processadoras que gerem esse código.

IDEs como o Eclipse e NetBeans e ferramentas de automação como Maven, Ant e Gradle possuem maneiras de regerar automaticamente essas classes sempre que alguma entidade for modificada. No Eclipse, por exemplo, há o [Dali](https://eclipse.org/webtools/dali/), um subprojeto do plugin WTP que faz esse processamento das entidades. Para habilitá-lo, basta que o _Project Facet_ do JPA seja usado e que seja configurado o _Source Folder_ das classes em _Properties -> JPA -> Canonical Metamodel_.

### Usando o Static Metamodel com uma Criteria

O trecho do código anterior que não estava fortemente tipado era aquele em que usávamos o atributo `valor` da classe `Venda` através de uma `String`. Usando a classe `Venda_`, o trecho ficaria algo como:

\[code language="java"\] Path<Double> valor = root.get(Venda\_.valor); \[/code\]

Se eventualmente mudarmos o tipo do atributo `valor` para `BigDecimal`, esse trecho de código iria logo falhar com um erro de compilação como _"Type mismatch: cannot convert from Path<BigDecimal> to Path<Double>"_, indicando que o tipo do `Path` está incorreto.

Caso mudássemos o nome do atributo para `quantia`, imediatamente teríamos um erro de compilação como _"valor cannot be resolved or is not a field in type Venda\_"_, indicando que o metamodelo estático da entidade `Venda` já não tem mais o atributo `valor`.

### Considerações finais

A nova versão da classe `VendaDAO`, usando Criteria com metamodelo estático e com variáveis intermediárias omitidas, ficaria algo como:

\[code language="java"\] public class VendaDAO {

@PersistenceContext private EntityManager em;

public List<Venda> lista(){ CriteriaBuilder builder = em.getCriteriaBuilder(); CriteriaQuery<Venda> criteria = builder.createQuery(Venda.class); criteria.from(Venda.class); return em.createQuery(criteria).getResultList(); }

public Double total(){ CriteriaBuilder builder = em.getCriteriaBuilder(); CriteriaQuery<Double> criteria = builder.createQuery(Double.class); Root<Venda> root = criteria.from(Venda.class); criteria.select(builder.sum(root.get(Venda\_.valor))); return em.createQuery(criteria).getSingleResult(); } \[/code\]

Sem warnings nem casts, resistente a erros de sintaxe e a refatorações. Porém, um código mais complexo e um pouco maior. E as consultas são bem simples...

A grande questão é saber se, para seu projeto e sua equipe, as vantagens de ter consultas fortemente tipadas compensam a complexidade da API de Criteria.

Esse e outros recursos do JPA são apresentados no curso [Persistência com JPA, Hibernate e EJB lite](https://www.caelum.com.br/curso-persistencia-java-jpa-hibernate/).

E você? Já usou a API Criteria do JPA com o Static Metamodel? Como foi a experiência?
