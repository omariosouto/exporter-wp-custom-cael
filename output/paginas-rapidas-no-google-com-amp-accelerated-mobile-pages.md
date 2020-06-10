---
title: "Páginas rápidas no Google com AMP - Accelerated Mobile Pages"
date: "2016-12-12"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Aqui na Caelum, Alura e Casa do Código, [somos fissurados em performance na web](https://blog.caelum.com.br/performance-web-no-mundo-real-porque-o-site-do-alura-voa/). Está mais que provado que [quanto mais rápido uma página é carregada para o usuário, melhor é a experiência](https://blog.caelum.com.br/porque-voce-nao-deveria-ligar-para-o-tempo-do-onload-ou-as-metricas-que-importam-para-performance-real-na-web/) dessa pessoa que está acessando nosso site e como consequência, [mais valor essa página pode gerar para o negócio](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/).

Para o SEO e o rankeamento dessa página nos buscadores, o resultado dessa performance é indireto mas também fundamental, pois um dos fatores levados em consideração é o Bounce Rate, ou seja, quanto menos usuários acessam sua página e rapidamente a abandonam, melhor para o seu ranking. E no mobile, onde as conexões são geralmente mais lentas, ter seu site carregando rapidamente é mais importante ainda.

Justamente para isso, em Outubro de 2015 foi lançado o [AMP - Accelerated Mobile Pages](https://www.ampproject.org/), que é um projeto da Google para padronizar páginas com o objetivo de serem carregadas rapidamente nos dispositivos móveis, mas que começou a ficar mais famoso esse ano com a adoção também do Facebook.

Na realidade, você nem precisa ser um _heavy user_ de dispositivos móveis para já ter usado AMP em algum momento antes. Se você usa um pouquinho de Google ou Facebook no seu celular, já deve ter passado por um link desses, com o um pequeno ícone de um raio.

\[caption id="attachment\_7921" align="aligncenter" width="378"\][![Página com AMP no Facebook](https://blog.caelum.com.br/wp-content/uploads/2016/12/amp-facebook.png)](https://blog.caelum.com.br/wp-content/uploads/2016/12/amp-facebook.png) Página com AMP no Facebook\[/caption\]

 

\[caption id="attachment\_7922" align="aligncenter" width="379"\][![Página com AMP no Google](https://blog.caelum.com.br/wp-content/uploads/2016/12/amp-google.png)](https://blog.caelum.com.br/wp-content/uploads/2016/12/amp-google.png) Página com AMP no Google\[/caption\]

**Como fazer minhas páginas serem compatíveis com AMP**

O primeiro passo para construir uma página AMP é montar a estrutura básica do HTML:

\[code language="html"\] <!doctype html> <html lang="pt-BR"> <head> <meta charset="utf-8"> <title>Passo a passo do blog da Caelum</title> </head> <body>

<h1>Páginas rápidas no Google com AMP</h1>

</body> </html> \[/code\]

No entanto, é preciso adicionar alguns cabeçalhos e estilos obrigatórios para essa página e o primeiro passo é adicionar no `head` o script que torna a página compatível:

\[code language="html"\] <head> <meta charset="utf-8"> <title>Passo a passo do blog da Caelum</title>

<script async src="https://cdn.ampproject.org/v0.js"></script> </head> \[/code\]

Em seguida, é preciso indicar que esse HTML agora é um HTML AMP. Fazemos isso adicionando amp ao lado da declaração da tag `<html>`:

\[code language="html"\] <html amp lang="pt-BR"> \[/code\]

Agora temos que indicar a `viewport` para ser de acordo com o dispositivo que abrirá o conteúdo:

\[code language="html"\] <head> <!-- demais tags --> <meta name="viewport" content="width=device-width,minimum-scale=1,initial-scale=1">

</head> \[/code\]

E por fim, somos obrigados a incluir o CSS básico com as transições para a exibição:

\[code language="css"\] <style amp-boilerplate>

body{ -webkit-animation:-amp-start 8s steps(1,end) 0s 1 normal both; -moz-animation:-amp-start 8s steps(1,end) 0s 1 normal both; -ms-animation:-amp-start 8s steps(1,end) 0s 1 normal both; animation:-amp-start 8s steps(1,end) 0s 1 normal both } @-webkit-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}} @-moz-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}} @-ms-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}} @-o-keyframes -amp-start{from{visibility:hidden}to{visibility:visible}} @keyframes -amp-start{from{visibility:hidden}to{visibility:visible}}

