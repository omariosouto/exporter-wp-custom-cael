---
title: "Repository: seu modelo mais Orientado a Objeto"
date: "2007-06-09"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já tem algum tempo que a [excelente discussão no GUJ](http://www.guj.com.br/posts/list/60916.java) estava me motivando a escrever a respeito.

Para ambientar, a principal discussão é usar: \[java\] Fornecedor fornecedor = ...; List contas = dao.carregaContasPagasDesde1999(fornecedor); \[/java\]

Ou:

\[java\] Fornecedor fornecedor = ...; List contas = fornecedor.getContasPagasDesde1999() \[/java\]

Já que [posts na forma de diálogos](http://nullability.org/?p=97) costumam ser muito interessantes, aproveito um papo que eu e o Paulo Silveira tivemos.

**Paulo**: credo, achei horroroso isso da classe de domínio acessar o repositório **Paulo**: mas fica bonitinha a sentença

**Fabio**: horroroso pq? vc prefere procedural? **Fabio**: repositório é domínio tb **Fabio**: um List, um Map são repositórios **Fabio**: vc acha acessar List e Map horroroso?

**Paulo**: tem certa razão **Paulo**: mas entao poderia chamar o DAO diretamente de repositório

**Fabio**: em muitos casos poderíamos **Fabio**: eu só estava pensando num jeito legal de injetar

**Paulo**: desde que o cara não receba os objetos de dominio

**Fabio**: talvez com interceptor de session, ou listener **Fabio**: ou então injeta nos métodos load()

**Paulo**: então. Complicado um Usuario sempre precisar de Repositorio!

**Fabio**: todo usuário não **Fabio**: só Usuário Managed **Fabio**: quando vc dá new num Usuário, vc tb não consegue navegar em relacionamentos **Fabio**: só faz sentido pegar relacionamento em managed

**Paulo**: certo **Paulo**: mas olha só **Paulo**: vai ter Usuario com repositório e outro sem **Paulo**: em alguns vc vai poder chamar o método que faz buscas, em outros não

**Fabio**: mas isso já é assim

**Paulo**: não com o dao

**Fabio**: imagina Usuario @OneToMany Categoria

**Paulo**: certo

**Fabio**: vc só consegue chamar usuario.getCategorias() no managed, mesma coisa **Fabio**: só vai poder chamar usuario.getCategoriasEspeciais() no managed **Fabio**: não adianta dar new Usuario().getCategoriasEspecias();

**Paulo**: hum... entendi! Podia ser um interceptor como fizemos no caelumweb **Paulo**: aí ele setava um atributo privado

**Fabio**: Ou então o próprio load do dao/repository pode fazer isso.

**Paulo**: mas sei la, baita arquiteturazinha complicada

**Fabio**: nada! só injetar o repositório **Fabio**: o resto é simples **Fabio**: em vez de chamar no dao, que é procedural, chama via getter

**Paulo**: não não, você tem razão. É facílimo de implementar **Paulo**: mas tipo, é muita coisa

**Fabio**: muita coisa do jeito que estão falando no tópico do GUJ! 500 camadas... **Fabio**: Repository não precisa nem ser interface

**Paulo**: aquele interceptador que fizemos para gravar no lucene já não gosto muuuuito

**Fabio**: Repository pode ter referência para Session **Fabio**: e não precisa injetar o repositorio na entidade com listener/interceptor **Fabio**: poderia ser:

\[java\] public class Repository { private final Session session; public Repository(Session session) { this.session = session; } public void get(Long id) { // poderia delegar para um dao, se fosse necessário Usuario u = session.load(id); u.setRepository(this); } } \[/java\]

**Fabio**: ou seja, na hora que você recupera uma Entidade managed, ela já vem com o repositório embutido...

**Paulo**: dessa maneira dá um pouco mais de responsabilidade para os beanzinhos entidades né? fica legal

**Fabio**: isso!

**Paulo**: proximo projeto vamos tentar essa abordagem?

**Fabio**: demorou... ;) **Fabio**: hoje em dia, se vc quiser mostrar um objeto e alguma pesquisa personalizada, vc tem que consultar o objeto e a lista nas lógicas **Fabio**: separados! E aí ejetar os dois **Fabio**: muito feio **Fabio**: geralmente faz-se isso (código usando algum controlador ruinzinho):

\[java\] public class Logica { // extends Action? ;) public void mostraFornecedorEContasPagas() { Long id = request.getParameter("id"); // século passado... Fornecedor f = fornecedorDAO.load(id) List<Conta> contasPagas = contasDAO.listaContasPagasDoMes(fornecedor); // ejeta tudo para mostrar na view. No século passado seria: request.setAttribute("fornecedor", fornecedor); request.setAttribute("contasPagas", contasPagas); } } \[/java\]

**Fabio**: MUITO procedural!

**Paulo**: cara se aplicarmos essas idéias, com velocity isso ia ficar ANIMAL **Paulo**: Ia dar para chamar os métodos a la DAO direto no beanzinho

**Fabio**: ISSO!

**Paulo**: #foreach contas in contas.desde(1994)

**Fabio**: é meu, legal né? **Fabio**: com JSP EL dá tb

**Paulo**: mais ou menos, teriamos de mexer nos evaluators

**Fabio**: não, soh usar padrão javabean **Fabio**: ahhh vc quer passar parâmetro, aí EL não rola mesmo **Fabio**: mas é puro DDD, já fiquei pensando bastante sobre o assunto **Fabio**: ultimamente que tenho enxergado um jeito legal de aplicar

**Paulo**: bacana **Paulo**: acho q vale a gente testar **Paulo**: parava de ficar enfiando getters para expor as coisas para view. **Paulo**: FABIO TODO: blogar sobre isso

Feito! ;)
