---
title: "4 recursos novos do CDI 1.1"
date: "2013-08-20"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já vimos [aqui no blog](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/) que o CDI é a especificação do Java EE 6 que cuida da parte de injeção de dependências de seu projeto. Em pouco tempo essa especificação se tornou extremamente importante e popular, e agora em sua versão 1.1 foram incluidas uma serie de novas funcionalidades e mudanças que podem ser muito úteis pra você que já adotou o CDI em um de seus projetos, ou ainda pretende adotar.

### **1\. Ativação Global de Interceptors, Decorators e Alternatives**

Uma das grandes mudanças que entraram na especificação do CDI 1.1 é a ativação global de Interceptors, Decorators e Alternatives. No CDI 1.0 esses elementos eram ativos apenas em um bean arquive, ou seja, se sua aplicação continha um jar (uma library qualquer) com interceptors, esses interceptors seriam vistos apenas naquele jar. Isso aconteceu porque em um primeiro momento não foi planejado uma boa solução pra ordenar globalmente esses elementos em toda a aplicação. Agora, além dessa ativação global, é possivel definir a prioridade em que seus interceptors, decorators e alternatives serão executados (no caso dos alternatives apenas o com maior prioridade será escolhido pelo container).

Para definir a prioridade destes elementos basta anotá-los com `@Priority` (javax.annotations) e definir um valor que será considerado para ordenação. Por exemplo, um interceptor que deve ser executado logo após os insterceptors de suas libraries, pode fazer algo como:

\[code language="java"\] @Priority(Interceptor.Priority.LIBRARY\_BEFORE) @Interceptor public class MeuInterceptor { ... } \[/code\]

Outros possiveis valores são: `PLATFORM_BEFOR = 0, LIBRARY_BEFORE = 1000, APPLICATION = 2000, LIBRARY_AFTER = 3000, PLATFORM_AFTER = 4000`. Interceptors definidos na aplicação devem estar entre o intervalor de `APPLICATION` e `LIBRARY_AFTER`, e caso eu queira ordena-los posso incrementar um valor que considerar adequado em sua prioridade, algo como:

\[code language="java"\] @Priority(Interceptor.Priority.APPLICATION + 50) @Interceptor public class MeuInterceptor { ... } \[/code\]

Você também pode ativar seus interceptors, decorators e alternatives explicitamente pelo arquivo beans.xml, nesse caso a prioridade dos elementos será a ordem em que forem escritos, por exemplo:

\[code language="xml"\] <beans xmlns="...">

<interceptors> <class>br.com.caelum.PrimeiroInterceptor</class> <class>br.com.caelum.SegundoInterceptor</class> </interceptors>

</beans> \[/code\]

### **2\. Filtros de Exclusão**

Outra importante funcionalidade agregada na especificação do CDI 1.1 foram os filtros de exclusão de classes para serem ignoradas pelo container, ou seja, classes que não serão escaneadas e vistas como managed beans pelo CDI. Dessa forma posso configurar em meu `beans.xml` a exclusão de classes, como por exemplo o caso de algumas classes úteis do projeto:

\[code language="xml"\] <beans ... > <scan> <!-- posso excluir apenas a classe: --> <exclude name="br.com.caelum.util.calendarUtil.java"/> <!-- ou posso excluir o pacote util completo: --> <exclude name="br.com.caelum.util.\*"/> </scan> </beans> \[/code\]

Além dessa facilidade, ainda posso condicionar quando quero excluir o escaneamento das classes. Por exemplo, para excluir o pacote de interceptors inteiro caso exista a propriedade do sistema "exclude-interceptors" definida:

\[code language="xml"\] <exclude name="br.com.caelum.interceptors.\*\*"> <if-system-property name="exclude-interceptors"/> </exclude> \[/code\]

Ou ainda definir se quero ou não excluir um pacote, subpacotes ou classes de acordo com a disponibilidade de algum outro bean:

\[code language="xml"\] <exclude name="br.com.caelum.jsf.\*\*"> <if-class-not-available name="javax.faces.context.FacesContext"/> </exclude> \[/code\]

Outra forma particularmente fácil de resolver o problema é com o uso da anotação `@Vetoed`. A partir do momento em que uma classe esteja anotada com `@Vetoed`, ela será ignorada pelo container do CDI da mesma forma que se tivesse declarada na tag <`exclude`\> do arquivo `beans.xml`.

### **3\. Novo Evento AfterTypeDiscovery**

O evento _AfterTypeDiscovery_ é disparado logo após a descoberta de classes do container. Observar este evento te possibilita adicionar ou remover classes da lista de interceptors, decorators ou alternatives de sua aplicação. Você pode inclusive reordenar estas listas, pois o container só define a ordenação desses elementos após este evento ter acontecido.

Interface javax.enterprise.inject.spi.AfterTypeDiscovery:

\[code language="java"\] public interface AfterTypeDiscovery { public List<Class<?>> getAlternatives(); public List<Class<?>> getInterceptors(); public List<Class<?>> getDecorators(); public void addAnnotatedType(AnnotatedType<?> type, String id); } \[/code\]

Para adicionar um interceptor manualmente na lista de interceptors descobertos em minha aplicação, por exemplo, posso criar uma classe bem simples que observa o evento AfterTypeDiscovery, buscar a lista de todos os interceptors e adicioná-lo na posição que eu precisar. O mesmo pode ser feito com decorators e alternatives.

\[code language="java"\] public void observer(@Observes AfterTypeDiscovery evento ){ // adiciona o meu interceptor como terceiro a ser executado evento.getInterceptors().add(2, MeuInterceptor.class); } \[/code\]

Quando invoco o método `.getInterceptors()`, a lista virá ordenada de acordo com o peso de `@Priority` dos elementos, ou pela ordem em que foram registrados no arquivo `beans.xml`.

### **4\. Trabalhando com uma @TransientReference**

Imagine classes como a _ConnectionFactory_ que você precisa injetar para criação da sua conexão, mas pretende fazer isso uma unica vez, ou seja, não precisa guardar uma referência em memória desse objeto. Agora isso é possível com o uso da anotação `@TransientReference`! Por exemplo:

\[code language="java"\] @SessionScoped public class ConnectionManager {

private final Connection connection;

public ConnectionManager(@Inject @TransientReference ConnectionFactory cf) { this.connection = cf.getConnnection(); }

public Connection getConnection() { return connection; } } \[/code\]

Se você gostou do CDI, possivelmente irá se interessar pelo nosso curso de [Laboratório Web com JSF e CDI](http://www.caelum.com.br/curso-java-web-jsf-cdi/), e também pela documentação de sua [especificação](http://www.cdi-spec.org/).

Gostou das novidades do CDI? A [nova versão do VRaptor](http://github.com/caelum/vraptor4) utiliza os recursos dessa especificação e possibilita que voce desenvolva para web de maneira ainda mais rápida!
