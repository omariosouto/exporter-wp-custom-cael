---
title: "Ei! Como é o seu DAO? Ele é tão abstraído quanto o meu?"
date: "2006-08-27"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Essa é uma pergunta comum entre os desenvolvedores. Alguns acham que há uma fórmula única, que DAO é um pattern fechado e que possui seu diagrama de classes bem definido. Eu discordo.

Na minha humilde opinião, DAO é uma maneira de você encapsular o seu acesso a dados. Não importa se é através de uma factory de objetos que recebem uma Session do hibernate por injeção de dependências, ou se é um montão de métodos estáticos cheio de SQL dentro deles (argh!). Obviamente a última opção envolve diversos outros antipatterns (como singleton e métodos estáticos, [que já criticamos anteriormente](https://blog.caelum.com.br/singletons-e-static-perigo-a-vista/)), porém ela consegue isolar seu acesso a dados, cumprindo o papel do DAO e de alguma maneira organizando sua camada.

Lendo a [definição de Data Access Object](http://java.sun.com/blueprints/corej2eepatterns/Patterns/DataAccessObject.html) no site da Sun, podemos chegar a um molde comum, mas nada impede que possamos incrementa-lo e modifica-lo de acordo com nossas necessidades. Por exemplo, com o Java 5, podemos tirar proveito dos generics. Na Caelum costumamos usar uma interface para os nossos DAOs parecida com a seguinte: \[java\] interface Dao<T> { T search(Serializable id); void persist(T t); void remove(T t); // ... } \[/java\]

A nossa implementação de DAO genérico para hibernate fica parecida com: \[java\] class GenericHibernateDao<T> implements Dao<T> { private Session session; private Class persistentClass;

public GenericHibernateDao(Session session, Class persistentClass) { this.session = session; this.persistentClass = persistentClass; }

public T search(Serializable id) { return session.load(persistentClass, id); }

// outros metodos da interface Dao } \[/java\]

Repare que ele recebe `session` como argumento, que poderia ser injetada por um container. Outra opção seria buscar a session de um singleton (o clássico `HibernateUtil`), mas a primeira maneira é bem mais elegante e testável. Repare que ainda temos os problemas de controle de transações: onde abrimos e comitamos? dentro do Dao? de dentro do injetor de dependências? de dentro da sua lógica de negócios? Nós costumamos fazer esse controle em um interceptador ou mesmo em um `Filter` de servlets, dependendo do framework MVC usado. Nosso filtro é [semelhante com o recomendado](http://community.jboss.org/wiki/OpenSessioninView) pela documentação do hibernate.

E então podemos utilizar nosso DAO: \[java\] Dao<Cliente> clienteDao = new GenericHibernateDao<Cliente>(session); clienteDao.persist(cliente); Cliente c5 = clienteDao.search(5); \[/java\]

Para as entidades que você precisa de mais que um simples cria/le/atualiza/deleta, criamos uma nova interface: \[java\] interface ClienteHibernateDao extends Dao<Cliente> { List<Cliente> buscaTodosClientesQueDevemDinheiro(); } \[/java\]

E teríamos uma classe `ClienteHibernateDao` que estende `GenericHibernateDao<Cliente>` e implementa `ClienteDao`.

Meus colegas da [easytech](http://easytech.inf.br/) preferem abstrair também o tipo da chave primária: \[java\] interface Dao<T, I> { T search(I id); // ... } \[/java\]

Dessa maneira você realmente ganha mais tipagem, já que no DAO anterior poderíamos passar qualquer objeto serializável como chave. Podemos refinar muito mais nossas classes de acesso a dados, mas até que ponto chegar?

Ficar abstraindo demais a sua camada de persistência pode levar a proliferação de centenas de pequenas classes que simplesmente não fazem nada, só delegam e delegam. Claro que existem casos em que abstrair para apenas delegar é interessante, diminuindo o acoplamento entre suas classes, porém isso pode chegar a um ponto que vai atrapalhar o desenvolvimento.

[Nesta entrada do blog dos desenvolvedores do hibernate](http://in.relation.to/Bloggers/UsingAbstractionFrameworksToMaintainControlWrongBet), o uso de camadas para abstrair as ferramentas de mapeamento objeto relacional são duramente criticadas.

E você, qual é a receita de bolo para o seu DAO? Até onde você o refina?
