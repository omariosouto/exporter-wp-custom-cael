---
title: "O drama do posicionamento de elementos na tela e o Flexbox"
date: "2013-07-24"
author: "luiz.real"
authorEmail: "luiz.real@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O CSS é uma ferramenta que revolucionou o webdesign. No início da Web, as páginas eram todas desenhadas usando tabelas no código HTML, o que era doloroso de escrever e manter. Com a popularização do CSS, houve uma separação entre conteúdo e apresentação: o conteúdo fica no HTML, a apresentação no CSS. Assim, ambas as partes ficaram mais fáceis de manter, além de inúmeros outros benefícios que comentamos ao longo do [nosso curso de desenvolvimento Web](http://www.caelum.com.br/curso-html-css-javascript/).

Com CSS, podemos escrever o HTML pensando apenas na informação que queremos apresentar, **sem nos preocuparmos com seu visual final**, isso graças ao poder que o CSS nos dá para modificar o visual de qualquer elemento da página.

No entanto, alguns layouts ainda são bastante desafiadores de serem feitos apenas com CSS. Considere o exemplo abaixo:

\[caption id="attachment\_5656" align="aligncenter" width="300"\][![Rodapé complicado de fazer com CSS](https://blog.caelum.com.br/wp-content/uploads/2013/07/rodape-mf-300x21.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/rodape-mf.png) Rodapé complicado de fazer com CSS\[/caption\]

Parece simples, não? No entanto, como fazer os ícones das redes sociais ficarem à direita e centralizados verticalmente com o logotipo à esquerda? Algumas possibilidades são usar a propriedade `position`, a propriedade `float` ou a propriedade `display` mas, em todos os casos, teremos que usar algum número mágico. Considere que nosso HTML está como abaixo:

\[code language="html"\] <footer> <div class="container"> <h1>Mirror Fashion</h1> <ul class="social"> <li><a href="#">Facebook</a></li> <li><a href="#">Twitter</a></li> <li><a href="#">Google+</a></li> </ul> </div> </footer> \[/code\]

A `div` com a classe `container` ajuda a centralizar o conteúdo no interior do rodapé. Veja como poderíamos posicionar os ícones de rede social, da lista com a classe `social`, usando a propriedade `position`:

\[code language="css"\] .container { position: relative; }

.social { position: absolute; top: 11px; /\* 11px!? Por quê!? \*/ right: 0; } \[/code\]

Repare que colocamos a declaração `top: 11px` para deixar os ícones centralizados verticalmente com o logotipo. Mas de onde vêm esses 11px? Podemos chegar nesse valor fazendo algumas contas, medindo a diferença de altura entre o logotipo na esquerda (54px) e os ícones (32px). Podemos, inclusive, deixar isso claro no código CSS, o que é considerado uma boa prática:

\[code language="css"\] .social { position: absolute; top: 11px; /\* 11px = (logo (54px) - icones (32px)) / 2 \*/ right: 0; } \[/code\]

No entanto, esse número continua sendo meio **mágico**, não? E o que acontece se resolvermos trocar o logotipo ou os ícones por imagens de tamanhos diferentes? Teremos que mexer no CSS, não? Isso é sinal de que nosso layout não está muito flexível, aumentando o seu custo de manutenção.

É para resolver esses e outros casos que está surgindo um novo conjunto de regras para o CSS conhecido como módulo de regras de layout **Flexbox**. O principal objetivo dessa nova especificação é facilitar o controle de espaçamentos e tamanhos de elementos da página **passando mais controle dos elementos filhos para o elemento pai**. Assim, as declarações passam a valer independente de quantos e quais elementos filhos um determinado elemento container tem.

Vejamos um exemplo simples de como essa nova especificação pode nos ajudar. Considere o layout abaixo:

\[caption id="attachment\_5657" align="aligncenter" width="300"\][![Três caixas lado a lado num container](https://blog.caelum.com.br/wp-content/uploads/2013/07/tres-caixas-300x30.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/tres-caixas.png) Três caixas lado a lado num container\[/caption\]

Para fazer as três caixas ficarem lado a lado dentro do container, podemos, simplificando um pouco, fazer

\[code language="css"\] .caixa { width: 33%; float: left; } \[/code\]

No entanto, a interação do `width` com as propriedades `padding` e `border` pode complicar nossas vidas na hora de fazer as três caixas sempre caberem na mesma linha da página. Além disso, o que acontece se decidirmos que teremos que colocar quatro caixas em vez de três apenas? Teremos que, novamente, **mexer no CSS**. Com a nova especificação flexbox fica muito mais simples! Basta indicar que o container é um _container flex_:

\[code language="css"\] .container { display: flex; } \[/code\]

E, agora, indicar que as caixas dentro desse container devem ocupar igualmente o espaço (daí o valor 1 para todas elas):

\[code language="css"\] .caixa { flex: 1; } \[/code\]

E pronto! Veja que, em nenhum momento, precisamos fazer alguma conta ou nos preocuparmos com as dimensões de cada uma das caixas. O próprio navegador vai cuidar disso para nós!

Neste exemplo, tínhamos um número fixo de caixas: três. No entanto, nem sempre é assim. Em alguns cenários, podemos ter um número de caixas dentro de um container variável. Poderíamos ter cinco, seis caixas. Neste caso, fica bem complicado fazer todas essas caixas permanecerem lado a lado dentro do container usando a propriedade `width`, não? Pois com flexbox não poderia ser mais simples:

\[code language="css"\] .container { display: flex; } .caixa { flex: 1; } \[/code\]

Exatamente o mesmo CSS de antes! Repare que a declaração `flex: 1` faz com que o navegador distribua o espaço do container entre as caixas **independente do número de caixas!**

Agora, e se quisermos que a distribuição de espaço não seja exatamente igual entre as caixas? Poderíamos querer que a primeira caixa fosse maior que as outras, por exemplo. Sem problemas!

\[code language="css"\] .caixa { flex: 1; } .caixa:first-child { flex: 2; } \[/code\]

Desta forma, dizemos ao navegador que a primeira caixa deve receber duas vezes mais espaço que as outras, novamente sem importar quantas sejam as outras.

Voltando ao nosso problema original: queríamos deixar os ícones das redes sociais à direita e centralizados verticalmente com o logotipo no nosso rodapé. Num container _flex_, além de poder controlar o tamanho dos filhos de um container, podemos controlar a **distribuição de espaço** entre eles e o **alinhamento** deles com relação ao container.

Neste caso do rodapé, queremos que os elementos fiquem centralizados verticalmente no rodapé. Então basta declararmos

\[code language="css"\] .container { display: flex; align-items: center; } \[/code\]

Com isso já chegamos no seguinte resultado:

\[caption id="attachment\_5660" align="aligncenter" width="300"\][![Footer com display: flex e align-items: center ativados](https://blog.caelum.com.br/wp-content/uploads/2013/07/footer-align-items-300x21.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/footer-align-items.png) Footer com display: flex e align-items: center ativados\[/caption\]

Agora, para fazer os ícones das redes sociais ficarem à direita, podemos mudar a forma como o container _flex_ distribui o espaço que sobra entre seus filhos. Como não declaramos a propriedade `flex` nem no logotipo nem nos ícones, eles permanecem com o tamanho original. Assim, sobra bastante espaço livre no rodapé. Esse espaço sobrando, por padrão, fica após os elementos, mas podemos mudar essa distribuição com a propriedade `justify-content` de um container _flex_. No nosso caso, queremos que o espaço fique todo **entre** os dois elementos, então basta declararmos isso

\[code language="css"\] .container { display: flex; align-items: center; justify-content: space-between; } \[/code\]

E pronto! Com apenas três declarações, chegamos ao resultado desejado **sem usar números mágicos** no CSS.

\[caption id="attachment\_5656" align="aligncenter" width="300"\][![Rodapé complicado de fazer com CSS](https://blog.caelum.com.br/wp-content/uploads/2013/07/rodape-mf-300x21.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/rodape-mf.png) Com a especificação Flexbox ficou fácil!\[/caption\]

Os dois exemplos dados lidam com dimensões e espaços no eixo horizontal da página, mas também é possível usar um container _flex_ para controlar os espaços e tamanhos no eixo vertical da página. Considere o seguinte layout, bastante usado por diversos sites:

\[caption id="attachment\_5661" align="aligncenter" width="300"\][![Layout simples, com cabeçalho, corpo e rodapé](https://blog.caelum.com.br/wp-content/uploads/2013/07/cabecalho-corpo-rodape-300x142.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/cabecalho-corpo-rodape.png) Layout simples, com cabeçalho, corpo e rodapé\[/caption\]

O código HTML dessa página seria algo como

\[code language="html"\] <header> <h1>Cabeçalho</h1> </header> <main> <h1>Corpo</h1> <p>Algum texto aqui</p> </main> <footer> <h1>Rodapé</h1> </footer> \[/code\]

Neste layout, o rodapé deve **sempre** aparecer no final da tela, independente do tamanho do texto. Uma forma de fazer isso é **transformar nossa página inteira num container flex**:

\[code language="css"\] body { display: flex; } \[/code\]

Além disso, é necessário deixar nossa página ocupando sempre a tela toda:

\[code language="css"\] html, body { height: 100%; } \[/code\]

Fazendo isso, já temos quase o que queremos. Falta apenas dizer para o body que os filhos dele devem ser dispostos um em cima do outro, e não um ao lado do outro, como é o padrão com um container _flex_:

\[code language="css"\] body { flex-direction: column; } \[/code\]

Com essas declarações, voltamos praticamente ao ponto inicial:

\[caption id="attachment\_5662" align="aligncenter" width="300"\][![Layout simples (cabeçalho, corpo e rodapé) com espaço sobrando após o rodapé](https://blog.caelum.com.br/wp-content/uploads/2013/07/cabecalho-corpo-rodape-espaco-sobrando-300x142.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/cabecalho-corpo-rodape-espaco-sobrando.png) Layout simples (cabeçalho, corpo e rodapé) com espaço sobrando após o rodapé\[/caption\]

No entanto, agora basta declararmos que o corpo do site deve ocupar todo o espaço sobrando no container _flex_:

\[code language="css"\] main { flex: 1; } \[/code\]

E pronto! Como não declaramos a propriedade `flex` nem para o cabeçalho nem para o rodapé, eles permanecem com o tamanho original. Resta então ao corpo da página crescer para ocupar o espaço completo da página.

Um outro problema que a especificação Flexbox ataca é a ordem dos elementos em uma página. Antes dela, a ordem em que os elementos aparecem numa página HTML é basicamente determinada pela ordem em que eles aparecem no código fonte da página. Isso pode ser um fator limitante, principalmente considerando layouts responsivos em que, muitas vezes, queremos mostrar elementos da página em ordens diferentes no desktop e nos dispositivos móveis. Considere o exemplo abaixo:

\[caption id="attachment\_5664" align="aligncenter" width="300"\][![Página de vendas de um serviço com três opções de pacotes e preços](https://blog.caelum.com.br/wp-content/uploads/2013/07/pacotes-desktop-300x140.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/pacotes-desktop.png) Página de vendas de um serviço com três opções de pacotes e preços\[/caption\]

No desktop, é interessante mostrar o pacote mais relevante no **meio** da tela. Entretanto, nos tablets e celulares, os três pacotes lado a lado não caberiam muito bem. Uma alternativa, então, é deixar o pacote mais interessante **acima** dos outros, como nas figuras abaixo.

\[caption id="attachment\_5665" align="aligncenter" width="137"\][![Versão mobile da página de três serviços](https://blog.caelum.com.br/wp-content/uploads/2013/07/pacotes-mobile-137x300.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/pacotes-mobile.png) Versão mobile da página de três serviços\[/caption\]

\[caption id="attachment\_5666" align="aligncenter" width="241"\][![Versão tablet da página de três serviços](https://blog.caelum.com.br/wp-content/uploads/2013/07/pacotes-tablet-241x300.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/pacotes-tablet.png) Versão tablet da página de três serviços\[/caption\]

O código HTML para essa parte da página, simplificado, poderia ser algo como

\[code language="html"\] <ol class="pacotes"> <li class="pacote pacote-principal">...</li> <li class="pacote pacote-menor">...</li> <li class="pacote pacote-maior">...</li> </ol> \[/code\]

Deixamos o pacote mais relevante em primeiro no HTML. Dessa forma, para os layouts mobile e tablet, não há muito segredo. Mas e o desktop?

Transformando a lista de pacotes num container _flex_, podemos usar a propriedade `order` nos elementos filhos para reordená-los como quisermos dentro do container:

\[code language="css"\] .pacote-principal { order: 2; } .pacote-menor { order: 1; } .pacote-maior { order: 3; } \[/code\]

Essa mesma técnica também pode ser bastante interessante para tornar nosso site mais acessível para leitores de tela. Podemos, por exemplo, colocar um menu de navegação no início do HTML para os leitores de tela, mesmo se ele aparecer depois no layout!

Por esses exemplos, é possível notar que a especificação Flexbox é bastante poderosa. No entanto, é importante ressaltar que ela não é feita para resolver **todos** os problemas de layout. Em muitos casos, usar as propriedades mais antigas `position` e `float` já basta e resulta num layout que funciona em mais navegadores.

Vale notar que a especificação Flexbox já é recomendação candidata pela W3C, ou seja, os navegadores já devem começar a implementá-la. O suporte dos navegadores já é razoável: tirando o Internet Explorer, que passou a suportar a especificação apenas na versão 10, e o Opera, que passou a suportá-la apenas na versão 12.1, todos os outros já a suportam há bastante tempo. Alguns ainda implementam uma sintaxe antiga da especificação, em que se usava `display: box` em vez de `display: flex`.

Os exemplos usados neste post estão online. Veja o código fonte no [Github](http://github.com/luiz/flexbox-demo) e teste no seu navegador! Conheça mais nos nossos [cursos de front-end](http://www.caelum.com.br/cursos-web-front-end/).
