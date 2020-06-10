---
title: "Guia rápido de migração EJB2 para EJB3"
date: "2007-03-19"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Migrar um sistema de EJB2 para EJB3 poderá ser um trabalho muito requisitado no futuro. A especificação do EJB3 facilita muito essa tarefa.

Considere um session bean 2.x chamado `ServicoBean`, com sua interface remota `Servico` e a interface home remota `ServicoHome`: \[java\] public interface ServicoHome extends EJBHome { Servico create() throws RemoteException, CreateException; }

public interface Servico extends EJBObject { void executa() throws RemoteException; }

public class ServicoBean implements SessionBean {

public void executa() { // executa a logica de negocios }

public void ejbActivate() { }

public void ejbPassivate() { }

public void ejbRemove() { }

public void setSessionContext(SessionContext sc) { } } \[/java\]

Repare que só poderemos alterar a nossa classe `ServicoBean`! A alteração das duas interfaces é inadmissível, para que não tenhamos de mudar os possíveis vários clientes desse EJB. Vamos mudar então o `ServicoBean`, começando por anota-lo como um `@Stateless` session bean e informando quais são suas interfaces (em vez de anotar as interfaces, o que é a opção mais interessante no EJB3). Além disso não implementamos mais a interface `SessionBean`, já que qualquer método de callback pode ser definido através de anotações:

\[java\] @Stateless @Remote(Servico.class) @RemoteHome(ServicoHome.class) public class ServicoBean { } \[/java\]

Infelizmente aqui não podemos implementar a interface `Servico`, como ocorreria em um EJB3 comum, por causa de um dos mesmos motivos que um session bean 2.x não deve implementar sua interface remota: teríamos de escrever os métodos encontrados em `EJBObject` (além de não ter sentido semântico, mas isso foi quebrado no EJB3).

Precisamos então adicionar nossos métodos de negócio com cuidado, para não ocorrer "spec violations" durante o deploy:

\[java\] @Stateless @Remote(Servico.class) @RemoteHome(ServicoHome.class) public class ServicoBean {} public void executa() { // executa a logica de negocios } } \[/java\]

No caso de um Session Bean Stateful, teríamos ainda o `ejbCreate`. Para isso basta anotarmos um ou mais métodos com `@Init`. Esse método pode ter qualquer nome, é pela assinatura do create na `Home` e pela anotação que o container descobrirá qual invocar. Pronto! Você pode usar seus clientes antigos normalmente, e agora você não precisa mais do `ejb-jar.xml` no jar desse seu ejb e pode contar com todos os recursos da nova especificação.

Os entity beans também podem ser facilmente migrados através dos VOs, desde que você tenha seguido as boas práticas e não os esteja acessando via interface remota.
