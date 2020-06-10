---
title: "Diminua suas dependências com os eventos do CDI"
date: "2012-12-11"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Já falamos de CDI aqui no blog da Caelum, tanto numa introdução pra você [começar a usar o CDI](https://blog.caelum.com.br/use-cdi-no-seu-proximo-projeto-java/) quanto em [tópicos mais avançados](https://blog.caelum.com.br/customizando-a-producao-de-dependencias-no-cdi/). E recentemente até abordei o tema em uma [palestra sobre CDI](http://www.slideshare.net/caelumdev/porque-voc-deveria-usar-cdi-nos-seus-projetos-java-javaone-la-2012-srgio-lopes) no JavaOne, além de sempre falarmos dele no nosso [curso de JSF e CDI](http://www.caelum.com.br/curso/fj-26-java-web-jsf2-cdi/).

Fato é que o CDI tem muitas vantagens e, por isso, **recomendo que você use no seu próximo projeto Java**. É uma solução completa de injeção e gerenciamento de dependências, é uma especificação oficial, é muito fácil e produtivo de usar, e tem muitos recursos interessantes.

Esse post trata de um recurso avançado do CDI pra **diminuir bastante o acoplamento dos seus objetos**. Injeção de dependências por si só já é uma baita diminuição de acoplamento: a gente passa a receber os objetos magicamente sem nos preocuparmos com sua criação ou seu ciclo de vida. Mas, se pensar bem, ainda estamos bem acoplados com a dependência que recebemos.

### Alto acoplamento

Uma classe `FinalizaCompra`, por exemplo, que recebe uma dependência injetada do tipo `SistemaPagamento`, está acoplada com esse objeto. Se o processo de finalizar uma compra for um pouco mais elaborado, é fácil ver que várias dependências, mesmo injetadas, geram uma grande complexidade:

\[java\] public class FinalizaCompra {

@Inject private SistemaPagamento pagamento; @Inject private Estoque estoque; @Inject private Notificacoes notificacoes; public void finalizaCompra() { pagamento.efetuaCobranca(); estoque.reservaMercadoria(); notificacoes.notificaCompra(); } } \[/java\]

Nesse exemplo, criei um processo que finaliza a compra de um usuário na loja virtual. Esse processo exige a execução de diversas tarefas diferentes e não relacionadas, e a classe `FinalizaCompra` foi encarregada de coordenar tudo.

Usamos `@Inject` pro CDI injetar os 3 objetos, o que já ajudou a flexibilizar nosso código. Mas a classe `FinalizaCompra` **tem dependências demais**. Pior, se o processo mudar e exigir mais um passo, teremos que editá-la e acrescentar mais uma dependência. Essa classe tem a difícil responsabilidade de coordenar vários objetos bem diferentes.

E se conseguíssemos flexibilizar nosso design para que a classe `FinalizaCompra` ficasse independente das etapas individuais do processo? Adicionar novas etapas não exigiria mudanças na `FinalizaCompra` e ela **não teria mais aquele acoplamento com tantas dependências diferentes**.

Conseguimos isso com os **eventos do CDI**.

### O evento

A API de eventos do CDI nos permite implementar um mecanismo onde um componente do sistema dispara um aviso quando certo acontecimento ocorrer, e depois outros componentes podem escutar esse aviso e executar suas respectivas atividades. O **evento** do qual falamos, é esse acontecimento que será avisado pra todo o sistema.

No nosso exemplo do processo de finalização de compra, o evento em si, o acontecimento que ocorre naquele instante, é simplesmente o fato do **usuário ter feito uma compra**. A compra é nosso evento. E o primeiro passo é criar uma classe simples pra representar esse evento, uma classe `Compra`.

A ideia é, então, criar um objeto `Compra` toda vez que a compra for efetuada. No caso, dentro da nossa classe `FinalizaCompra` de antes:

\[java\] public class FinalizaCompra { public void finalizaCompra() { Compra compra = new Compra(produto, cliente); } } \[/java\]

Essa classe `Compra` seria bem simples; no exemplo, fiz com que recebesse o produto da compra e qual cliente estava recebendo, apenas pra ilustrar.

### Disparando eventos no CDI

Próximo passo: usar o CDI pra anunciar pra todo mundo que uma compra foi efetuada naquele instante na classe `FinalizaCompra`. Usamos a classe `Event` que é parametrizada com o tipo do evento que estamos lidando. Ficaria então um simples `Event<Compra>`. E, como tudo no CDI, vamos injetar esse objeto pra não precisarmos criá-lo:

\[java\] @Inject @Any Event<Compra> eventoCompra; \[/java\]

Esse objeto tem uma utilidade principal: disparar o evento no momento certo e anunciar o objeto do evento pra todo mundo que estiver escutando. É uma simples chamada:

\[java\] eventoCompra.fire(compra); \[/java\]

Pra ficar mais claro, vamos juntar tudo na nossa classe `FinalizaCompra`, agora terminada:

\[java\] public class FinalizaCompra { @Inject @Any private Event<Compra> eventoCompra; public void finalizaCompra() { Compra compra = new Compra(produto, cliente); eventoCompra.fire(compra); } } \[/java\]

Nesse ponto, o que temos é nossa classe anunciando pra todo lado que o evento de Compra acontece quando alguém chega no método `finalizaCompra`.

### Escutando eventos no CDI

Bom, mas a parte difícil era que, ao acontecer uma compra, precisávamos disparar 3 processos: o pagamento, a reserva da mercadoria no estoque, e uma notificação por email pro usuário. Antes, essas 3 chamadas ficavam dentro da `FinalizaCompra`; agora, vamos inverter isso.

A `FinalizaCompra` apenas avisa que o evento de `Compra` aconteceu. E cada uma das 3 classes (`Pagamento`, `Estoque` e `Notificacoes`) fica responsável por **observar se o evento de compra aconteceu ou não**.

Com CDI, isso é muito fácil. Basta cada uma das classes receber o objeto do evento como argumento e usar a anotação `@Observes`. A classe `Estoque`, por exemplo, ficaria:

\[java\] public class Estoque { public void reservaMercadoria(@Observes Compra compra) { // ... reserva produto no estoque ... } } \[/java\]

O papel do CDI aqui é coletar o evento disparado na nossa classe `FinalizaCompra` e disparar todos os métodos marcados com `@Observes` recebendo aquele evento específico.

Podemos implementar o `@Observes` em quantas classes quisermos (no nosso caso, seriam as 3 citadas) e o CDI vai disparar todas. Precisa de mais um participante no processo? Sem problemas, só colocar `@Observes` nele e nada mais precisa ser mudado no sistema.

### Acoplamento praticamente zero

**Repare como o processo fica completamente desacoplado**. A classe `FinalizaCompra` nem conhece mais quais são as etapas do processamento de uma `Compra`. Pra ela, podem ser 3 passos ou mil passos, não importa. Seu trabalho é simplesmente disparar o evento na hora certa.

Conseguimos **flexibilidade e simplicidade** importantes em qualquer sistema um pouco mais complexo. **Separamos bem as responsabilidades**, e diminuímos o papel da `FinalizaCompra` que antes era de centralizar toda a coordenação do processo de compra.

E esse mecanismo do CDI é, na verdade, uma implementação bastante flexível e fácil de usar de um design pattern famoso chamado **Observer** que está descrito no GoF. A gente fala mais desse pattern (e outros) no [curso online de Design Patterns](http://www.caelum.com.br/curso/online/design-patterns/) da Caelum.

Se você gosta do CDI, pode se interessar também pelos slides da palestra do JavaOne que mostram esse mecanismo de eventos e ainda outras ideias bacanas:

<iframe src="http://www.slideshare.net/slideshow/embed_code/15504107" width="427" height="356" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC;border-width:1px 1px 0;margin-bottom:5px" allowfullscreen webkitallowfullscreen="" mozallowfullscreen=""></iframe>
