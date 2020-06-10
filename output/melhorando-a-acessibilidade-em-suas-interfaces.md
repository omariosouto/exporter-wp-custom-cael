---
title: "5 dicas para melhorar a acessibilidade em sua interface"
date: "2017-11-06"
author: "natan.souza"
authorEmail: "natan.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Será que acessibilidade é algo tão complicado que vale a pena eu abrir mão de 25% dos meus usuários? **Acessibilidade** normalmente é um tema que não é muito abordado por aí, seja por falta de informação ou de interesse. Confira cinco dicas para melhorar a acessibilidade do seu projeto.

Tanto no [curso de UX](https://www.caelum.com.br/curso-ux-usabilidade-mobile-web), quanto nos [cursos de Front-end](https://www.caelum.com.br/formacao-frontend) da Caelum sempre levantamos pontos conceituais e técnicos deste tema tão importante. Muitos alunos vão para o curso de UX querendo melhorar a usabilidade do site de sua empresa, mas usabilidade está totalmente amarrada à acessibilidade:

<blockquote class="twitter-tweet" data-lang="pt"><p dir="ltr" lang="pt">Se pecares em acessibilidade, pecarás em usabilidade.</p>— Natan Souza (@designernatan) <a href="https://twitter.com/designernatan/status/903236543694983168">31 de agosto de 2017</a></blockquote>

<script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

E por favor, não me venha com essa de "não é meu público-alvo" ou "não temos tempo". Saiba que além de ser [lei](http://www12.senado.leg.br/noticias/materias/2016/01/21/lei-brasileira-de-inclusao-entra-em-vigor-e-beneficia-45-milhoes-de-brasileiros), cerca de [1/4 da população brasileira tem algum tipo de deficiência.](http://exame.abril.com.br/brasil/ibge-24-da-populacao-tem-algum-tipo-de-deficiencia/) Olá querido amigo-leitor com miopia!

\[caption id="attachment\_9284" align="aligncenter" width="320"\]![Fala com a minha mão](https://blog.caelum.com.br/wp-content/uploads/2042/08/fala-com-a-minha-mao.gif) Não é meu público-alvo blá blá blá....\[/caption\]

Agora vamos te ajudar a deixar sua **interface mais acessível** com algumas dicas/macetes.

## 1 - Não depender da cor para passar informação

Você é o mais novo desenvolvedor em uma empresa de gestão horizontal. Aí chega na hora de ver a prioridade de cada tarefa no Trello pelas etiquetas e você se depara com isso:

![Etiquetas do Trello simulando daltonismo](https://blog.caelum.com.br/wp-content/uploads/2042/08/trello-with-color-blindness.jpg)

 

Entre elas temos categorias como "feito", "melhorias" e "bug que pode custar seu emprego". Qual você atacaria primeiro? A vermelha claro... Percebeu como é perigoso depender da cor para passar alguma informação?

O que poderia ser feito é colocar uma legenda ou mesmo uma textura para diferenciar as etiquetas entre si, tarefa que o pessoal do Trello fez com louvor:

![Trello labels with texture](https://blog.caelum.com.br/wp-content/uploads/2042/08/trello-labels-with-texture.jpg)

Pra fazer um teste de como ficaria aos olhos de um daltônico, normalmente vou no Photoshop e em "View > Proof Setup > Color Blindness". Mas existem [várias extensões para o Chrome](https://chrome.google.com/webstore/search/color%20blind) que ajudam nisso também.

Outro exemplo disso é quando dá erro em algum formulário e só a borda do campo fica vermelha para indicar o erro. Você pode ajudar o usuário com um apoio visual (um ícone) ou um apoio textual:

![Input with and without error message](https://blog.caelum.com.br/wp-content/uploads/2042/08/mail-without-and-with-error-message.jpg)

## 2 - Não tirarás o zoom do usuário

Eu uso óculos. Além de deixar o iOS com textos em negrito e aumentar a fonte do sistema como um todo, eu constantemente preciso dar zoom em sites. Aí por algum motivo divino alguém achou que era uma boa ideia travar o zoom para eu não enxergar o conteúdo do site. Afinal, tem que ficar bonitinho

![Zoom dont allowed](https://blog.caelum.com.br/wp-content/uploads/2042/08/zoom-dont-allowed-577x1024.jpg)

Toda vez que você bloqueia o zoom no seu site ou app, um [filhote de panda escorrega e cai no cimento](http://imgur.com/gallery/6N46F). Apesar de ser fácil [travar o zoom](https://stackoverflow.com/a/4472910), ferir essa liberdade só se justificaria se a experiência de usuário fosse bastante prejudicada por conta disso. Entretanto, recomendo que tenha mais empatia e repense essa UX.

## 3 - Imagens de conteúdo precisam do atributo ALT

Você entra no seu e-commerce preferido para ver se tem promoção de roupa e aparece isso logo na home:

![Promotion 20% off banner example](https://blog.caelum.com.br/wp-content/uploads/2042/08/example-banner-20-off.jpg)

Bacana! 20% de desconto é um baita desconto! Agora olha como fica para o leitor de tela de uma pessoa cega:

![Example banner without alt attribute](https://blog.caelum.com.br/wp-content/uploads/2042/08/banner-without-alt-1.jpg)

Não tão bacana agora, ele vai ler apenas "Imagem...", como que o software vai saber do que se trata a imagem?! Como que o usuário cego vai saber que está rolando uma promoção? Hoje, infelizmente, os softwares leitores de tela ainda não conseguem ler o conteúdo de uma imagem sem um apoio textual, o atributo `alt`.  Veja como não é nada complicado colocar o `alt` em uma imagem no HTML:

`<img src="imagens/destaque-home.png" alt="Promoção: Sunny Days 20% off">`

Alguns pontos interessantes sobre o `alt`:

- você colocou o texto "imagem de um robô" no `alt` de uma imagem, o leitor de tela lerá "Imagem: imagem de um robô". Redundante? Com certeza! Evite;
- se não há nada para ser colocado em algum `alt`, repense a função da imagem, se ela for decorativa coloque-a via CSS com [técnicas de _image replacement_](https://www.caelum.com.br/apostila-html-css-javascript/mais-html-e-css/#4-12-substituicao-por-imagem). Se não puder tirar, deixe o `alt` vazio;
- além de melhorar a acessibilidade, você melhorará a experiência de uso para conexões mais lentas e também sua posição no Google;
- o Facebook tem uma AI monstruosa que está gerando `alt` automático. Dê um "Inspect element" em algumas fotos suas ou [olha esse print aqui](https://blog.caelum.com.br/wp-content/uploads/2042/08/automatic-alt-attribute-on-facebook.jpg). Ainda um pouco aberto demais, mas estamos evoluindo bem;
- Recomendo **fortemente** investir meia hora do seu tempo e ver esta [talk do Reinaldo Ferraz sobre o alt](https://www.youtube.com/watch?v=5FJJuEVt5sA&).

 

## 4 - HTML semântico com sabedoria

Essa pode levar meses para você dominar, mas preocupe-se em quando estiver marcando o conteúdo do seu site em marcá-lo corretamente.

Se um texto tem função de subtítulo, por que marcá-lo com um `<p>` se existe um elemento específico para isso, no caso, o `<h2>`? Isso é ruim tanto para acessibilidade quanto para SEO.

Além disso, há alguns leitores de tela que possuem atalhos para escaneamento rápido dos títulos da página, que tal ajudar nisso?

Outros pontos rápidos?

- se for pra navegação, use o `<a>`, se for interação ou em formulários use o `<button>`;
- campos de texto sempre atrelados a algum rótulo (`<label>`) sobre o que é aquele campo;
- se precisa de um _container_ e não sabe direito qual elemento do HTML5 usar, melhor ser genérico com `<div>` do que marcar erroneamente;
- quer passar a ideia que um texto é importante, use o elemento `<strong>`, o `<b>` seria visual, e visual é CSS;
- use o atributo `lang` correspondente ao idioma da página, e até de trechos que sejam diferentes do da página;
- o leitor de tela não diferencia atualmente `<h1>` de `<main>` e `<h1>` de `<footer>`, mantenha a regra antiga limitando-se a um por página;
- `placeholder` não substitui `<label>` e isso é **regra**.

 

## 5 - Menu muito grande? Link para pular para o conteúdo

Você resolveu ir a um show e acabou quebrando os braços (não me pergunte como) enquanto pulava loucamente ao som de Night Train. Ortopedista disse que em duas semanas você poderá tirar o gesso, e enquanto isso só conseguirá usar o computador com um dedo... Vai ter que se virar usando só a tecla TAB.

Aí você entra em algum e-commerce e tem que passar por quinhentos itens no menu de navegação até chegar no produto que você quer. Nada legal... Solução? Um **link escondido para pular para o conteúdo principal**! Algo como o WordPress faz:

![Jump to main content in Wordpress](https://blog.caelum.com.br/wp-content/uploads/2042/08/jump-to-main-content-wordpress.png)

Como que esse atalho surge? No primeiro TAB que o usuário der! Simples e facilita bastante a vida de quem depende do teclado para navegação, como no exemplo que dei acima (deficiência temporária) ou algum tipo de deficiência permanente ou situacional.

E claro, dê uma tratada com carinho na ordem dos tabs com a ajuda do [atributo tabindex](https://developer.mozilla.org/pt-BR/docs/Web/HTML/Global_attributes/tabindex).

 

## Implementei tudo isso já, e agora?

"_Legal, Natan. Implementei essa lista e várias outras coisas mas como saber se está ok?_".

Quando você troca uma lâmpada como você sabe se funcionou? Você testa! Várias coisas bacanas que você pode fazer para testar:

- desligar o monitor e tentar navegar usando um leitor de tela como o [NVDA;](https://www.nvaccess.org/)
- tentar navegar apenas via teclado;
- tirar o óculos para ver se os tamanho das fontes estão decentes;
- usar alguma extensão como o [Spectrum](https://chrome.google.com/webstore/detail/spectrum/ofclemegkcmilinpcimpjkfhjfgmhieb) que simula a visão daltônica;
- [lista com validadores automáticos de acessibilidade](https://www.w3.org/WAI/ER/tools/);
- contratar deficientes visuais para fazer testes de usabilidade.

> A melhor maneira de verificar se o seu site é acessível é testando!

 

## Quem seguir e onde pegar mais informações

- [Talita Pagani](https://twitter.com/talitapagani) e [Reinaldo Ferraz](https://twitter.com/reinaldoferraz) são ótimas referências e sempre estão palestrando sobre o assunto;
- [WebAIM](http://webaim.org) é uma bíblia online de acessibilidade;
- [Styleguide de Acessibilidade](http://a11y-style-guide.com/style-guide/);
- [Diretrizes de Acessibilidade para Conteúdo Web](https://www.w3.org/Translations/WCAG20-pt-PT/) (WCAG), da própria W3C;
- [Hipsters.tech sobre Acessibilidade.](https://hipsters.tech/acessibilidade-web-hipsters-21/)

 

Já viu o [curso de Acessibilidade web](https://www.alura.com.br/curso-online-acessibilidade-web-design-inclusivos) na Alura? Mostra que acessibilidade começa bem antes de colocar a mão no código!

E como a acessibilidade é tratada, ou não tratada, na sua empresa hoje? Quais os desafios e desculpas que aparecem no dia-a-dia?

Conta aí nos comentários!
