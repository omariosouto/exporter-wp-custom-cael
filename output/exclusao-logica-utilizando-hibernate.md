---
title: "Exclusão lógica utilizando Hibernate"
date: "2012-08-21"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em praticamente todos os projetos de software em que trabalhamos temos as funcionalidades conhecidas como CRUD. Porém nem sempre o delete do CRUD significa que devemos remover a informação do banco. Algumas vezes temos que apenas desativar o registro, porém mantê-lo por motivos de histórico ou auditoria.

[![](https://blog.caelum.com.br/wp-content/uploads/2022/08/20120816_141559-300x144.jpg)](https://blog.caelum.com.br/wp-content/uploads/2022/08/20120816_141559.jpg)

Uma solução comum para este problema é utilizar a exclusão lógica, criando uma coluna que indica se um determinado registro está ativo ou não, e alterando a funcionalidade de exclusão para não disparar um `delete`. Em vez disso, um `update` alterando o valor desta coluna.

Utilizando o Hibernate em nosso projeto, podemos ter uma classe com um atributo que indica se o registro está ativo ou não, da seguinte maneira:

\[java\] @Entity public class Cliente { @Id @GeneratedValue private Long id;

private String nome;

private String cpf;

private Boolean ativo;

// getters e setters caso necessarios

} \[/java\]

E então modificamos a nossa lógica de exclusão para apenas atualizar o registro:

\[java\] public class ClienteDAO { private final Session session;

public ClienteDAO(Session session) { this.session = session; }

public void excluir(Cliente cliente) { cliente.setAtivo(false); session.update(cliente); } } \[/java\]

Esta é uma solução simples e comum para implementar uma funcionalidade de exclusão lógica, mas alguns recursos do hibernate permitem uma abordagem muito interessante. Podemos alterar como o Hibernate trabalhar com a exclusão de registros, através da anotação `@SQLDelete` :

\[java\] @Entity @SQLDelete(sql = "update Cliente set ativo = 0 where id = ?") public class Cliente {

@Id @GeneratedValue private Long id;

private String nome;

private String cpf;

private Boolean ativo; } \[/java\]

A anotação `@SQLDelete` serve para sobrescrevermos a instrução SQL que será enviada para o banco de dados, quando o método `delete` for invocado em uma `Session` do Hibernate. Com o uso desta anotação, não precisamos alterar a lógica de exclusão existente.

\[java\] public void excluir(Cliente cliente) { session.delete(cliente); } \[/java\]

Mas agora temos um novo problema: nossa lógica de pesquisa está retornando os registros que estão inativos caso não sejamos explícitos em relação a essa coluna. Bastaria então alterar a query de consulta para filtrar os registros, trazendo apenas os que estão ativos:

\[java\] public List ativos() { return session.createQuery("from Cliente where ativo = true").list(); } \[/java\]

Mas ainda podem haver dezenas de outras queries de pesquisa, onde também não queremos trazer os registros inativos. Seria muito trabalhoso ter que alterá-las uma a uma e toda nova busca não esquecer de adicionar este parâmetro. Podemos também alterar a forma de buscar registros com a anotação `@Where` do Hibernate:

\[java\] @Entity @SQLDelete(sql = "update Cliente set ativo = 0 where id = ?") @Where(clause = "ativo = 1") public class Cliente { //resto do código omitido } \[/java\]

Na anotação `@Where` temos o atributo `clause`, onde informamos um filtro que será aplicado em todas as consultas, na entidade `Cliente`, realizadas via HQL ou Criteria. Desta maneira nossa lógica de pesquisa permanece sem alterações:

\[java\] public List ativos() { return session.createQuery("from Cliente").list(); } \[/java\]

Um último problema: Como visualizar os registros que estão inativos? Bem, como estamos utilizando a anotação `@Where`, o Hibernate vai adicionar o filtro definido nesta anotação em todas as consultas efetuadas na entidade `Cliente`. Portanto mesmo com uma query `session.createQuery("from Cliente where ativo = false").list();` esta consulta retornará zero registros, pois o código SQL gerado por esta query será algo como:

\[java\] select cliente0\_.id as id0\_, cliente0\_.ativo as ativo0\_, cliente0\_.cpf as cpf0\_, cliente0\_.nome as nome0\_ from Cliente cliente0\_ where ( cliente0\_.ativo = 1 ) and cliente0\_.ativo=0 \[/java\]

Infelizmente não há como desativar o `@Where` em uma consulta específica. Para recuperarmos os registros inativos podemos usar SQL nativo:

\[java\] public List inativos() { return session.createSQLQuery("select \* from Cliente where ativo = 0").addEntity(Cliente.class).list(); } \[/java\]

Como a query para buscar os registros inativos é bem simples, não há tantos problemas em utilizar SQL nativo, mas certamente não é elegante.

Existem outras maneiras de obter resultados parecidos, e uma delas é aplicando filtros em consultas, utilizando as anotações `@FilterDef` e `@Filter`:

\[java\] @Entity @FilterDef(name = "clientesAtivos") @Filter(name = "clientesAtivos", condition = "ativo = 1") public class Cliente { //resto do código omitido } \[/java\]

Precisamos dessas duas anotações, pois podemos criar um filtro que recebe parâmetros em tempo de execução, e neste caso os parâmetros devem ser definidos no atributo `parameters` da anotação `@FilterDef`, e referenciados no atributo `condition` da anotação `@Filter`. Um exemplo deste caso seria:

\[java\] @Entity @Filter(name = "clientesAtivosOuInativos", condition = "ativo = :status") @FilterDef(name = "clientesAtivosOuInativos", parameters = {@ParamDef(name = "status", type = "boolean")}) public class Cliente { private Boolean ativo;

//resto do código omitido } \[/java\]

Para utilizar este filtro em alguma query devemos ativá-lo primeiro, pois por padrão o Hibernate desconsidera todos os filtros em qualquer query. Essa é uma vantagem em relação ao `@Where`, já que você pode decidir se irá levá-los em consideração ou não. Fazemos isto com `session.enableFilter("clientesAtivos");`.

No caso de um filtro que recebe parâmetros definidos, passamos estes parâmetros da seguinte maneira: `session.enableFilter("clientesAtivosOuInativos").setParameter("status", true);`.

Um outro cenário onde a utilização da anotação `@Filter` se encaixa bem é quando temos uma aplicação que atende a vários clientes, e um cliente não pode ter acesso aos dados dos outros clientes. Neste tipo de aplicação, também conhecida como _Multitenancy_, podemos criar um filtro que recebe como parâmetro o `id` do cliente logado, e utilizar este filtro em todas as queries da aplicação.

Mais detalhes sobre Multitenancy pode ser visto neste post: [Um produto para muitos clientes: implementando multitenancy](https://blog.caelum.com.br/um-produto-para-muitos-clientes-implementando-multitenancy/ "Um produto para muitos clientes: implementando multitenancy"). Lembrando que essas anotações usadas aqui são específicas do Hibernate, e não fazem parte da especificação JPA. Quem sabe em alguma próxima versão?

O projeto com os códigos mostrados neste post pode ser visto em: [http://github.com/rcaneppele/hibernate-delete-logico](http://github.com/rcaneppele/hibernate-delete-logico "http://github.com/rcaneppele/hibernate-delete-logico")

Para casos mais avançados, com possibilidade de auditoria detalhada, o [Hibernate Envers](http://www.jboss.org/envers/) (agora JBoss Envers) possibilita as mais diversas configurações.

Para quem quer aprender JPA com o Hibernate, a [Editora Casa do Código](http://www.casadocodigo.com.br "Casa do Código") lançou o [livro](http://www.casadocodigo.com.br/products/livro-jsf-jpa "Livro JSF e JPA") que mostra como usar as tecnologias em conjunto com o framework JSF.

Você já teve que implementar um delete lógico na sua empresa? O que você usou?
