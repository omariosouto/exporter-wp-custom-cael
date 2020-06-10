---
title: "Customizando a produção de dependências no CDI"
date: "2011-07-13"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Os DAOs aparecem com frequência dos projetos Java. Para implementá-los, quase sempre é usado algum framework _ORM_, como o Hibernate por exemplo. Nesses casos, é muito comum que vários de nossos DAOs acabem com código repetido para operações simples, como um CRUD. Acabamos criando o famoso [DAO genérico](https://blog.caelum.com.br/possibilidades-de-design-no-uso-do-seu-generic-dao/), concentrando algumas operações simples, para reutilizá-lo em outras partes do sistema.

Aqui na Caelum, [no curso FJ-26](http://www.caelum.com.br/curso/fj-26-java-web-jsf2-cdi/), fazemos um projeto onde usamos JSF2 com acesso a dados via JPA2, usando o Hibernate como implementação. Trabalhamos com injeção de dependencias para reduzir o acoplamento e gerenciar melhor o ciclo de vida de nossos `EntityManagers` e transações. Para isso usamos o **Weld**, que é a implementação de referência do [CDI](http://www.infoq.com/br/presentations/cdi-weld-seam-caelumdaybsb), uma das novas especificações do Java EE 6.

![JBoss Weld](https://blog.caelum.com.br/wp-content/uploads/2011/07/weld_logo_r4v1.png "JBoss Weld")

Imagine que é necessário implementar um cadastro de produtos para o sistema. Para isso, pode ser utilizado um Managed Bean do **JSF** e um respectivo DAO de produto para contemplar a funcionalidade. Teríamos um código parecido com o abaixo:

\[sourcecode language="java"\] @ManagedBean public class ProdutoBean { private Produto produto;

// get e set do produto omitido

public void grava() { ProdutoDAO produtos = new ProdutoDAO(); produtos.adiciona(produto); } } \[/sourcecode\]

O problema aqui é que nosso Managed Bean sabe exatamente como criar um novo `ProdutoDAO`, ficando bastante acoplado a ele e difícil testá-lo isoladamente. Para resolver esse primeiro problema, pode-se pedir para que o Weld **injete** uma instância dessa classe. Basta que, ao invés de dar o `new` manualmente, declaremos um atributo do tipo `ProdutoDAO` anotado com um simples `@Inject`:

\[sourcecode language="java"\] @Inject private ProdutoDAO produtos; \[/sourcecode\]

Esse _managed bean_ está servindo apenas para realizar as operações de CRUD. Pode ser interessante, ao invés de ser criado um novo DAO especializado para cada entidade, criar um genérico. Além disso, talvez você precise receber outras dependências através do construtor, impossibilitando a existência de um sem parâmetros.

A implementação de um DAO genérico fica parecida com a que temos logo abaixo:

\[sourcecode language="java"\] public class DAO<T> implements Serializable { private final Class<T> classe; private EntityManager em;

public DAO(Class<T> classe) { this.classe = classe; }

public void adiciona(T obj) { em.persist(obj); }

public void remove(T obj) { em.remove(obj); }

public void atualiza(T obj) { em.merge(obj); }

public List listaTodos() { CriteriaQuery query = em.getCriteriaBuilder().createQuery(classe); query.from(classe); return em.createQuery(query).getResultList(); }

public T buscaPorId(Long id) { return em.find(classe,id); } } \[/sourcecode\]

Precisamos receber a classe como parâmetro porque [o tipo do `T` não é mantido no bytecode](https://blog.caelum.com.br/nao-posso-descobrir-nem-instanciar-tipos-genericos-porque/). É a conhecida erasure. Agora que temos o DAO genérico, podemos pedir para o Weld injeáa-lo, ao invés do específico que havia sido criado para o produto:

\[sourcecode language="java"\] @Inject private DAO<Produto> produtos; \[/sourcecode\]

Acontece que quando o container tenta injetar esse objeto, é percebido que ele tem um construtor com argumentos, e entre eles existe um parâmetro do tipo `Class` o qual o Weld não sabe instanciar. Uma exception será lançada logo no momento que o container estiver subindo:

\[code\] Unsatisfied dependencies for type \[DAO<Produto>\] with qualifiers \[@Default\] at injection point \[\[field\] @Inject private br.com.caelum.notasfiscais.mb.ProdutoBean.dao\] \[/code\]

Quando existe uma situação desse tipo, a primeira coisa que deve ser feita, é criar uma classe com um método capaz de criar esse tipo de objeto, deixando-o anotado com `@Produces`. Dessa forma o Weld sabe que sempre que for necessário criar um objeto do tipo `DAO`, ele deverá criá-lo através deste factory method.

\[sourcecode language="java"\] public class DaoFactory {

@Produces public DAO create() { return new DAO(qualClasseAqui?); } } \[/sourcecode\]

Para finalizar, precisamos saber qual classe passar como argumento do dao genérico, caso contrário não é possível instanciá-lo. Pensando neste tipo de problema, podemos receber como parâmetro no método, um objeto do tipo `InjectionPoint`, que contém informações relevantes sobre o ponto do código que está pedindo a injeção. Com ele conseguimos obter o tipo parametrizado definido no atributo do Managed Bean, exatamente o que precisamos para resolver nosso problema. E com isso obtemos a classe correta para a construção do dao genérico. Para fazer esse truque, precisamos apenas de um pouco de reflection:

\[sourcecode language="java"\] public class DaoFactory { @Produces public DAO create(InjectionPoint injectionPoint) { ParameterizedType type = (ParameterizedType) injectionPoint.getType(); Class classe = (Class) type.getActualTypeArguments()\[0\]; return new DAO(classe); } } \[/sourcecode\]

Um detalhe interessante é o controle do ciclo de vida dos objetos criados pelo CDI. Caso seja necessário produzir apenas uma instância por request por exemplo, podemos anotar o método com `@RequestScoped`. Da mesma maneira que temos o `@RequestScoped`, existem também as anotações `@SessionScoped` e `@ApplicationScoped`, já pensadas para os escopos padrões da **WEB**. Também é suportado o `@ConversationScoped`, que pode ser usado quando algum objeto deve ser mantido durante a navegação de um determinado conjunto de páginas.

Para maximizar o uso do CDI, ainda podemos trabalhar com o **Seam 3**, que provê diversas extensões visando fechar os buracos que não foram contemplados pela especificação. A extensão mais popular, pensando numa aplicação JSF, é o suporte a anotação **ViewScoped** , provido pelo JSF2 e que não pertence ao CDI.

Configurar como o CDI vai produzir suas dependência é fácil, e um recurso poderoso para desacoplar o ciclo de vida de recursos caros. O [curso FJ-26 da Caelum](http://www.caelum.com.br/curso/fj-26-java-web-jsf2-cdi/) aborda diversos cenários de uso de CDI integrado a JSF2 e Seam 3.
