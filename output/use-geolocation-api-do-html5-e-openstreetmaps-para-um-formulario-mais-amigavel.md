---
title: "Use Geolocation API do HTML5 e OpenStreetMaps para um formulário mais amigável"
date: "2015-07-11"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Você certamente já se irritou ao preencher um formulário online gigante, cheio de campos. E você certamente já quis se jogar pela janela se tentou preencher esse formulário num celular. A web mobile tem crescido muito, mas **digitar no celular ainda é trabalhoso**. Faça de tudo pra limitar a frustração dos seus usuários.

E uma ideia é usufruir dos recursos cada vez mais avançados dos navegadores - tanto mobile quanto Desktop. Nesse artigo, quero explorar a **Geolocation API** do HTML5 para oferecer ao usuário o preenchimento automático dos seus dados de endereço.

É um recurso a mais, no melhor estilo _progressive enhancement_, que pode ser usado ao pedir pro usuário criar seu cadastro, preencher um endereço de entrega numa loja virtual, e em muitos outros cenários.

## A Geolocation API

O que conseguimos, basicamente, é obter as coordenadas - **latitude** e **longitude** - do usuário. A API é simples, através do método `getCurrentPosition` no objeto `navigator.geolocation`. Note que obtemos as coordenadas através de um callback assíncrono, já que a obtenção dos dados pode demorar um pouco:

\[code language="javascript"\] navigator.geolocation.getCurrentPosition(function(posicao) { alert(posicao.coords.latitude + ', ' + posicao.coords.longitude); }); \[/code\]

No código acima, apenas mostramos a latitude e a longitude obtidas em um `alert`. Teste usando o botão abaixo:

<p data-height="92" data-theme-id="0" data-slug-hash="eNBdEq" data-default-tab="result" data-user="sergiolopes" class="codepen">Veja o exemplo no <a href="http://codepen.io/sergiolopes/pen/eNBdEq/">CodePen</a>.</p>

O navegador sempre vai pedir autorização ao usuário antes de liberar a informação de localização, mantendo a privacidade.

A [API](http://www.w3.org/TR/geolocation-API/) ainda nos permite outras opções. Podemos passar um callback adicional para rodar em caso de erro e até um terceiro argumento com opções adicionais. É possível definir um `timeout` de espera, permitir que o navegador dê a última posição em cache ou exigir uma nova posição, e até habilitar um modo de alta precisão que pode demorar mais mas dá um resultado melhor. Podemos também usar o método `watchPosition`, que nos permite monitorar o deslocamento do usuário pedindo a posição várias vezes (útil para uma app de navegação).

A Geolocation API já é suportada em [todos os principais navegadores](http://caniuse.com/geolocation) - incluindo IE9.

[![](http://imgs.xkcd.com/comics/geoip.png "XKCD")](http://xkcd.com/713/)

## Dados geográficos com OpenStreetMaps

A geolocation API só vai nos dar as coordenadas. Para implementarmos a funcionalidade de preencher o formulário, precisamos obter os dados do endereço a partir da latitude e da longitude. Mas esse passo não é tão trivial: é preciso buscar em algum banco de dados geográfico.

Há algumas opções de serviços que fazem o chamado _reverse geocoding_. O [Google Maps](https://developers.google.com/maps/documentation/geocoding/#ReverseGeocoding) é uma opção famosa mas tem uma API paga e um pouco mais complicada. O [OpenStreetMaps](http://wiki.openstreetmap.org/wiki/Nominatim#Reverse_Geocoding_.2F_Address_lookup) possui um serviço muito simples e aberto para uso, fácil de se integrar em qualquer página Web via JSONP.

O endereço é simples e recebe as coordenadas como argumento. Por exemplo, com a localização da Caelum SP: (clique para ver a resposta)

**[http://nominatim.openstreetmap.org/reverse?lat=-23.5880894&lon=-46.6321951](http://nominatim.openstreetmap.org/reverse?lat=-23.5880894&lon=-46.6321951)**

Ainda podemos escolher o formato (JSON, XML) e se queremos um callback (JSONP). Como vamos integrar direto na página Web, vamos usar JSONP e nosso callback chamará `preencherDados`:

**[http://nominatim.openstreetmap.org/reverse?lat=-23.5880894&lon=-46.6321951&format=json&json\_callback=preencherDados](http://nominatim.openstreetmap.org/reverse?lat=-23.5880894&lon=-46.6321951&format=json&json_callback=preencherDados)**

## Misturando tudo para uma boa experiência do usuário

Imagine então um formulário de cadastro que precisa de muitas informações. Além do nome e do email, o endereço completo do usuário. Podemos oferecer a opção de **preencher automaticamente o endereço com os dados de geolocalização do usuário**.

Começamos pegando as coordenadas do usuário com a Geolocation API e montando a URL dinamicamente:

\[code language="javascript"\] navigator.geolocation.getCurrentPosition(function(posicao) { var url = "http://nominatim.openstreetmap.org/reverse?lat="+posicao.coords.latitude+"&lon="+posicao.coords.longitude+"&format=json&json\_callback=preencherDados"; }); \[/code\]

Para chamar a URL, como estamos usando JSONP, basta criar um elemento `<script>` com `src` apontando para essa URL:

\[code language="javascript"\] var script = document.createElement('script'); script.src = url; document.body.appendChild(script); \[/code\]

Na URL, definimos a função `preencherDados` como receptora dos dados do serviço do OpenStreetMap. Precisamos definir essa função então, que receberá os dados em formato JSON. Aí basta navegar nos dados e usá-los para preencher os campos na página. Por exemplo, se tivermos um elemento com ID campoCEP na página, faríamos:

\[code language="javascript"\] function preencherDados(dados) { document.querySelector('#campoCEP').value = dados.address.postcode; } \[/code\]

O exemplo final apenas cria mais campos (rua, número, cidade, CEP) e preenche todos com base no retorno do serviço. Veja:

<p data-height="500" data-theme-id="0" data-slug-hash="WvoGZa" data-default-tab="result" data-user="sergiolopes" class="codepen">Veja o exemplo no <a href="http://codepen.io/sergiolopes/pen/WvoGZa/">CodePen</a>.</p>

Há muitas possibilidades de melhoria ainda, claro. Como oferecer um spinner enquanto carrega, usar um ícone de geolocalização, não exibir o botão caso o navegador não tenha suporte, preencher mais campos (estafo, país, etc).

Mas repare como pouco código já traz muito ganho de usabilidade. O usuário pode pular o preenchimento de vários campos, ganhando tempo. Algo essencial sobretudo no mobile. Repare ainda como o formulário continua editável, para o caso de erros nos dados e o usuário precisar fazer ajustes.

Nos nossos [cursos de front-end](https://www.caelum.com.br/cursos-web-front-end/) estamos sempre preocupados em como fazer códigos que melhorem a interface e ainda com _progressive enhancement_. No [curso de usabilidade](https://www.caelum.com.br/curso-design-interface-ux-usabilidade/), discutimos esse e outros cenários que complicam a vida do usuário e como soluções técnicas simples podem fazer grande diferença.

<script async src="//assets.codepen.io/assets/embed/ei.js"></script>
