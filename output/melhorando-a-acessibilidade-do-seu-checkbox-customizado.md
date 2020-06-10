---
title: "Melhorando a acessibilidade do seu checkbox customizado"
date: "2018-05-17"
author: "natan.souza"
authorEmail: "natan.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma coisa bacana que designer adora fazer: customizar componentes de formulários. Sempre para reforçar a identidade visual do sistema e deixar o visual mais uniforme. E quem já precisou fazer o código de um _checkbox_ customizado sabe o quão sofrido é, mas já viu se ele é acessível?

Assumindo que temos que fazer o checkbox da imagem abaixo e devemos usar uma `<div>`. No mundo real o mais eficiente seria usar um `<input type=”checkbox”>` propriamente dito.

Vejamos o exemplo abaixo:

![Checkbox fake](http://blog.caelum.com.br/wp-content/uploads/2018/04/checkbox-fake.jpg)

[Você poder ver o código dele aqui nesse Codepen.](https://codepen.io/designernatan/pen/eMKGpQ)

Agora vamos ver como um usuário de leitor de tela ouve esse lindo checkbox:

![Exibidor de fala do NVDA tentando ler o checkbox](http://blog.caelum.com.br/wp-content/uploads/2018/04/checkbox-ruim-no-screen-reader.png)

Como a pessoa vai saber que isso é um checkbox se ele não fala isso para o usuário? Como fazer isso se você está obrigado a usar uma `<div>`?

E como fazer com que o leitor de tela entenda que isso é um checkbox e não um texto perdido?

No nosso caso, a `<div>` precisa virar um checkbox. Vamos então mudar sua função em nosso HTML, alterando seu papel no nosso código apenas colocando o atributo `role=checkbox`. Vendo no exibidor de fala do NVDA:

![Div com role checkbox](http://blog.caelum.com.br/wp-content/uploads/2018/04/checkbox-com-role.jpg)

Agora sim ele fala que se trata de uma caixa de seleção, mas ainda não fala se já está marcado ou não...

## Está marcado ou não?

Outro problema que podemos notar é que o usuário não vai saber se o checkbox está marcado ou desmarcado. Por padrão ele deve vir marcado ([_bad UX_?](https://darkpatterns.org)), colocar o atributo `checked` faria sentido em um elemento de formulário. Nessa situação precisaremos usar os ARIAs, outra parte da especificação da WAI ARIA que envolve estados: o atributo `aria-checked`. Isso para informar que sim, essa caixa de seleção está “checkada”:

```

 <div class="contato-newsletter-check" role="checkbox" aria-checked="true">
    X
 </div>
```

E como o usuário vai saber que o elemento é focável?

## Tendo foco

Se a pessoa estiver navegando via teclado, como ter certeza que está com o foco nesse checkbox? Por isso que elementos de formulário precisam passar o feedback que estão ou não com o foco ativo.

Para resolvermos isso no nosso código precisamos dizer que ele é focável usando o atributo que muda a sequência natural de foco do HTML, o atributo `tabindex`.

No caso precisamos mudar a ordem dessa sequência natural ou simplesmente falar que ele é focável? Só focável! Para isso colocamos seu valor em “0”:

```

 <div class="contato-newsletter-check" role="checkbox" aria-checked="true">
    X
 </div>
```

E da mesma forma que colocamos o atributo `for` em um elemento `<label>` também devemos fazê-lo aqui, afinal a `<div>` agora tem alma de checkbox. Isso para que o usuário, que pode não ter a mesma destreza com ponteiro do mouse que nós, consiga acertar aquela pequena caixinha azul.

Vamos fazer isso substituindo a `<div>`, colocando o atributo `for` e o ID em nosso checkbox:

```

<div class="contato-containerCampo contato-containerCampo--full">
  <div class="contato-newsletter-check" role="checkbox" aria-checked="true" tabindex="0" id="spam">
    X
  </div>
  <label class="contato-newsletter-label fm-label for-checkbox" for="spam">
    Quero receber a Newsletter da Caelum com notícias técnicas. <strong>Sem spam!</strong>
  </label>
</div>
```

Aí entraria em mexer nos atributos via JavaScript e dar um jeito nesse "X" sendo lido ali, mas isso fica para outro post. Para adiantar você pode ver um [chekbox acessível aqui.](https://codepen.io/designernatan/pen/XEBXda/)

Se quiser saber mais sobre checkbox e acessibilidade, recomendo dar uma olhada no [MDN sobre como usar a role checkbox.](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques/Using_the_checkbox_role)

## “Natan, por que não usou o checkbox logo”?

A ideia desse post foi mostrar como mudar a semântica de um elemento sem precisar alterá-lo de fato. Perceba que pequenas atitudes podem fazer a diferença na hora que nosso usuário vai usar nossos projetos.

Idealmente devemos usar o elemento específico para a situação específica. No caso de algo para marcar e desmarcar, já existe o checkbox, então deveríamos usá-lo!

Outro exemplo! Atualmente não temos um elemento específico para _dropdowns_, com o `role=dropdown` e mais alguns ajustes, uma `<div>` pode se transformar nisso, como o [_dropdown_ do Bootstrap.](https://getbootstrap.com/docs/4.0/components/dropdowns/)

Confira abaixo, outro exemplo de um checkbox acessível, agora usando o _input_ checkbox:

<p class="codepen" data-height="292" data-theme-id="0" data-slug-hash="XEBXda" data-default-tab="css,result" data-user="designernatan" data-embed-version="2" data-pen-title="Checkbox true">See the Pen <a href="https://codepen.io/designernatan/pen/XEBXda/">Checkbox true</a> by Natan Souza (<a href="https://codepen.io/designernatan">@designernatan</a>) on <a href="https://codepen.io">CodePen</a>.</p>

<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Espero que esses posts de acessibilidade estejam sendo úteis para você e se quiser se aprofundar e fazer um projeto acessível na prática recomendo dar uma olhada na [carreira sobre acessibilidade na Alura.](https://www.alura.com.br/carreira-acessibilidade-web)
