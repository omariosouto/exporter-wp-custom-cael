---
title: "Simplifique suas consultas com o Query By Example do Hibernate"
date: "2015-05-12"
author: "marcio.shibao"
authorEmail: "marcio.shibao@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Durante o desenvolvimento de uma aplicação, é comum precisarmos de um filtro por vários parâmetros ao mesmo tempo.

[![tela_pesquisa_muitos_campos](https://blog.caelum.com.br/wp-content/uploads/2050/04/tela_pesquisa_muitos_campos-1024x573.png)](https://blog.caelum.com.br/wp-content/uploads/2050/04/tela_pesquisa_muitos_campos.png)

Algumas vezes, não sabemos a priori quais parâmetros devem ser considerados, e devemos ser capazes de executar a busca independente das informações que foram passadas.

Por exemplo, imagine que estamos desenvolvendo uma aplicação em que as pessoas podem fazer pedidos para restaurantes. Para isto, temos em nosso banco de dados duas tabelas, uma representando o restaurante e outra os tipos de comida.

Se estivermos trabalhando em uma aplicação Java e usando frameworks ORM como o Hibernate, teríamos os seguintes modelos:

\[code language="java"\] @Entity public class Restaurante {

@Id @GeneratedValue private Long id;

private String nome;

private String endereço;

@ManyToOne private TipoDeComida tipoDeComida;

//Getters and Setters. }

@Entity public class TipoDeComida {

@Id @GeneratedValue private Long id;

private String nome;

//Getters and Setters } \[/code\]

Para que o usuário possa escolher o Restaurante, ele vai poder pesquisar pelo nome, endereço e o tipo de comida que lhe interessar.

Considerando que todos os parâmetros estejam sempre preenchidos, poderíamos simplesmente escrever uma HQL para realizar a pesquisa, dado um objeto do tipo Restaurante:

\[code language="java"\] public List<Restaurante> busca(Restaurante restaurante) { String hql = "from Restaurante r where r.nome like :nome" + " and r.endereco like :endereco" + " and r.tipoDeComida.nome like :tipoDeComida"; return session.createQuery(hql) .setParameter("nome", restaurante.getNome()) .setParameter("endereco", restaurante.getEndereco()) .setParameter("tipoDeComida", restaurante.getTipoDeComida().getNome()) .list() } \[/code\]

Mas e se o usuário não soubesse que tipo de comida escolher, ou não soubesse o nome do restaurante? Para isso, precisamos fazer uma consulta considerando que os campos do filtro são opcionais.

Precisamos então de diversas condições no nosso código que verificam se as informações foram preenchidas:

\[code language="java"\] String hql = "from Restaurante r where 1=1" if(! restaurante.getEndereco().equals("")) { hql += " and r.endereco like :endereco" }

if(! restaurante.getNome().equals("")) { hql += " and r.nome like :nome" }

if(! restaurante.getTipoDeComida().getNome().equals("")) { hql += " and r.tipoDeComida.nome like :tipoDeComida" } // ifs novamente para atribuir os parâmetros na query \[/code\]

Porém, para adicionar as condições na nossa query, estamos concatenando cada cláusula na string da HQL. Solução que evidentemente não é a mais adequada, pois além de deixar o código com diversos ifs, ainda é preciso tomar cuidado com espaços nas concatenações da HQL.

Para evitar toda essas concatenações, podemos utilizar a Criteria, do Hibernate. Com ela podemos simplesmente adicionar uma nova restrição caso a condição seja satisfeita.

\[code language="java"\] Criteria criteria = session.createCriteria(Restaurante.class);

if(! restaurante.getEndereco().equals("")) { criteria.add(Restrictions.eq("r.endereco", restaurante.getEndereco())); }

if(! restaurante.getNome().equals("")){ criteria.add(Restrictions.eq("r.nome", restaurante.getNome())); } \[/code\]

Essa é uma solução que resolve nosso problema, mas quantas linhas foram necessárias para fazer esta consulta? Mesmo utilizando o Criteria ainda são 3 linhas de código para cada cláusula, dado que para cada parâmetro do filtro teríamos que fazer um if para adicionar a query. Claramente, já é melhor do que a concatenação de HQLs, mas será que ainda não dá para ficar melhor? E se pudessemos resolver nosso problema com apenas uma linha de código?

Não seria muito melhor se apenas passássemos para o Hibernate uma instância do Restaurante, com as informações que vierem populadas e ele descobrisse quais informações devem ser consideradas no filtro? Justamente para isso o Hibernate possui uma funcionalidade chamada Query By Example. Desta forma, ao invés de criarmos várias condições e adicionar uma Restriction em cada uma, podemos simplesmente adicionar um Example da seguinte forma:

\[code language="java"\] // Objeto restaurante populado, mas poderia vir direto do request pelo seu // framework MVC Restaurante restaurante = new Restaurante(); restaurante.setNome("Pico"); restaurante.setEndereco("Vergueiro");

TipoDeComida tipo = new TipoDeComida(); tipo.setNome("Contemporânea");

restaurante.setTipoDeComida(tipo);

// Query sendo feita Example example = Example.create(restaurante); List<Restaurante> restaurantes = s.createCriteria(Restaurante.class) .add(example).list(); \[/code\]

Portanto, com apenas duas linhas (poderia ser uma linha se deixássemos a criação do Example inlined) conseguimos fazer a busca com base apenas nos campos preenchidos pelo nosso usuário. Ao executarmos essa Criteria, teremos o seguinte SQL gerado:

\[code language="sql"\] select this\_.id as id1\_2\_1\_, this\_.endereco as endereco2\_2\_1\_, this\_.nome as nome3\_2\_1\_, this\_.tipoDeComida\_id as tipoDeCo4\_2\_1\_, tipodecomi2\_.id as id1\_3\_0\_, tipodecomi2\_.nome as nome2\_3\_0\_ from Restaurante this\_ left outer join TipoDeComida tipodecomi2\_ on this\_.tipoDeComida\_id=tipodecomi2\_.id where ( this\_.endereco=? and this\_.nome=? ) \[/code\]

No entanto, executando o criteria acima é possível notar que todas as comparações feitas entre atributos foi com igual. Mas e se quisermos utilizar like? O Example permite que você configure a forma como ele fará as comparações. Para habilitar o like basta usar o método enableLike() e passar como parâmetro o MatchMode. Desta forma, se quisermos comparar string usando like e % no começo e fim de cada string, teríamos o seguinte código:

\[code language="java"\] Example example = Example.create(restaurante).enableLike(MatchMode.ANYWHERE); \[/code\]

Executando novamente a query, veremos os likes sendo utilizados:

\[code language="sql"\] this\_.endereco like ? and this\_.nome like ? \[/code\]

Assim, conseguimos realizar uma busca com campos opcionais para a classe Restaurante. Mas note que mesmo tendo adicionado as informações do tipo de comida dentro do objeto restaurante, ele está sendo ignorado no nosso filtro. O Example, por conta da Criteria, funciona sempre no nível do objeto pai (no caso o restaurante) e não desce para os filhos (no caso, o tipo de comida). Mas queremos deixar o tipo de comida como um campo do filtro.  Como adicionar esta restrição na nossa consulta?

Para resolver este problema basta usar a mesma ideia de quando é necessário adicionar restrições para um objeto de outro nível daquele considerado, ou seja, criar uma sub Criteria. Assim, depois de criar uma criteria para o tipo de comida basta adicionar um novo Example para esta instância. Portanto, temos o seguinte código:

\[code language="java"\] Example exampleRestaurante = Example.create(restaurante) .enableLike(MatchMode.ANYWHERE); Example exampleTipoDeComida = Example.create(restaurante.getTipoDeComida());

List<Restaurante> restaurantes =  session.createCriteria(Restaurante.class).add(exampleRestaurante) .createCriteria(“tipoDeComida”).add(exampleTipoDeComida) .list(); \[/code\]

E agora nossa consulta vai ser executada com o where completo:

\[code language="sql"\] where ( this\_.endereco like ? and this\_.nome like ? ) and ( tipodecomi1\_.nome=? ) \[/code\]

Quando uma informação não for preenchida, ela será ignorada pelo where, sem você precisar ficar programando ifs intermináveis. Essa é a grande vantagem do Query By Example. E você já usou esse recurso em seus projetos? Quais outras funcionalidades do Hibernate você gosta?

Esse e diversos outros recursos do Hibernate e da JPA você aprende no nosso [Curso Persistência com JPA, Hibernate e EJB lite](https://www.caelum.com.br/curso-persistencia-java-jpa-hibernate/)
