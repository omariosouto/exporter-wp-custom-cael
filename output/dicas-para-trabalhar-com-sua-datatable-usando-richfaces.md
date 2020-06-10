---
title: "Dicas para trabalhar com sua DataTable usando RichFaces"
date: "2011-05-10"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Se você trabalha com JSF, já deve ter trabalhado com algumas das várias bibliotecas de componentes, seja com [RichFaces](http://www.jboss.org/richfaces), [PrimeFaces](http://www.primefaces.org/), [IceFaces](http://www.icefaces.org/main/home/) ou ainda outra. Para nossa escolha, levamos em consideração fatores como: variedade de componentes, facilidade de customização, documentação e principalmente, se possui os componentes necessários para resolver o problema específico da aplicação.

A [MundoJ edição 44](http://mundoj.com.br/44conteudo.shtml) comparou vários dos principais componentes dessas bibliotecas, mostrando as vantagens e desvantagens de cada uma. Especificamente no RichFaces, podemos utilizar o componente [<rich:dataTable/>](http://livedemo.exadel.com/richfaces-demo/richfaces/dataTable.jsf?c=dataTable&tab=usage), que é simples de ser usado e atende muitos casos, sendo uma boa evolução em relação à `dataTable` padrão.

Com ele podemos filtrar usando os atributos `filterBy` e `filterEvent` ou ordenar as informações por alguma coluna usando o atributo `sortBy`. Também é possível fazer um agrupamento de linhas e colunas (colspan e rowspan) usando as tags `rich:columnGroup` assim como fazer uso de sub tabelas usando a tag `rich:subTable` como no exemplo abaixo:

\[sourcecode language="xml"\] <rich:columnGroup> <rich:column rowspan="2"> <rich:spacer /> </rich:column> <rich:column colspan="3"> <rich:spacer /> </rich:column> <rich:subTable var="" value=""> <rich:column> <rich:spacer/> </rich:column> </rich:subTable> </rich:columnGroup>

\[/sourcecode\]

![](https://blog.caelum.com.br/wp-content/uploads/2011/05/Screen-shot-2011-05-10-at-12.05.35-AM.png "Screen shot 2011-05-10 at 12.05.35 AM")

Quando precisamos de algo mais sofisticado, como selecionar uma linha, reordenar as colunas ou até mesmo agrupar as informações, temos que trocar o `dataTable` por um componente mais poderoso, o `[ExtendedDataTable](http://livedemo.exadel.com/richfaces-demo/richfaces/extendedDataTable.jsf?c=extendedDataTable&tab=usage)`. Esse componente é muito semelhante ao primeiro e vai além, permitindo modificações extras feitas diretamente pelo usuário, tornando a experiência mais agradável e flexível, podendo analisar as informações de uma perspectiva diferenciada. Para selecionar uma linha usamos o atributo `selection` e se precisarmos selecionar varias linhas alteramos o atributo `selecionMode` (multi, single e none). Se quisermos agrupar as linhas a partir dos valores de uma determinada coluna usamos o atributo `groupingColumn`.

![](https://blog.caelum.com.br/wp-content/uploads/2011/05/Screen-shot-2011-05-10-at-12.05.55-AM.png "Screen shot 2011-05-10 at 12.05.55 AM")

Navegando pelo sistema, o cliente perceberá que ao voltar para a tela de listagem, todas as modificações feitas na tabela foram perdidas, como é habitual na web. O `ExtendedDataTable` por padrão, não guarda as customizações feitas pelo usuário. Todavia, ele possui um mecanismo para manter o estado da tabela através da propriedade `[tableState](http://docs.jboss.org/richfaces/latest_3_3_X/en/devguide/html/rich_extendedDataTable.html)`. Essa propriedade guarda uma configuração em uma `String` com o estado da tabela para que possamos armazená-lo.

\[sourcecode language="xml"\] <rich:extendedDataTable tableState="#{preferenciaController.preferencia}"/> \[/sourcecode\]

Basta fazer um binding com um ManagedBean em uma `String`. Para que a solução seja válida, precisamos que o escopo desse Bean seja no mínimo de Session, para manter as configurações durante todo o tempo em que o usuário estiver usando o sistema:

\[sourcecode language="java"\] @ManagedBean(name="preferenciaController") @SessionScoped public class PreferenciaController { private String preferencia; public String getPreferencia() { return preferencia; } public void setPreferencia(String preferencia) { this.preferencia = preferencia; } } \[/sourcecode\]

A `String preferencia` será populada com um JSON que contém toda a configuração do estado atual da tabela, como por exemplo o abaixo:

\[sourcecode language="java"\] { "columnsVisibility":{ "categoria":"1","numeroSolicitacao":"1","situacao":"1" }, "columnGroupingState":{ "order":"ASCENDING","columnId":"numeroSolicitacao" }, "columnsSizeState":{ "categoria":"100","numeroSolicitacao":"100","situacao":"28", }, "columnsOrder":\["numeroSolicitacao","categoria","situacao"\] } \[/sourcecode\]

Contudo, solucionamos parcialmente o problema, pois se o usuário sair do sistema, a sessão expirar ou entrar em outro navegador, perderemos toda a informação que estava no JSON. Para resolvermos definitivamente, podemos persistí-lo em um banco de dados, assim, quando ele voltar ao sistema, basta carregar a informação novamente:

\[sourcecode language="java"\] @Entity public class PreferenciaUsuario { private String preferencia; @Column(length=3500) public String getPreferencia() { return preferencia; } public void setPreferencia(String preferencia) { this.preferencia = preferencia; } } \[/sourcecode\]

Ao começar a utilizar esse recurso, você notará que para cada modificação feita em seu componente, uma requisição Ajax será disparada para atualizar o valor em seu ManagedBean. Aqui há um problema, pois um update pode ser disparado a cada uma dessas requisições ajax, se as preferências carregadas previamente estiverem ainda gerenciadas (managed) pelo seu EntityManager, seja porque o seu ManagedBean está em um escopo de Conversation ou o [@PersistenceContext(type=PersistenceContextType.EXTENDED)](http://blogs.sun.com/enterprisetechtips/entry/extended_persistence_context_in_stateful) foi utilizado.

Existem algumas formas de solucionar, uma delas é após carregar o objeto de Preferências do usuário, colocá-lo no estado [detached](http://download.oracle.com/javaee/5/api/javax/persistence/EntityManager.html#clear()) e na hora de salvar fazer merge. Uma outra forma, caso esteja utilizando o Seam, é controlar [manualmente o flush da transação](http://docs.jboss.org/seam/2.2.1.CR1/reference/en-US/html_single/#d0e7456). Ou até mesmo oferecer uma forma explícita do usuário escolher quando gravar o estado da tabela.

![](https://blog.caelum.com.br/wp-content/uploads/2011/05/Screen-shot-2011-05-10-at-12.06.27-AM.png "Screen shot 2011-05-10 at 12.06.27 AM")

Tabelas e listagens são frequentes numa aplicação web, e parece que o cliente nunca está satisfeito: sempre quer um filtro a mais, uma coluna a menos, uma ordenação aqui e ali. Os datatables dos diversos vendors de JSF possibilitam realizar esse trabalho com facilidade, e cada um vai oferecer opções específicas e bastante elaboradas. Conhece-las irá poupá-lo de muito trabalho.
