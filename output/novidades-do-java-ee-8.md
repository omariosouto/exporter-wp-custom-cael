---
title: "CDI, MVC e outras novidades do Java EE 8"
date: "2015-07-23"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[Estamos acompanhando de perto](https://blog.caelum.com.br/primeiros-passos-do-mvc-1-0/) as novidades do Java EE 8, e recentemente excelentes atualizações foram divulgadas. O lançamento do primeiro [draft do CDI 2.0](http://cdi-spec.org/news/2015/07/03/CDI-2_0-EDR1-released/) é uma delas, seguida de novidades no status do MVC 1.0, JSON-P, JMS 2.1, entre outras. Aqui vão algumas delas:

### CDI 2.0: Melhorias na API de Eventos e suporte para Java SE

Nessa nova versão, a API de eventos ganhou diversas melhorias, como uso assíncrono e ordenação -- duas features bastante solicitadas pela comunidade. Na prática, o exemplo que utilizamos [nesse post sobre eventos do CDI](https://blog.caelum.com.br/diminua-suas-dependencias-com-os-eventos-do-cdi/) poderia ficar assim:

\[code language="java" highlight="7"\] public class FinalizaCompra {

    @Inject private Event<Compra> eventoCompra;

public void finalizaCompra() {        Compra compra = new Compra(produto, cliente);        eventoCompra.fireAsync(compra);     } } \[/code\]

A classe `FinalizaCompra` notifica assíncronamente que o evento aconteceu, enquanto outras classes, como a `Estoque`, poderão observar esse evento -- também assíncronamente.

\[code language="java" highlight="3"\] public class Estoque {

    public void reservaMercadoria(@ObservesAsync Compra compra) { // reserva produto no estoque     } } \[/code\]

Legal, não é? Outro ponto é que se estivermos trabalhando de forma síncrona, e por alguma necessídade de regra de negócio precisarmos que a classe `Notificacoes` seja a ultima a observar o evento de compra, podemos priorizar toda ordem de execução:

\[code language="java"\] public class Estoque {

    public void reservaMercadoria(@Observes @Priority(1) Compra compra) { // reserva produto no estoque     } }

public class Notificacoes {

    public void enviaNotificacao(@Observes @Priority(2) Compra compra) { // envia email, sms, notificando a campra     } } \[/code\]

Se quiser, você pode ver mais detalhes sobre o [funcionamento assíncrono](https://docs.jboss.org/cdi/spec/2.0.EDR1/cdi-spec.html#firing_events_asynchronously) e [ordenação de observers](https://docs.jboss.org/cdi/spec/2.0.EDR1/cdi-spec.html#observer_ordering) no capítulo de eventos da nova versão da spec. Muitas dessas novidades serão aplicadas e suportadas no VRaptor 4, que tem sua nova versão totalmente integrada ao CDI. Vale comentar que [todo o fluxo de uma request do VRaptor é tratado com eventos do CDI!](http://www.vraptor.org/pt/docs/eventos/#trabalhando-com-o-recurso-de-eventos-do-cdi)

Por fim, seria injusto falar do CDI 2.0 sem falar do suporte ao Java SE. Utilizando a classe `CDI`, que já conhecemos [nesse outro post do CDI 1.0](https://blog.caelum.com.br/4-recursos-novos-do-cdi-1-1/), juntamente com a iterface `CDIProvider`, agora podemos fazer:

\[code language="java"\] public static void main(String... args) { try(CDI<Object> cdi = CDI.getCDIProvider().initialize()) { // inicia o container do CDI Estoque estoque = cdi.select(Estoque.class).get(); estoque.reservaMercadoria(new Compra("Livro da CDC")); } // container stopado automaticamente depois do bloco do try } \[/code\]

### MVC 1.0: Novos recursos e melhorias na API de Validação

O MVC também recebeu novos recursos desde [nosso último post](https://blog.caelum.com.br/primeiros-passos-do-mvc-1-0/), em especial um mecanismo de proteção [CSRF](https://pt.wikipedia.org/wiki/Cross-site_request_forgery) (_Cross-Site Request Forgery_). Entre outras variações, você pode ativar o recurso globalmente pela sua classe `Application`.

\[code highlight="14" language="java"\] @ApplicationPath("mvc") public class MvcApplication extends Application {

@Override public Set<Class<?>> getClasses() { final Set<Class<?>> set = new HashSet<>(); set.add(ProdutoController.class); return set; }

@Override public Map<String, Object> getProperties() { final Map<String, Object> map = new HashMap<>(); map.put(Csrf.ENABLE\_CSRF, Boolean.TRUE); return map; } } \[/code\]

Feito isso, todas as requisições de POST para o `ProdutoController` serão protegidas, com controle de um token que deve ser enviado pelo formulário da view:

\[code highlight="3" language="html"\] <form action="/produtos/adiciona" method="post"> <input type="submit" value="Click here"/> <input type="hidden" name="${csrf.name}" value="${csrf.token}"/> </form> \[/code\]

A API de validação também tem sido o foco das mudanças mais recentes. Muito ainda está sendo discutido, mas por enquanto, você já pode imaginar o método `adiciona` sendo validado assim:

\[code highlight="4,9" language="java"\] @Controller @Path("/produtos") public class ProdutoController {

@Inject private BindingResult result;

@POST @ValidateOnExecution(type = ExecutableType.NONE) public Response adiciona(@Valid @BeanParam Produto produto) { if (result.isFailed()) { // mostra mensagens de validação, redireciona, etc } // adiciona produto no banco, etc etc } } \[/code\]

### E muito mais novidades!

Essas são apenas algumas das muitas novidades e atualizações que nos esperam. O Java EE está previsto só pro final do ano que vem, próximo ao JavaOne San Francisco 2016, mas desde já você pode e deve contribuir com testes, idéias, reports de bugs entre outros. O [primeiro draft do CDI 2.0](http://cdi-spec.org/news/2015/07/03/CDI-2_0-EDR1-released/), assim [como do MVC 1.0](https://ozark.java.net/download.html), e diversas outras especificações já estão disponíveis para download. E você, que outras novidades espera no Java EE 8? Já tem testado alguma delas?
