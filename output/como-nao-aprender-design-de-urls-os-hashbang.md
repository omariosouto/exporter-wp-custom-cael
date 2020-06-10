---
title: "Como não aprender design de urls: os Hashbangs"
date: "2011-04-14"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/hashbang.png "hashbang")

Um dos grandes problemas de requisições AJAX é o fato de as engines de busca, como o Google, não possuírem uma forma simples e direta de realizar o crawling das páginas para que seus conteúdos apareçam nos resultados de buscas. Com isso, é muito comum que sites que foram desenvolvidos inteiramente utilizando AJAX, tenham apenas a Home visível nos resultados do buscador.

Para tentar resolver este problema, o Google propôs [uma maneira](http://googlewebmastercentral.blogspot.com/2009/10/proposal-for-making-ajax-crawlable.html) para indicar que as interações feitas através de requisições AJAX pudessem ser indexadas. Essa proposta sugeria que os desenvolvedores ao criarem seus links que disparariam requisições assíncronas, alterassem sua URL através da adição de fragmentos, que indicam algum id de elemento ou uma âncora existente na página, seguida de uma exclamação, como em http://www.acme.com/um\_livro#!capitulo2.

Ao utilizarmos em conjunto os caracteres #! nas URLs, temos o que ficou conhecido como _hashbang_.

Alguns sites utilizam essa técnica, como o Twitter: [http://twitter.com/#!/caelum](http://twitter.com/#!/caelum). Ao clicar em algum link que gere requisição AJAX nessa página, a URL mudará para outra contendo esse mesmo padrão, como em: [http://twitter.com/#!/caelum/followers](http://twitter.com/#!/caelum/followers). Com isso, agora o Googlebot ao navegar por essas páginas, conhece quais são essas URLs e pode indexá-las.

Um outro exemplo de site que tem se utilizado da mesma técnica é o [http://lifehacker.com](http://lifehacker.com).

**Desvantagens práticas**

Uma primeira desvantagem nessa abordagem, é que a partir de agora o site possui apenas 1 endereço válido, que é o caso do twitter com a [http://www.twitter.com](http://www.twitter.com). Dessa forma, quando acessamos [http://www.twitter.com/#!/caelum](http://www.twitter.com/#!/caelum), **o que é acessado é a Home do twitter**, que possui um código Javascript cuja função é disparar uma requisição AJAX para exibir os dados adequados a partir das informações contidas após o hashbang. Isso faz com que um acesso à esses dados se torne consideravelmente mais lento.

Além disso, se houver o menor erro nesse Javascript, a página inteira pode deixar de ser carregada. [E isso de fato aconteceu](http://www.isolani.co.uk/blog/javascript/BreakingTheWebWithHashBangs): no dia do lançamento do novo Lifehacker, um pequeno problema com JavaScript tornou o site inteiro inacessível durante horas.

Um outro ponto que deve ser levado em consideração é que ao utilizar hashbangs nas URLs, cria-se um acoplamento forte entre a URL e o Javascript que tem como função dar "significado" ao conteúdo da URL. Com isso, se futuramente for decidido utilizar alguma alternativa ao hashbang, o ideal é manter todas as URLs antigas funcionando, ou seja, nessa situação o Javascript será sempre necessário.

Outra desvantagem é a quebra da acessibilidade das páginas, um exemplo é tentar  visualizar o código fonte dos seguidores de alguma pessoa no twitter. O código fonte devolvido não é o que está sendo visualizado. Isso pode limitar a acessibilidade das páginas, pois alguns dispositivos ou softwares podem não interpretar esses Javascripts.

**Qual é a alternativa ao hashbang?**

Atualmente, com o HTML5, é possível fazer com que páginas que utilizam requisições AJAX para exibir conteúdos sejam facilmente indexadas pelos mecanismos de busca. Isso se dá através da [History API](http://www.w3.org/TR/html5/history.html) que permite que com uma simples chamada de JavaScript, troquemos a URL do nosso navegador.

A partir de métodos como o `history.pushState` e `history.replaceState` controlamos a URL que está sendo acessada no site e assim podemos exibir novos conteúdos carregados via AJAX.

Um exemplo de uso é o [Github](http://github.com), onde ao navegarmos pelo código de algum projeto, como por exemplo o [VRaptor](https://github.com/caelum/vraptor "VRaptor"), a navegação na estrutura de diretórios do projeto é uma requisição AJAX disparada para buscar o novo conteúdo. Ainda assim, a URL é alterada normalmente sem hashbang, como [https://github.com/caelum/vraptor/tree/master/vraptor-core](https://github.com/caelum/vraptor/tree/master/vraptor-core). Repare que não é preciso mais onerar as URLs com carecteres estranhos.

Cuidar das suas URLs desde o momento da sua criação e mantê-las funcionais é primordial para qualquer site. O bom design de uma URL pode fazer com que seu site aumente consideravelmente o número de requisições recebidas, mas para isso não é preciso fazer endereços específicos para mecanismos de busca, nesse caso, bastaria usar a History API. Vários tópicos de Web avançados como HTML5 são vistos no [curso WD-43](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/).
