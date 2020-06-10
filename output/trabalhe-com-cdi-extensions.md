---
title: "Trabalhe com CDI extensions"
date: "2013-04-02"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

No final do ano passado, implementamos uma integração inicial do [VRaptor com CDI]( https://github.com/asouza/vraptor/tree/master/vraptor-core). Durante o desenvolvimento tivemos que usar bastante funcionalidades [dessa poderosa especificação](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/ "motivos de sobra para usar o CDI").  O desafio que tivemos foi o de manter a compatibilidade em relação ao construtor com argumentos do VRaptor. O problema é que todo ponto de injeção para o CDI deve estar marcado com a anotação `@Inject`, diferente de como é no VRaptor. Abaixo segue um exemplo:

\[code language="java"\] @Component public class CDIComponent implements ComponentFactory { private final MyRequestComponent component;

@Inject public CDIComponent(MyRequestComponent component) { this.component = component; }

public ComponentToBeProduced getInstance() { return new ComponentToBeProduced(); } } \[/code\]

Só que os usuários do VRaptor estão acostumados a apenas declarar um construtor, receber os argumentos e deixar que o framework se vire para realizar a injeção de dependências. Aí é que está justamente o ponto de customização. Precisamos pegar todas essas classes que possuem construtor com argumentos e avisar ao CDI que ele deve considerar esses construtores como se estivessem anotados com `@Inject`. Para fazer isso, [a especificação fornece diversos pontos de extensão](http://docs.jboss.org/weld/reference/latest/en-US/html/extend.html), onde podemos registrar classes novas que não foram previamente scaneadas, excluir classes que não precisamos além de poder alterar as configurações registradas para uma determinada classe.

Abaixo segue o exemplo da classe que já vamos criar! Para sermos notificados sobre o evento de configuração das classes, vamos utilizar a anotação `@Observes`, fazendo uso da implementação do Design Pattern [Observer](https://blog.caelum.com.br/diminua-suas-dependencias-com-os-eventos-do-cdi/ "eventos no cdi"), que já é suportado nativamente por qualquer implementação do CDI.

\[code language="java"\] public class AddInjectToConstructorExtension { public void processAnnotatedType(@Observes final ProcessAnnotatedType pat) { //nossa mágica vai entrar aqui } } \[/code\]

O objeto do tipo _ProcessAnnotatedType_ representa as configurações de uma determinada classe. Uma das coisas que podemos fazer com esse objeto é, por exemplo, saber quais anotações existem em cada elemento da classe. Podemos também adicionar novas informações, por exemplo que a classe tem a anotação `@Inject` no seu construtor. O primeiro passo é criar um novo objeto do tipo _ProcessAnnotatedType_, que vai ser o substituto do original. Para não gastarmos tempo implementando isso na mão, vamos usar um projeto da Apache chamado [**Delta Spike**](http://incubator.apache.org/deltaspike/ "site do deltaspike"). Ele possui diversas extensões para ajudar em projetos que usam CDI.

\[code language="java"\] public class AddInjectToConstructorExtension {</pre> public void processAnnotatedType(@Observes final ProcessAnnotatedType pat) { AnnotatedTypeBuilder builder = new AnnotatedTypeBuilder(); //lendo as configurações originais builder.readFromType(pat.getAnnotatedType()); } }

\[/code\]

Agora precisamos adicionar a anotação no construtor e informar ao container que ele deve usar essa nova configuração:

\[code language="java"\] public class AddInjectToConstructorExtension { public void processAnnotatedType(@Observes final ProcessAnnotatedType pat) { AnnotatedTypeBuilder builder = new AnnotatedTypeBuilder(); builder.readFromType(pat.getAnnotatedType()); List<Constructor> constructors = //pega os construtores com argumentos da classe if(hasArgsConstructorAndNoInjection) { Constructor constructor = constructors.get(0); builder.addToConstructor(constructor, new AnnotationLiteral<Inject>() {}); // aqui efetivamente trocamos a configuração que o CDI vai usar ler a classe. pat.setAnnotatedType(builder.create()); } } } \[/code\]

O código de verificação do construtor foi deixado de fora de maneira proposital, apenas para não perdermos o foco do post. Caso queira dar uma olhada no código completo, basta seguir este [link](https://github.com/asouza/vraptor/blob/master/vraptor-core/src/main/java/br/com/caelum/vraptor/ioc/cdi/extensions/AddInjectToConstructorExtension.java "código no github").

Agora que já temos nossa extensão, vamos aos detalhes menores. Toda extensão no CDI deve implementar a interface de marcação chamada _Extension_. Basicamente segue a mesma ideia da interface _Serializable_:

\[code language="java"\] public class AddInjectToConstructorExtension implements Extension { public void processAnnotatedType(@Observes final ProcessAnnotatedType pat) { AnnotatedTypeBuilder builder = new AnnotatedTypeBuilder(); builder.readFromType(pat.getAnnotatedType()); List<Constructor> constructors = //pega os construtores com argumentos da classe if(hasArgsConstructorAndNoInjection){ Constructor constructor = constructors.get(0); builder.addToConstructor(constructor, new AnnotationLiteral<Inject>() {}); //aqui efetivamente trocamos a configuração que o CDI vai usar ler a classe. pat.setAnnotatedType(builder.create()); } } } \[/code\]

Também precisamos avisar ao container que ele deve carregar essa nossa extensão, assim como avisamos a um container web que ele deve carregar nosso _Listener_. Para fazer isso, basta declarar um arquivo chamado `javax.enterprise.inject.spi.Extension`, dentro de uma pasta chamada `services`. Essa pasta deve ficar dentro da `META-INF`.  Agora basta que seja escrito o nome completo de cada classe que representa uma extensão:

\[code\] br.com.caelum.vraptor.ioc.cdi.extensions.AddInjectToConstructorExtension br.com.caelum.vraptor.ioc.cdi.extensions.ComponentExtension br.com.caelum.vraptor.ioc.cdi.extensions.ComponentFactoryExtension \[/code\]

Pronto! Nossa extensão vai ser carregada e todas as configurações de classes que tiverem construtor com argumentos, e não tiverem adicionado a anotação `@Inject`, vão ser alteradas. Tudo isso de maneira completamente transparente para quem está usando o VRaptor! Em breve, o CDI vai entrar como mais um provider de injeção no VRaptor, juntando-se ao Spring, Guice e Pico.
