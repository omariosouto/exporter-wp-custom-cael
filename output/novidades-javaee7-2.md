---
title: "Conheça as principais novidades no JavaEE 7"
date: "2013-03-12"
author: "mario.amaral"
authorEmail: "mario.amaral@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Previsto para abril de 2013, a nova versão do **JavaEE** vem com atualizações para as principais especificações, além da adição de várias outras focadas em facilitar o desenvolvimento de aplicações para nuvem no modelo _PAAS_.

[![Screen Shot 2013-03-11 at 7.03.58 PM](https://blog.caelum.com.br/wp-content/uploads/2013/03/Screen-Shot-2013-03-11-at-7.03.58-PM-300x205.png)](https://blog.caelum.com.br/wp-content/uploads/2013/03/Screen-Shot-2013-03-11-at-7.03.58-PM.png)

Vejamos as principais novidades:

**JPA 2.1** ([JSR-338](http://jcp.org/en/jsr/detail?id=338 "JSR-338")) Agora a JPA irá suportar chamadas a _procedures_ de maneira transparente, sem a necessidade de usar código específico de nenhuma implementação:

\[sourcecode language="java"\] //prepara a procedure StoredProcedureQuery query = EntityManager.createStoredProcedureQuery("processaBoletos"); //registra os parametros da procedure query.registerStoredProcedureParameter(1, String.class, ParameterMode.OUT); //saída query.registerStoredProcedureParameter(2, Integer.class, ParameterMode.IN); //entrada query.setParameter(2, 001); //seta o parametro query.execute(); String resposta= query.getOutputParameterValue(1); \[/sourcecode\]

Podemos também invocar qualquer função que esteja disponível no banco de dados, através do novo comando `FUNCTION` da `JPQL`:

\[sourcecode language="sql"\] SELECT c FROM Cliente c WHERE FUNCTION(‘EhClienteEspecial’, c.id) \[/sourcecode\]

Os `EntityListeners` da JPA agora também estão integrados com o **CDI**, permitindo receber por injeção qualquer dependência usando o `@Inject`:

\[sourcecode language="java"\] class PedidoListener{ @Inject private GeredorDeNumero gerador;

@PrePersist public void geraNumero (Object o){ Pedido p = (Pedido) o; P.setNumero(gerador.novoNumero()); } } \[/sourcecode\]

Também foi incluído o suporte a operações de update e delete em lote usando criteria, [além de outras funcionalidades](https://blogs.oracle.com/arungupta/entry/jpa_2_1_early_draft).

**CDI 1.1** ([JSR-346](http://jcp.org/en/jsr/detail?id=346 "JSR-346"))

Apenas pequenas mudanças e melhorias serão incluídas nessa versão. Entre algumas das novidades esperadas estão permitir a ordenação de _Interceptors_ e _Decorators_, uma API para criação de contextos personalizados, injeção de objetos estáticos e criação de eventos relacionados a API de _Servlets_, como inicio/fim de um `HTTPServletRequest` por exemplo.

**EJB 3.2** ([JSR-345](http://jcp.org/en/jsr/detail?id=345 "JSR-345")) Aqui o foco é continuar facilitando cada vez mais a configuração e o uso de **EJBs**. Agora teremos suporte a transações dentro de _callbacks_ do ciclo de vida dos _beans_:

\[sourcecode language="java"\] @Stateful public class CarrinhoBean{ @PersistenceContext private EntityManager em;

@TransactionAttribute @PrePassivate public void armazenaNoBanco(){ // algum código transacional } ... } \[/sourcecode\]

As configurações sobre quais interfaces serão expostas nos _beans_ também foram simplificadas:

\[sourcecode language="java"\] public interface Agendador{} public interface Executador{} //todas as interfaces são locais @Stateless public class AgendadorBean implements Executador, Agendador{ ... }

//todas as interfaces são remotas @Remote @Stateless public class AgendadorBean implements Executador, Agendador{ ... } \[/sourcecode\]

Caso deseje que apenas uma das interfaces seja exposta, é só adicionar a anotação desejada:

\[sourcecode language="java"\] @Remote public interface Agendador{}

//apenas a interface Agendador é exposta como remota @Stateless public class AgendadorBean implements Executador, Agendador{ ... } \[/sourcecode\]

**JSF 2.2** ([JSR-344](http://jcp.org/en/jsr/detail?id=344 "JSR-344"))

Algumas _features_ que antes só estavam disponíveis através do **Seam3** agora fazem parte da especificação. Isso acontece com a tag `viewAction` e a possibilidade de receber injeção de dependências via `CDI` em qualquer componente do JSF, como `Validator`, `Converters` e até mesmo em componentes customizados.

Uma nova _annotation_ `@ViewScoped` foi criada em substituição a atual para permitir integração do CDI com _ManagedBeans_ neste escopo. E falando em escopos, o `@FlowScoped` foi adicionado para ajudar na criação de fluxos pré-definidos, como _wizards_, sequências de passos em algum fluxo de negócio, etc.

Além destas, ainda há uma integração melhor com **HTML5**, suporte a configuração programática, um novo componente para _upload_ de arquivos usando _ajax_, e muitas outras, como é possível ver na [lista de novidades no JSF 2.2](http://jdevelopment.nl/jsf-22 "lista de novidades no JSF 2.2")

**JAX-RS 2.0** ([JSR-339](http://jcp.org/en/jsr/detail?id=339 "JSR-339"))

Agora com suporte a uma API de cliente, é possível implementar chamadas a um serviço _rest_ de maneira portável entre os servidores sem a necessidade de bibliotecas externas:

\[sourcecode language="java"\] Client client = ClientFactory.newClient();

//Dispara uma requisição GET para a url http://.../produtos?nome=Caneta Produto p = client.target("http://.../produtos") .queryParam("nome", "Caneta") .request().get(Produto.class); \[/sourcecode\]

[Outras novidades](http://java.dzone.com/articles/whats-new-jax-rs-20) são a integração com **Bean Validation**, suporte a requisições assíncronas tanto na API cliente quanto na servidor, filtros e interceptors.

**JMS 2.0** ([JSR-343](http://jcp.org/en/jsr/detail?id=343 "JSR-343"))

JMS foi uma das especificações que mais sofreram alterações, removendo a maior parte do código desnecessário para enviar mensagens:

\[sourcecode language="java"\] @Stateless public class EnviadorDeMensagens @Resource(lookup = "jms/connectionFactory") ConnectionFactory connectionFactory;

@Resource(lookup="jms/fila") Queue fila;

public void sendMessageNew (String msg) { try (JMSContext context = connectionFactory.createContext();){ context.send(fila,msg); } } } \[/sourcecode\]

Além de [outras alterações e novas funcionalidades](http://www.slideshare.net/arungupta1/jms20), como o suporte a _Paas_, melhorias nos _Message Driven Beans_ e envio de mensagens com hora marcada.

**Bean Validation 1.1** ([JSR-349](http://jcp.org/en/jsr/detail?id=349 "JSR-349"))

As anotações de validação agora podem ser utilizadas em parametros de métodos:

\[sourcecode language="java"\] public void login (@NotNull String login, @NotNull String pass){ ... } \[/sourcecode\]

Componentes como `ConstraintValidator` agora são integrados ao **CDI**, [além de outras pequenas mudanças](http://relation.to/18584.lace).

Outras especificações foram adicionadas ao JavaEE, como [JCACHE](http://jcp.org/en/jsr/detail?id=107) para gerenciamento de cache de objetos, [Batch](https://blogs.oracle.com/arungupta/entry/batch_applications_in_java_ee) para execução de tarefas em lote, similar ao [Spring Batch](http://static.springsource.org/spring-batch/), [JSON-P](http://jcp.org/en/jsr/detail?id=353) com as mesmas funcionalidades do JAX-B, mas para Json. [Na lista completa de funcionalidades do JavaEE 7](https://blogs.oracle.com/arungupta/entry/java_ee_7_key_features) você pode acompanhar todas as _JSRs_ e conhecer mais sobre as novas especificações e evoluções da plataforma Java.

O que acha das novidades? O que você sente falta no desenvolvimento Java EE?
