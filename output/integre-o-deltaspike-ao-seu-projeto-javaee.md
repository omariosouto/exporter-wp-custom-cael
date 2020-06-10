---
title: "Integre o DeltaSpike ao seu projeto JavaEE"
date: "2014-11-18"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Você pode estar órfão das funcionalidades do [Seam 3](http://seamframework.org), [CDI Source](http://www.andygibson.net/blog/article/announcing-cdisource/) ou [Apache CODI](http://myfaces.apache.org/extensions/cdi/).  Pode também estar muito bravo com a especificação JAAS do JavaEE com sua falta de atualização e difícil uso. Além disso, [triste com o suporte ao escopo de Conversation do CDI](http://stackoverflow.com/questions/8049294/cdi-conversationscoped-with-ajax) ser ainda tão ruim na integração com [JSF e Ajax](http://forum.primefaces.org/viewtopic.php?f=3&t=17995). Por fim, sua aplicação utiliza um servlet container e você ainda tem aqueles códigos toscos pouco produtivos de abertura e fechamento de [transações controlados manualmente](http://stackoverflow.com/questions/19194256/why-use-stateless-session-beans-for-transaction-demarcation).

Para ajudar nesses questionamentos a resposta é [DeltaSpike](http://deltaspike.apache.org/migration-guide.html). É uma biblioteca cujo objetivo principal foi [unificar as diferentes](http://wiki.apache.org/incubator/DeltaSpikeProposal) funcionalidades existentes das mais diversas bibliotecas que auxiliam nas tarefas diárias do desenvolvedor. Além disso, ele pretende ser a [biblioteca padrão](http://java.dzone.com/articles/introducing-deltaspike-10) para extensões ligadas principalmente ao JavaEE. Basicamente é um junção de forças, ao invés de cada um fazer a sua solução, os commiters dos projetos citados anteriormente se uniram.

O projeto possui uma [api extensa](http://deltaspike.apache.org/documentation/), passando [por testes](http://deltaspike.apache.org/documentation/test-control.html), [validação](http://deltaspike.apache.org/documentation/bean-validation.html), ajuda para ter acesso aos [contextos HTTP](http://deltaspike.apache.org/documentation/servlet.html), criação de [Repositórios](http://deltaspike.apache.org/documentation/data.html), até mesmo uma integração com o [Quartz](http://deltaspike.apache.org/documentation/scheduler.html). Enfim, muitas features interessantes para facilitar o seu trabalho, inclusive se você já estiver em um [Application Server](http://en.wikipedia.org/wiki/Comparison_of_application_servers), que mesmo com a [ótima](https://blog.caelum.com.br/novidades-javaee7-2/) versão do [JavaEE 7](http://refcardz.dzone.com/refcardz/java-enterprise-edition-7#refcard-download-social-buttons-display), ainda possui alguns gaps, como a parte de segurança. Seria impossível cobrir todas elas, até porque desconheço boa parte, mas tenho certeza que você escreverá nos comentários as features que está usando para debatermos.

A configuração inicial do [DeltaSpike é bem simples](http://blog.arungupta.me/2014/06/deltaspike-1-0-extend-javaee-techtip32/), basicamente é adicionar dependências no `pom.xml`. Como o projeto é divido por módulos, o único realmente necessário é o core, o resto é adicionado de acordo com a necessidade do desenvolvedor. Vamos para o código então.

Supondo que você esteja em um Servlet Container, o primeiro passo a ser feito é prover um `EntityManager` para o DeltaSpike poder trabalhar. Com CDI, basta usar uma fábrica:

\[code language="java"\] public class JPAUtil {

private static EntityManagerFactory emf = Persistence.createEntityManagerFactory("cacs");

public static void closeEntityManagerFactory() { JPAUtil.emf.close(); }

@Produces @RequestScoped public EntityManager createEntityManager() { return JPAUtil.emf.createEntityManager(); }

public void closeEntityManager(@Disposes final EntityManager entityManager) { if (entityManager.isOpen()) { entityManager.close(); } } } \[/code\]

Pronto, vamos pesquisar alguns dados de usuário. Para isso, precisamos criar um [repositório](https://blog.caelum.com.br/repository-seu-modelo-mais-orientado-a-objeto/) e suas queries. O modulo [data](http://deltaspike.apache.org/documentation/data.html) tem um suporte muito parecido com o [framework Esfinge](https://github.com/EsfingeFramework), basta montar o nome do método com um formato pré-definido e a query será gerada dinamicamente

\[code language="java"\] @Repository(forEntity = Usuario.class) public interface TodosUsuarios extends EntityRepository<Usuario, Integer> { List<Usuario> findByEmailLikeOrderById(String email);

Usuario findByEmailEqualAndSenhaEqual(String email, String senha);

Long count(); } \[/code\]

Agora basta injetar a referência para `TodosUsuarios` conforme exemplo:

\[code language="java"\] @Path("/usuarios") public class ServicosUsuario {

@Inject private TodosUsuarios todosUsuarios;

@GET @Path("/{email}") @Produces(MediaType.APPLICATION\_JSON) public Response listarUsuariosPorEmail(@PathParam("email") String email) { return Response.ok(todosUsuarios.findByEmailLikeOrderById("%"+email+"%")).build(); }

@GET @Path("/total") public Response total() { return Response.ok(todosUsuarios.count()).build(); } } \[/code\]

Ainda relacionado ao Banco de Dados, nos códigos que precisamos de transação, a solução era chamar diretamente o `entityManager.getTransaction().begin()` ou criar na [mão interceptadores CDI](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/) para criar transações demarcadas. O módulo JPA já oferece esse serviço, basta adicionar o [interceptador](http://deltaspike.apache.org/documentation/jpa.html) no `beans.xml` e anotar os métodos que necessitam de transação com `@Transactional` (importe do pacote deltaSpike). O módulo também oferece um suporte interessante para [Extended Persistence Context](http://deltaspike.apache.org/documentation/jpa.html).

[No aspecto de segurança](http://in.relation.to/Bloggers/SecuringYourApplicationsWithPicketLinkAndDeltaSpike), o JavaEE deixa muito a desejar, então o [módulo do projeto](http://deltaspike.apache.org/documentation/security.html) oferece serviços interessantes, por exemplo, determinados métodos na sua aplicação só podem ser acessos caso o usuário tenha determinado perfil. Criamos uma anotação para identificar essa regra:

\[code\] @Retention(RetentionPolicy.RUNTIME) @Target(value = { ElementType.TYPE, ElementType.METHOD }) @Documented @SecurityBindingType public @interface Critico { } \[/code\]

Criamos um classe para fazer a regra de negocio, perceba que o UsuarioLogado é um classe provida pelo CDI:

\[code\] public class AutorizadorCritico { @Secures @Critico public boolean conferirPerfil(InvocationContext invocationContext, BeanManager manager, UsuarioLogado usuarioLogado) throws Exception { return usuarioLogado.isAdministrador()? true : false; }

@Produces @Critico @Named("fornecedores") public List<Fornecedor> listaFornecedores() { return todosFornecedores.listar(); } } \[/code\]

Por fim, o módulo de [JSF](http://deltaspike.apache.org/documentation/jsf.html) oferece vários recursos, talvez o mais interessante seja uma nova gama de escopos. Um cenário comum em aplicações que usam esta tecnologia é o cadastro baseado em wizards, no qual há navegação por mais de uma tela. Apesar das novas [novidades da versão 2.0 do framework](https://blog.caelum.com.br/10-razoes-para-migrar-sua-aplicacao-para-jsf-2/), fazer isso em JSF não costuma ser muito trivial. Você pode usar o `@ViewScoped`, o `@ConversationScoped` do CDI ou até mesmo o Flash scope, mas a solução que eu achei mais conveniente é o `@ViewAcessScoped` do DeltaSpike.

Temos um cadastro no qual a primeira página é a listagem dos elementos e quando um elemento é selecionado, ocorre uma navegação para a página de edição. As duas páginas possuem referência para o mesmo [BackingBean](http://balusc.blogspot.com.br/2011/09/communication-in-jsf-20.html). Quando selecionamos um item na página de listagem, queremos que este item ainda fique ativo na página de edição. Se usássemos o `@ViewScope` do JSF, quando ocorre a navegação, o BackingBean seria criado novamente, perdendo a referência para o item selecionado.

O `@ViewAccessScoped` resolve esse problema. A definição é muito boa:

> However, sometimes you need beans with a lifetime which is as long as needed and as short as possible - which are terminated automatically (as soon as possible)

. O escopo mantém um objeto ativo enquanto estiver referência para ele. Exemplificando:

Primeira página de listagem: `#{usuarioBean.usuarios}` e `#{usuarioBean.editar(usuario)}`

\[code language="java"\] @ViewAcessScoped @ManagedBean class UsuarioBean {

} \[/code\]

Já na segunda página de edição temos a referência `#{usuarioBean.usuarioSelecionado.nome}`. Aqui o objeto `UsuarioBean` ainda está ativo e só irá morrer quando o usuário navegar para uma próxima  página em que não haja referência para este ManagedBean.

> The simple rule is, as long as the bean is referenced by a page - the bean will be available for the next page (if it’s used again the bean will be forwarded again)

.

Por incrível que pareça ainda recebo muitos [questionamentos em sala sobre o Seam](http://in.relation.to/Bloggers/SeamNextAnnouncement), e nas minhas aulas toda vez que eu falo sobre o DeltaSpike os alunos costumam gostar, espero que agora ainda mais. Você pode se inteirar mais sobre essas tecnologias com [nossos cursos](http://www.caelum.com.br/curso-java-web-jsf-cdi/), onde sempre debatemos as novidades e outras opções. E você, gostou de alguma feature? Já utiliza? Não esqueça de deixar seu comentário.