</style> <noscript> <style amp-boilerplate> body{-webkit-animation:none;-moz-animation:none;-ms-animation:none;animation:none} </style> </noscript> \[/code\]

Parece muita coisa, não? [Mas eu já deixei esse HTML básico pronto para você. É só baixá-lo e usar.](https://gist.github.com/adrianoalmeida7/68a15fe681654e76f616bf6b98fa1b79)

**Preparando o conteúdo da página para vídeos, imagens e áudios**

Enquanto o conteúdo da sua página tiver apenas textos, nada muda no HTML que você vai preparar para sua página AMP. Você poderá usar as mesmas tags H1, H2, span, links, botões e assim por diante.

No entanto, vai perceber que as tags `<img>`, `<video>` e `<audio>` não são compatíveis. Isso acontece, pois antes de carregar os recursos (áudio, vídeo ou imagens), é preciso entender o layout da página, para que eles sejam apresentados adequadamente. Além disso, também é preciso que as requisições feitas sejam otimizadas. Por esse motivo, essas 3 tags são substituídas por equivalentes, iniciando em `<amp-` .

Ou seja, para uma imagem, basta usarmos `<amp-img src="caminho_da_imagem.jpg"></amp-img>`

**Customizando o CSS da sua página**

Todo o CSS necessário para que a página seja exibida precisa estar dentro do `<head>` da mesma e separado do `<style amp-boilerplate>` que já havíamos adicionado anteriormente.

Esse nosso CSS customizado deverá ser adicionado agora dentro de `<style amp-custom> </style>`. Apenas fique atento, pois [algumas poucas propriedades e seletores não são permitidos](https://www.ampproject.org/docs/guides/responsive/style_pages), no entanto as principais e que já estamos acostumados podem ser usadas livremente.

**Testando se não fizemos nenhuma besteira**

Para garantir que o que fizemos está válido, precisamos passar o nosso HTML por um validador que vai acusar para nós caso algo esteja errado. O processo de validação é extremamente simples. Basta você subir a página em um servidor html simples (pode ser um Apache, por exemplo) e acessar a página passando ao final do endereço `#development=1`, por exemplo: http://localhost:8000/artigo\_blog.amp.html#development=1

Para visualizar o resultado, basta abrir o console do Chrome e caso haja algo errado, as mensagens explicando os erros irão aparecer em vermelho:

\[caption id="attachment\_7924" align="aligncenter" width="623"\][![Erro de validação AMP](https://blog.caelum.com.br/wp-content/uploads/2016/12/erro-validacao-amp.png)](https://blog.caelum.com.br/wp-content/uploads/2016/12/erro-validacao-amp.png) Erro de validação AMP\[/caption\]

**Garanta que o conteúdo não será indexado duas vezes**

Repare que se colocássemos essa página no ar, com praticamente o mesmo conteúdo que já existe em uma outra página, estaríamos duplicando nosso próprio conteúdo, o que é uma má prática do ponto de vista de SEO.

Para contornarmos esse problema, precisamos na nossa página AMP, indicar qual é a página que possui o conteúdo original e que é a página que deverá ser indexada pelos buscadores. Fazemos isso através de um `<link rel="canonical">` que apontará para a página com o conteúdo original:

\[code language="html"\] <link rel="canonical" href="http://caminho\_do\_arquivo\_original/arquivo.html"> \[/code\]

Agora para que as ferramentas saibam que há uma página AMP por trás da página original, precisamos adicionar também a indicação na que será a página indexada:

\[code language="html"\] <link rel="amphtml" href="https://caminho\_do\_arquivo\_amp/arquivo.html"> \[/code\]

Pronto, agora poderíamos ir até o Search Console e solicitar que a nossa página seja re-indexada ou esperar que os bots dos buscadores passem em nossas páginas e faça a reindexação, para que nossa nova página AMP fique disponível.

Aqui na Caelum você pode aprender técnicas para desenvolvimento front-end que tornam seu site funcional no mobile, o famoso layout responsivo, com os [cursos da nossa formação front-end](https://www.caelum.com.br/cursos-web-front-end/). E para saber mais sobre SEO e outras técnicas para te ajudarem a rankear bem nos buscadores, você pode conhecer a [Carreira SEO Expert da Alura](https://www.alura.com.br/carreira-seo-expert) e também o livro de [SEO da Casa do Código](https://www.casadocodigo.com.br/products/livro-seo).
