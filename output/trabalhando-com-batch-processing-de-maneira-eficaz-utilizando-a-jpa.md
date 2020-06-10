---
title: "Trabalhando com Batch Processing de maneira eficaz utilizando a JPA"
date: "2013-10-01"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Frequentemente temos que desenvolver funcionalidades que lidarão com grandes volumes de dados. Imagine por exemplo uma situação onde temos que ler um arquivo em algum formato específico(csv, xml, json, dentre outros), que contêm milhões de registros de vendas, e em seguida devemos inserir todos esses registros em um banco de dados.

Trabalhando com a Java Persistence API, poderíamos ter a seguinte classe representando a entidade Venda:

\[java\] @Entity public class Venda {

@Id @GeneratedValue(strategy = GenerationType.IDENTITY) private Long id; private BigDecimal valor; private String cliente; private String vendedor; private Calendar data;

//getters e setters... } \[/java\]

E o seguinte método para persistir os registros contidos no arquivo:

\[java\] public void cadastraVendasContidasNoArquivo(InputStream arquivo) { EntityManager em = //recupera o EntityManager em.getTransaction().begin();

Venda efetuada = recuperaProximaVenda(arquivo); while (efetuada != null) { em.persist(efetuada); efetuada = recuperaProximaVenda(arquivo); }

em.getTransaction().commit(); em.close(); } \[/java\]

O código anterior, embora esteja correto, é pouco eficaz e pode causar um `OutOfMemoryError`.

O problema é que estamos percorrendo a lista de vendas, e, para cada objeto desta lista, invocando o método `persist`, que faz com que o objeto passe a ser gerenciado pelo contexto de persistência, além de colocá-lo no cache de 1º nível.

Colocar milhões de objetos no cache de 1º nível da JPA quase sempre é inviável. Uma solução mais eficaz seria persistir os objetos aos poucos, fazer a sincronização com o banco de dados, e em seguida já remover esses objetos do cache de 1º nível: \[java\] public void cadastraVendasContidasNoArquivo(InputStream arquivo) { EntityManager em = //recupera o EntityManager em.getTransaction().begin();

int contador = 1; Venda efetuada = recuperaProximaVenda(arquivo); while (efetuada != null) { em.persist(efetuada);

//a cada 50 objetos, faz a sincronizacao e limpa o cache if (contador % 50 == 0) { em.flush(); em.clear(); } efetuada = recuperaProximaVenda(arquivo); contador++; }

em.getTransaction().commit(); em.close(); } \[/java\]

O código anterior utiliza um contador para controlar quando sincronizar e limpar o cache, que no nosso exemplo será a cada 50 objetos que forem persistidos. Com essa alteração evitamos colocar milhões de objetos no cache de 1º nível.

Outra situação comum, onde temos que lidar com um grande volume de dados, ocorre quando precisamos atualizar uma grande quantidade de registros de uma determinada tabela no banco de dados. Por exemplo, imagine que precisamos criar uma funcionalidade que será responsável por atualizar o status de todos os `Contratos` que estejam vencidos. Podemos implementar essa funcionalidade com o seguinte código:

\[java\] public void atualizaOStatusDosContratosVencidos() { EntityManager em = //recupera o EntityManager em.getTransaction().begin(); String query = "select c from Contrato c where c.dataVencimento < :hoje and c.status = :ativo"; List<Contrato> vencidos = em.createQuery(query, Contrato.class) .setParameter("hoje", Calendar.getInstance()) .setParameter("ativo", StatusDoContrato.ATIVO) .getResultList(); for(Contrato vencido : vencidos) { vencido.alteraStatusPara(StatusDoContrato.VENCIDO); } em.getTransaction().commit(); em.close(); } \[/java\]

Novamente não há nada de errado com o código anterior, ele apenas é ineficaz, pois carrega todos os objetos para a memória e para o cache de 1º nível.

Estamos carregando todos os objetos para a memória apenas para alterar o atributo status, e em seguida esses objetos não são mais utilizados. Nesse caso poderíamos enviar uma instrução `SQL` de `update` diretamente para o banco de dados, sem precisar carregar os objetos para a memória:

\[java\] public void atualizaOStatusDosContratosVencidos() { EntityManager em = //recupera o EntityManager em.getTransaction().begin();

String query = "update Contrato c set c.status = :vencido where c.dataVencimento < :hoje and c.status = :ativo"; em.createQuery(query) .setParameter("vencido", StatusDoContrato.VENCIDO) .setParameter("hoje", Calendar.getInstance()) .setParameter("ativo", StatusDoContrato.ATIVO) .executeUpdate();

em.getTransaction().commit(); em.close(); } \[/java\]

O código foi alterado para utilizar o método `executeUpdate()`, que executa uma instrução de update ou delete. Com essa pequena alteração evitamos carregar, desnecessariamente, milhões de objetos para a memória, e assim ganhamos em performance e eficácia.

Com essas dicas que vimos conseguimos evitar problemas como `OutOfMemoryError` e lentidão na execução de rotinas batch.

Você pode conhecer outros problemas comuns que enfrentamos quando utilizamos a JPA no nosso curso de [Persistência com JPA, Hibernate e EJB lite](http://www.caelum.com.br/curso-persistencia-java-jpa-hibernate/ "Curso JPA"), e aprender como resolvê-los de maneira eficaz.

E você, já passou por algum problema ao trabalhar com Batch Processing com a JPA?
