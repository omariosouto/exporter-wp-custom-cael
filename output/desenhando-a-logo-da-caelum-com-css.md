---
title: "Desenhando a logo da Caelum com CSS"
date: "2016-07-11"
author: "gabriel.ferreira"
authorEmail: "gabriel.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Além de instrutor da Caelum, eu também sou editor de conteúdo das redes sociais da própria Caelum, da [Alura](https://www.alura.com.br/) e da [Casa do Código](http://casadocodigo.com.br).

Além de criar e gerenciar o conteúdo que é postado, faz parte do meu trabalho interagir com as pessoas e ver o que elas estão falando sobre nós. E então, algumas semanas atrás fomos marcados em um tweet:

[![tweet](https://blog.caelum.com.br/wp-content/uploads/2016/06/tweet-300x292.png)](https://blog.caelum.com.br/wp-content/uploads/2016/06/tweet.png)

Eu e o [Paulo Silveira](http://twitter.com/paulo_silveira) achamos sensacional e pensamos: "esse cara tem que escrever um post pra gente!". Entramos em contato com o [Jerfferson](https://twitter.com/jerffersan) e ele topou!

O post a seguir é de autoria dele. Boa leitura :)

* * *

Estes dias atrás eu estava brincando no [codepen](http://codepen.io) quando um amigo me desafiou a desenhar a logo da Caelum com CSS. Eu aceitei o desafio e desenhei a logo que você pode conferir [neste link](http://codepen.io/jerfersan/pen/gMOrEm).

Vou deixar claro que não é nenhum pouco prático desenhar uma logo com CSS, primeiro porque o CSS é uma linguagem de estilo, e segundo porque existem formas melhores para fazer isto, como, por exemplo, desenhar em um software de gráficos vetoriais e depois exportar com o formato SVG. Mas existem algumas coisas que podemos revisar nesta logo desenhada com CSS.

Eu vou mostrar como eu desenhei a logo e no decorrer da explicação eu citarei alguns tópicos (nem sei se vai ter tópico suficiente pra citar :B).

Se preferir, abra o [codepen](http://codepen.io/) ou o [jsfiddle](https://jsfiddle.net/) para acompanhar.

## Análise da logo da Caleum

Vamos fazer uma breve análise visual da logo da Caelum:

[![0-iUVQIOPkCegZzL8m-](https://blog.caelum.com.br/wp-content/uploads/2016/06/0-iUVQIOPkCegZzL8m--300x94.png)](https://blog.caelum.com.br/wp-content/uploads/2016/06/0-iUVQIOPkCegZzL8m-.png)

O simbolo da logo da Caelum é basicamente composto por:

- Um quadrado preto externo com esquinas arredondadas;
- Um quadrado branco interno com esquinas arredondadas, porém com a esquina superior esquerda muito mais arredondada;
- Uma estrela no canto superior esquerdo.

E temos o texto da logo, que eu basicamente ignorei a fonte utilizada na logo original (eu espero que o designer da logo não fique bravo comigo).

## Desenho do símbolo da logo

Se existisse alguma parte complicada para desenhar a logo da Caelum com CSS, essa parte seria o símbolo, mas não existe. São basicamente dois quadrados arredondados :D

Eis aqui todo o HTML do símbolo:

\[html\]<div class="symbol"></div>\[/html\]

Sim, não precisaremos nada além de um único elemento, pois utilizaremos pseudo-elementos do CSS para atingir o nosso objetivo. Opa, e aqui está o primeiro tópico: **pseudo-elementos**.

Você pode acessar a [documentação da Mozilla Developer Network](https://developer.mozilla.org/en/docs/Web/CSS/Pseudo-elements) para saber um pouco mais sobre pseudo-elementos.

Aqui estão as nossas primeiras linhas de CSS:

\[css\] .symbol { width: 100px; height: 100px; background-color: #000; border-radius: 20px; position:relative; } \[/css\]

Ok, temos agora o nosso primeiro quadrado preto.

Vamos adicionar mais um pouco de CSS:

\[css\] .symbol:before { display:block; content:""; background-color:#FFF; position:absolute; left: 9px; top: 9px; right: 9px; bottom: 9px; border-radius: 14px; border-top-left-radius: 100%; } \[/css\]

Acabamos de adicionar o detalhe branco interno, e agora já é possível visualizar a logo da Caelum se formando.

Note que eu utilizei o pseudo-elemento ::before com apenas um ":" (dois-pontos), o motivo é simples: Os pseudos elementos foram introduzidos no CSS2 com apenas um ":", e na especificação do CSS3 os pseudos elementos foram implementados com dois ":" para poder diferenciar das pseudo-classes. Por questão de compatibilidade com alguns navegadores (IE8, cof cof!), é bom utilizar apenas ":before".

Para terminarmos o símbolo, precisaremos adicionar a estrela no canto superior esquerdo.

E aqui estão as nossas últimas linhas de css para o símbolo da logo da Caelum: \[css\] .symbol:after { color: #FFF; content:"★"; display:block; font-size: 18px; left: 8px; top: 8px; position:absolute; } \[/css\]

Novamente eu utilizei um pseudo-elemento, porém desta vez eu utilizei o pseudo-elemento ::after para posicionar a estrela. A estrela é um caracter da [tabela unicode](http://unicode-table.com/en/).

Done! O nosso símbolo está pronto!

## Nome e slogan da Caelum

Agora precisaremos adicionar o nome e slogan da Caelum. Para isto devemos incrementar o nosso HTML:

\[html\] <div class="logo"> <div class="symbol"></div> <div class="nome">caelum</div> <div class="slogan">ensino e inovação</div> </div> \[/html\]

O css do nome e slogan da logo ficam dessa forma: \[css\] .logo { color: #000; font-family: "Arial"; width: 400px; height: 140px; position:relative; } .nome{ font-size: 90px; line-height: 105%; position: absolute; right: 0; top: 12px; } .slogan { font-size: 30px; position:absolute; right: 4px; top: 102px; } \[/css\]

Aqui o CSS também é bem simples, a única questão é o posicionamento. Poderíamos ter posicionado o nome e o slogan de diversas maneiras (com float, por exemplo), mas eu preferi posicionar com auxílio do posicionamento absoluto, pois pra este caso é mais simples e não tem muitos pontos negativos.

É isso! Aqui terminamos de desenhar a logo da Caelum com CSS.

## Considerações finais

Se você acessou o codepen que citei no início do post, provavelmente você deve ter notado que o CSS foi escrito com LESS. O único motivo para eu ter utilizado o LESS foi por causa das variáveis, pois seria possível redimensionar a logo baseado na largura dela.

Se você não conhece pré-processadores de CSS, recomendo que você dê uma olhada no LESS ou SASS.

Sinta-se a vontade para dar o seu feedback, ele será muito bem vindo :\]
