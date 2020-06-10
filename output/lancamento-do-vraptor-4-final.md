---
title: "Lançamento do VRaptor 4 final"
date: "2014-04-28"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Depois de mais de 60 mil downloads diretos do VRaptor 3 (sem contar via maven), mais de [dois mil commits](https://github.com/caelum/vraptor4), a versão 4.0.0.Final do VRaptor está disponivel para download. Junto com ela um [novo site](http://vraptor.com.br) com novidades sobre as atualizações do framework. Você já pode começar a usar baixando o [blank-project](https://bintray.com/caelum/VRaptor4/br.com.caelum.vraptor/) ou nosso projeto exemplo, o [vraptor-musicjungle](https://bintray.com/caelum/VRaptor4/br.com.caelum.vraptor/).

Ou se você utilizava alguma versão anterior, já pode atualizar a dependência em seu projeto:

\[code language="xml"\] <dependency> <groupId>br.com.caelum</groupId> <artifactId>vraptor</artifactId> <version>4.0.0.Final</version> </dependency> \[/code\]

E se você nao conhece o VRaptor, pode começar agora pelo [guia de 1](http://www.vraptor.org/pt/docs/guia-de-1-minuto/) e [de 10 minutos](http://www.vraptor.org/pt/docs/guia-de-10-minutos/).

O VRaptor 4 utiliza o [CDI 1.1](http://www.cdi-spec.org/) como container de DI (injeção de dependências), portanto tira proveito das features específicas dessa já conhecida e bastante utilizada especificação do JavaEE 7.

E o melhor, utilizando VRaptor você também pode tirar proveito desses recursos. Caso anda não esteja familiarizado com o CDI, você pode ler [como começar com essa especificação](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/), conhecer [algumas das features da versão 1.1](https://blog.caelum.com.br/4-recursos-novos-do-cdi-1-1) e entender como [customizar a produção de suas dependências](https://blog.caelum.com.br/customizando-a-producao-de-dependencias-no-cdi/), tudo no blog da Caelum.

## Trabalhando com eventos do CDI

Nosso fluxo principal agora é tratado com [eventos do CDI](https://blog.caelum.com.br/diminua-suas-dependencias-com-os-eventos-do-cdi/), assim nosso código ficou ainda mais desacoplado e extensível. [Nessa página da documentação](http://caelum.github.io/vraptor4/pt/docs/eventos/) exibimos um mapa dos eventos e seus observers, assim como uma explicação simples sobre o que cada um deles representa.

Você pode facilmente observar os eventos do VRaptor, para isso basta escrever um observer simples como esse:

\[code language="java"\] import javax.enterprise.event.Observes; import br.com.caelum.vraptor.events.ControllerNotFound;

public class ControllerNotFoundHandler { public void metodo(@Observes ControllerNotFound evento) { // alguma ação para quando o controller não for encontrado } } \[/code\]

## Um novo modelo de Interceptors

Se você precisa de ordenação na execução de seus eventos, considere utilizar Interceptors. O VRaptor 4 possui um novo modelo baseado em anotações! Veja como pode escrever seu interceptor:

\[code language="java"\] @Intercepts public class ApplicationInterceptor {

@Accepts public boolean accepts(ControllerMethod method) { return method.containsAnnotation(Audit.class); }

@BeforeCall public void before() { // código a ser executado antes da lógica }

@AfterCall public void after() { // código a ser executado depois da lógica }

@AroundCall public void intercept(SimpleInterceptorStack stack) { // código a ser executado antes da lógica stack.next(); // continua a execução // código a ser executado depois da lógica } } \[/code\]

Um interceptor sem o método anotado com `@Accepts` é global, ou seja, vai interceptar todas as requisições. Outra novidade dos interceptors é que você pode utilizar os aceptors customizados como o `@AcceptsWithPackage` e `@AcceptsWithAnnotations`:

\[code language="java"\] @Interceptor @AcceptsWithAnnotations(Audit.class) public class AuditInterceptor { ... } \[/code\]

A criação de seus próprios custom acceptors é bem simples e [está bem detalhada aqui](http://caelum.github.io/vraptor4/pt/docs/interceptadores/).

## Environment integrado ao core

O plugin [vraptor-environment](https://github.com/caelum/vraptor-environment) foi integrado ao core e ganhou novas features! Você ainda pode receber o `Environment` injetado e chamar seu método `get` para ler propriedades, dessa forma:

\[code language="java"\] String email = environment.get("email"); \[/code\]

Porém agora como alternativa você pode simplesmente pedir a propriedade injetada com o uso da anotação `@Property`:

\[code language="java"\] @Inject @Property private String email; \[/code\]

Como neste caso a propriedade tem o mesmo nome da chave, passar o `value` é opcional. Mas você poderia fazer algo como:

\[code language="java"\] @Inject @Property(value="email-de-dev") private String email; \[/code\]

... e ainda passar um valor `default`, para caso a propriedade não exista:

\[code language="java"\] @Property(value="email-de-dev", defaultValue="dev@caelum.com.br"). \[/code\]

## E quanto aos nossos plugins?

Dentre diversos outros aqui não listados, os seguintes plugins já possuem uma versão compativel com essa nova versão do framework:

[vraptor-time-converters](https://github.com/caelum/vraptor-time-converters) - trabalhar com date time e java time [vraptor-simplemail](https://github.com/caelum/vraptor-simplemail) - já conhecido plugin de envio de emails [vraptor-errorcontrol](https://github.com/caelum/vraptor-error-control) - controle de erros efetivo com envio de e-mails [vraptor-quartzjob](https://github.com/caelum/vraptor-quartzjob) - schedule Quartz para agendamento de tarefas [vraptor-freemarker](https://github.com/caelum/vraptor-freemarker) - trabalhar com templates do freemarker [vraptor-jpa](https://github.com/caelum/vraptor-jpa) e vraptor-hibernate - produtores e controle de transação [vraptor-brutauth](https://github.com/leonardowolter/vraptor-brutauth), [vraptor-shiro](https://github.com/dipold/vraptor-shiro) e [vraptor-authz](https://github.com/Turini/vraptor-authz/) - autent. e autorização [vraptor-dash](https://github.com/caelum/vraptor-dash) - dashboard com diversar ferramentas pro seu projeto

Você pode ler mais sobre essas e outras novidades [no site do framework](http://www.vraptor.org). O VRaptor 4 é uma evolução da versão 3, portanto grande parte dos conceitos como as convenções, injeção pelo construtor e uso do `Result`, continuam da mesma forma. Para te ajudar a migrar de VRaptor 3 para VRaptor 4 [criamos esse tutorial](http://www.vraptor.org/pt/docs/migrando-de-um-projeto-com-vraptor3/).

Agradecemos a todos os desenvolvedores ativos e pessoal da comunidade que contribuem tanto para o crescimento do projeto. Esse é o momento perfeito para usar VRaptor 4 em seus projetos, **vários projetos já estão usando em produção**, inclusive o [guj.com.br](http://guj.com.br) que utiliza desde sua primeira versão beta publica.
