---
title: "Metaprogramação em Java? O papel do APT."
date: "2009-12-09"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em 2004, com o lançamento do Java 5, muitas novidades entraram pra linguagem. As [anotações](http://java.sun.com/javase/6/docs/technotes/guides/language/annotations.html) são um recurso hoje fundamental, que utilizamos como metadados. O Hibernate, junto com a JPA/EJB 3.0, popularizaram muito o uso das anotações para afetar o comportamento em tempo de execução do framework em relação às suas classes anotadas. Em linguagens dinâmicas isso vai além. O Ruby utiliza muito metadata, até mesmo para gerar acessores e modificadores de atributos em tempo de execução:

\[ruby\] class ContaPagar attr\_accessor :descricao, :valor, :data end \[/ruby\]

Como poderíamos fazer um exemplo simples desse em Java? Infelizmente não é algo trivial como o uso de anotações feito pelo Hibernate/JPA, pois, mesmo usando manipulação de bytecode, não adianta gerar os getters ou setters em tempo de execução, já que não conseguiremos utilizá-los pela característica de tipagem estática da linguagem. Precisaremos usar uma abordagem em tempo de compilação.

O [JAX-WS utiliza bastante de anotações para a geração de código cliente dos webservices](https://blog.caelum.com.br/webservices-sem-servidor-de-aplicacao-no-java-6/) através de duas feramentas: o [wsimport](http://download.oracle.com/docs/cd/E17802_01/webservices/webservices/docs/2.0/jaxws/wsimport.html) para trabalhar com WSDL e o [Annotation Processor Tool](http://download.oracle.com/javase/6/docs/technotes/guides/apt/index.html) (APT) para trabalhar com código fonte Java. Através do APT, [incluso apenas como ferramenta no Java 5](http://download.oracle.com/javase/1.5.0/docs/guide/apt/GettingStarted.html), você pode criar [Processadores](http://download.oracle.com/javase/6/docs/api/javax/annotation/processing/Processor.html) de anotações que são acionados pelo APT e podem gerar novos códigos Java.

O Java 6 leva o APT para um outro nível: além de fazer parte da API (não apenas uma ferramenta `com.sun`) agora é possível que um `Processor` sejá executado pelo próprio compilador, sem a necessidade de executá-lo a parte. Para isso, basta que exista um _jar_ no seu _classpath_ que contenha o arquivo `javax.annotation.processing.Processor` dentro de `META-INF/services`, fazendo com que os processadores lá citados sejam executados a cada compilação, possivelmente gerando novo código Java.

Onde isso é útil?

Um dos aguardados recursos da JPA 2.0 no Java EE 6 é o sistema de `Criteria` parecido com o do Hibernate, que pode ser agora utilizado de uma maneira _typesafe_ sem o uso de Strings. Como iremos nos referenciar à um atributo de uma classe sem String? Para uma classe, sabemos que podemos escrever `NomeDaClasse.class`, e um `Class<NomeDaClasse>` carregado pelo mesmo classloader será retornado.

Apesar de ser [um dos 25 recursos mais pedidos pela comunidade](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=5043025), o Java não possui suporte para literais de construtores, métodos e atributos. Isso é, se quero pegar uma referência para um Method que tenho certeza absoluta que existe, preciso de qualquer forma utilizar a API de reflection e lidar com as checked exceptions e diversos passos necessários. Uma forma de contornar isso e fazer a `Criteria` _typesafe_ da JPA 2.0 ser viável foi [o uso do `StaticMetaModel`](http://blogs.sun.com/ldemichiel/entry/java_persistence_2_0_proposed).

Imagine que para a classe `ContaPagar` a seguir:

\[java\] @Entity public class ContaPagar {

@Id @GeneratedValue private int id;

private String descricao;

private Double valor;

@Temporal(TemporalType.DATE) private Calendar data;

@ManyToOne private Fornecedor fornecedor;

private boolean pago;

// metodos } \[/java\]

Precisaríamos criar uma classe paralela, a `ContaPagar_`, que contém atributos estáticos que de certa forma representam os atributos persistidos da nossa entidade JPA:

\[java\] @StaticMetamodel(ContaPagar.class) public abstract class ContaPagar\_ {

public static volatile SingularAttribute<ContaPagar, Integer> id; public static volatile SingularAttribute<ContaPagar, String> descricao; public static volatile SingularAttribute<ContaPagar, Double> valor; public static volatile SingularAttribute<ContaPagar, Calendar> data; public static volatile SingularAttribute<ContaPagar, Fornecedor> fornecedor; public static volatile SingularAttribute<ContaPagar, Boolean> pago; } \[/java\]

Daria um certo trabalho manter a estranha classe `ContaPagar_` atualizada de acordo com toda modificação na classe `ContaPagar`. A solução é utilizar um gerador de código, e esse gerador é um processador do APT, que será invocado toda vez que o `javac` rodar (no Eclipse é necessário [ativar o APT](http://help.eclipse.org/galileo/index.jsp?topic=/org.eclipse.jdt.doc.isv/guide/jdt_apt_getting_started.htm)). O Hibernate 3.5-beta2 já [disponibiliza](http://repository.jboss.com/maven2/org/hibernate/hibernate-jpamodelgen/) esse [gerador](http://relation.to/Bloggers/HibernateStaticMetamodelGeneratorAnnotationProcessor) (mas com limitado suporte ao Criteria do JPA2), assim como o EclipseLink. Dessa forma podemos selecionar o atributo `valor` de todas as `ContaPagar`, com garantia de tipos através de `ContaPagar_.valor`:

\[java\] CriteriaBuilder cb = manager.getCriteriaBuilder(); CriteriaQuery cq = cb.createQuery(Double.class); Root cpagar = cq.from(ContaPagar.class); CriteriaQuery select = cq.select(cpagar.get(ContaPagar\_.valor)); return this.manager.createQuery(select).getResultList(); \[/java\]

Se isso é uma vantagem ou não, é outra discussão: alguns dizem que esse ganho de tipagem forte na `Criteria` não compensa a quantidade de código extra, além de que seus testes unitários pegariam erros no caso de String erradas. Diferente do que parece a primeira vista, o código **não** é refatorável da maneira clássica: renomear o atributo `valor` para `valorTotal` vai gerar um novo atributo `ContaPagar_.valorTotal`, mas quem se referenciava ao atributo com nome antigo não será refatorado, pois refatoramos o nome do modelo, e não do metamodelo. As ferramentas devem evoluir para conseguir lidar com casos como esse.

Mas seria essa metaprogramação tão poderosa quanto a do Ruby, onde métodos são inclusos na classe de acordo com o uso de metadados (como o _attr\_acessor_)?

O [Sérgio Lopes](http://twitter.com/sergio_caelum) e o [Nico Steppat](http://twitter.com/steppat) da Caelum me apresentaram ao [lombok](http://projectlombok.org/), um processador do APT que, através de [recursos da API não pública da JVM da Sun](http://github.com/tml/lombok/blob/master/src/core/lombok/javac/apt/Processor.java), gera código na própria classe que está sendo compilada:

![meta programação com Java e APT](https://blog.caelum.com.br/wp-content/uploads/2009/12/metaprogramacao-java.png)

_À esquerda um código que não possui métodos, à direita uma surpresa: o outline do Eclipse acusando a exitência de métodos no bytecode dessa classe._

  
Com isso podemos gerar métodos em tempo de compilação que podem ser acessados pela sua IDE, fornecendo até mesmo _code completion_! Isso abre caminho para recursos que se assemelham aos _mixins_, e com tipagem estática, e ao mesmo tempo para prejuízos parecidos com os do [monkey patching](http://en.wikipedia.org/wiki/Monkey_patch). A força de uma linguagem mais dinâmica permitiu a criação do [grails](http://www.grails.org/) com recursos similares aos de ActiveRecord do rails, onde - no caso do rails - atributos do banco podem ser acessados diretamente sem requerer a declaração no modelo.

O APT abre caminho para implementações similares na plataforma Java usando a própria linguagem, alguém poderá em um futuro próximo adicionar os getters e setters necessários para seu modelo baseado na engenharia reversa de tabelas, relacionamentos e configurações, como o Rails faz, mas com algumas limitações. Há também a questão de que se isso é útil para uma linguagem como Java.
