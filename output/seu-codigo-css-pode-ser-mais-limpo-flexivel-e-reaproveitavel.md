---
title: "Seu código CSS pode ser mais limpo, flexível e reaproveitável"
date: "2013-01-16"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Bons programadores aprendem boas práticas de código desde criancinhas. Encapsulamento, bons nomes de variáveis e métodos, orientação a objetos e outros conceitos clássicos estão na ponta da língua. Apesar disso, vejo muito programador bom fazendo código front-end ruim. Minha opinião? **Fazer um bom código front-end é mais difícil do que parece**.

Escrever um _bom JavaScript_ costuma ser mais fácil. Por ser uma linguagem de programação parecida com as outras, conseguimos mapear bem os conceitos de encapsulamento e orientação a objetos (apesar da dificuldade dos prototypes).

Mas, com HTML e CSS, a coisa começa a ficar mais complicada. Como ter um CSS mais reaproveitável? Algumas boas práticas para devs front-end:

![Heróis](https://blog.caelum.com.br/wp-content/uploads/2013/01/4950445842_daabb0ae20_b_mini.jpg)

## Não sobrescreva suas próprias regras CSS.

Você já passou por isso: coloca um `float:left` num seletor e depois precisa limpá-lo com `float:none` num caso particular. Ou ainda reescrever um `width:auto` e coisas do tipo. **Péssima prática**.

Além de te fazer escrever a mesma propriedade duas vezes, não faz uso dos padrões do navegador (no caso, que todo `float` já é `none`). Evite isso invertendo seus seletores: deixe só os elementos que realmente precisa flutuar com `float:left` e os demais herdam o valor padrão `none` e pronto.

Um cenário que vejo isso acontecer muito é ao adaptar sites pra telas menores com media queries. Na tela grande, você flutua elementos pra todo lado mas num celular pequeno decide empilhar os elementos e tirar o `float`:

\[code language="css"\] /\* desktop \*/ .botao-magico { float: right; }

/\* mobile \*/ @media (max-width: 600px) { .botao-magico { float: none; } } \[/code\]

Nesse caso de media queries, aliás, isso só aconteceu porque usamos a estratégia de escrever os estilos do desktop primeiro e depois sobrescrever com os estilos pra dispositivos menores. Chamam essa estratégia de **desktop-first** e esse exemplo é um ótimo argumento pra você não fazer isso e preferir **mobile-first** no seu CSS:

\[code language="css"\] /\* mobile \*/ /\* nada aqui! o padrão é float:none \*/

/\* desktop \*/ @media (min-width: 600px) { .botao-magico { float: right; } } \[/code\]

## Abuse de classes pra reaproveitar código CSS. Evite IDs.

IDs são muito inflexíveis: só podem aparecer uma vez na página e um elemento não pode ter mais de um ID (o elemento é um _singleton_). Isso faz do ID o vilão do reaproveitamento de código.

Você deveria **usar classes sempre no seu CSS**. Isso te permite aplicar o mesmo estilo em mais de um elemento diferente sem replicar regras. Seu código fica mais curto e mais limpo.

Classes CSS nos permitem o tipo de reaproveitamento que a herança nos traz em linguagens orientadas a objetos. Crie uma hierarquia de classes pra reaproveitar estilos comuns mesmo em elementos diferentes.

Por exemplo: você tem dois elementos na página (`.menu` e `.noticias`) que têm funções e visuais diferentes. Mas os dois seguem a base visual do site que é ter fundo cinza, com borda redonda e uma sombra. Ao invés de copiar as propriedades, crie uma nova classe base pra esses elementos: \[code language="html"\] <div class="box menu">...</div> <div class="box noticias"></div> \[/code\] \[code language="css"\] .box { background: #ccc; border-radius: 5px; box-shadow: 2px 2px 2px black; } \[/code\]

Prefira criar essa classe nova `.box` que escrever o CSS usando as classes dos outros dois elementos `.menu, .noticias { ... }`. O dia que um terceiro box surgir, é só aplicar a classe, sem mexer no CSS.

## Não brigue com a especificidade do CSS.

Falando em classes e sobrescrever regras, a coisa fica pior ainda com as regras de especificidade do CSS. Seletor de ID tem precedência ao de classe, que tem precedência ao de tagname.

Outro motivo pra você evitar IDs no CSS é que você não pode sobrescrever suas propriedades com suas classes genéricas reaproveitáveis. Precisaria de outro ID pra sobrescrever um seletor de ID.

Não brigue com a especificidade. Mantenha a especificidade dos seus seletores no mesmo nível usando classes sempre pra estilizar. São mais fáceis de compor. E jamais use `!important` pra forçar um estilo.

## Números mágicos são um mal sinal no CSS.

Toda vez que você escreve um `margin-top:37px` ou um `width:381px` um bebê foca morre. Evite números mágicos no seu CSS que são **calculados arbitrariamente** e **certamente quebrarão** se o texto for um pouquinho maior ou se o seu menu tiver uma entrada a mais.

Pior ainda aquele número mágico pra alinhar algo (tipo um `top:-1px`) que certamente está levando em conta a renderização num certo browser e vai quebrar em outro navegador ou se o ícone mudar. Isso faz seu CSS ser pouco reaproveitável e exige manutenção constante. Quer alinhar um ícone com texto? Aprenda a usar o `vertical-align:middle` (mesmo ele não funcionando do jeito que você pensa que funciona).

## Use unidades flexíveis.

Números mágicos podem ser evitados em muitos casos usando unidades relativas como porcentagens. Se você tem uma página de `940px` de largura e precisa dividir em 5 colunas, não escreva `width:188px` porque você nunca vai lembrar da onde saiu esse valor. Prefira `width:20%` que mostra de maneira mais óbvia que é 1/5 do página.

E, claro, se puder, faça todas as suas unidades de layout com porcentagens. Isso vai fazer seu design ser flexível e não depender do tamanho do navegador. **A Web é uma mídia elástica e confinar sua página a pixels estáticos é transformar a Web em uma mídia mais limitadas como a impressa**.

Para elementos tipográficos ou afetados pela tipografia, use `em` como medida flexível.

## Evite acoplar o estilo CSS a tags específicas do HTML.

Escrever o nome tag no meio do CSS faz com que seu estilo fique acoplado à estrutura do HTML. Se o HTML muda, seu estilo quebra. Podemos evitar isso criando mais classes e diminuindo o acoplamento entre HTML e CSS.

Se você tem um menu como uma lista cheia de itens, não use `.menu li` no CSS. Prefira criar uma classe específica `.menu-item` e aplicá-la no HTML. Você vai poder mudar seu HTML depois sem problemas.

Até quando você usa elementos mais padronizados, como `<header>` ou `<h1>`, a boa prática é usar classes pra estilizar:

\[code language="html"\] <header class="topo"> <h1 class="chamada-principal">Compre já!</h1> </header> \[/code\]

Tudo isso te deixa muito mais livre pra mudar o HTML por razões de semântica e conteúdo, sem afetar o estilo CSS.

## Dê bons nomes pra suas classes

E já que boa parte das práticas envolve criar novas classes, é bom saber nomeá-las direito. A regra óbvia, quer você já segue, é criar **nomes legíveis e fáceis de entender**. Use `.painel-principal` ao invés de `.pnlPri`.

Mas não é só isso. Os nomes das classes são a ponte entre seu HTML e seu CSS. Eles serão escritos no meio do conteúdo HTML da página e, portanto, **devem ter semântica de conteúdo e não de visual**.

Isso quer dizer que você não deveria criar classes chamadas `.box-lateral` ou `.titulo-azul` ou `.painel-direita`. Se o seu box não for lateral ou o título mudar de cor ou o painel mudar de lado, sua classe perde significado.

Evite isso criando classes com nomes de conteúdo, como `.painel` ou `.chamada-principal`. E, claro, se, eventualmente, você tiver um `.titulo-azul` no seu código, não deixe a classe com esse nome o dia que a cor mudar pra vermelho; renomeie a classe!

## Documente bem as exceções.

Toda regra tem exceções. Às vezes, você vai colocar um ID ou tagname no seu CSS. Ou vai precisar de um `!important` num raro lugar. Ou aquele número mágico que você não vai lembrar depois como calcula.

Isso deve ser exceção, não abuse. E, sempre que fizer isso, documente direito no CSS o porquê daquela escolha.

\[code language="css"\] /\* estrutura esperada para o menu: <ul class="menu"> <li><a href=""></a> <li><a href=""></a> </ul> \*/ .menu { ... } .menu li { ... } .menu a { ... }

/\* usando ID porque o widget do facebook obriga \*/ #facebook-like iframe { width: 100% !important; /\* important pra sobrescrever css inline do widget \*/ } \[/code\]

## Mais

Há muitas outras boas práticas, claro. Você deve identar bem seu código, ordenar as propriedades de maneira lógica e se preocupar com [performance](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/). Você pode usar [pré-processadores](https://blog.caelum.com.br/css-facil-flexivel-e-dinamico-com-less/), frameworks e técnicas avançadas (como OOCSS). No [curso de front-end da Caelum](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/), falamos de várias dessas técnicas e com muitos exemplos práticos.

O importante é ter um cuidado especial pelo seu CSS para deixá-lo sempre o mais fácil, reaproveitável e flexível o possível. Isso vai ajudar o seu dia a dia e dos outros devs da sua equipe.
