---
title: "HTTP/2 Server Push na prática"
date: "2016-04-25"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Quando discutimos a [performance do novo site do Alura](https://blog.caelum.com.br/performance-web-no-mundo-real-porque-o-site-do-alura-voa/), comentei do nosso uso do HTTP 2.0 e, principalmente, do recurso chamado **Server Push**.

Já comentei um pouco da teoria do Server Push no post sobre as [novidades do HTTP 2.0](https://blog.caelum.com.br/as-fantasticas-novidades-do-http-2-0-e-do-spdy/). Mas como usar na prática? Vejamos.

## O gargalo e o inline de recursos

Para renderizar uma página, o navegador precisa baixar muitas coisas além do HTML. Precisa de HTML, CSS, JavaScript, imagens, fontes etc. Isso em várias requisições diferentes que o navegador precisa fazer para obter os recursos necessários.

Só que um do maiores gargalos da Web é que ele precisa ler o HTML para, por exemplo, descobrir qual requisição de CSS ele precisa fazer. E aí ler o CSS para descobrir, por exemplo, qual imagem de background baixar. Isso pra tudo.

É bem ruim. A renderização fica bloqueada porque o CSS não carregou ainda, mas pra isso ele precisa baixar e ler o HTML primeiro. Cria-se uma árvore de dependências que muitas vezes acarreta em carregamento sequencial dos elementos da página.

Sabendo disso, muitas pessoas começaram a fazer o **inline de recursos**. Para evitar ter que esperar o HTML ser lido pra só aí requisitar o CSS, você pode embutir o código CSS direto no HTML nas tags `<style>`. Ou seja, evitamos uma segunda requisição para obter o CSS, que já vai junto com o HTML logo na primeira.

Isso é particularmente interessante em recursos blocantes que atrasam a renderização inicial da página. Em especial, o CSS necessário para renderizar o topo da página. Esse tipo de prática ficou conhecida como **otimizar o Critical Rendering Path**. Embute-se o CSS de cima da dobra e carregamos o restante de forma assíncrona.

## O que é o Server Push

O inline de recursos é útil e uma otimização necessária. Mas não deixa de ser uma **gambiarra**. É trabalhoso de fazer e, principalmente, ruim para o cache. Com o CSS embutido no HTML, ele não pode ser cacheado de forma independente e ser compartilhado por várias páginas diferentes.

Eis então que o HTTP/2 entra com seu Server Push.

O Server Push resolve o problema do inline direto no protocolo de forma simples e elegante. A ideia é que, quando o usuário requisitar o HTML por exemplo, podemos enviar a resposta do CSS junto mesmo antes de ele requisitar.

[![](https://blog.caelum.com.br/wp-content/uploads/2014/04/Screenshot-2014-04-11-19.12.36.png)](https://blog.caelum.com.br/wp-content/uploads/2014/04/Screenshot-2014-04-11-19.12.36.png)

Ou seja, com uma única requisição, no HTTP/2, podem ser enviadas múltiplas respostas. Sem precisar fazer inline, sem matar o cache e de forma bastante simples.

## Server Push nos servidores

O Server Push é um recursos interno do protocolo HTTP/2 que é binário e difícil de manipular na mão. Então os servidores começaram a pensar numa forma dos desenvolvedores expressarem quais recursos devem ser pushados. Não existe uma regra aqui mas parece haver um consenso se formando em torno de usar a [RFC de Web Linking](https://tools.ietf.org/html/rfc5988). É assim que o Apache faz e é assim que o Google App Engine faz (e como usamos no site do Alura).

É bem simples: na resposta, você inclui um cabeçalho **Link** que lista as URLs dos recursos adicionais que serão pushados para o cliente. O servidor lê esse header e faz as mágicas para transformar num server push.

A sintaxe da [RFC de Web Linking](https://tools.ietf.org/html/rfc5988) manda a gente colocar a URL do recurso, o tipo dele e a relação que temos com ele. No caso, para o server push funcionar, queremos uma relação de **preload**.

Na prática, é mais simples que parece. Se quiser enviar o arquivo estilos.css, adicionamos o seguinte cabeçalho:

\[code\] Link: &amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;lt;estilos.css&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;gt;; rel=preload; as=style \[/code\]

Podemos pushar vários recursos de uma vez:

\[code\] Link: &amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;lt;estilos.css&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;gt;; rel=preload; as=style, &amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;lt;home.css&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;gt;; rel=preload; as=style \[/code\]

O servidor interpreta esse cabeçalho e oferecer para o navegador o push dos recursos listados como preload.

Para enviar o cabeçalho em si, use sua linguagem de backend favorita. Em PHP, basta um:

\[code language="php"\] &amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;lt;?php header('Link: &amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;lt;estilos.css&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;gt;; rel=preload; as=style'); ?&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;gt; \[/code\]

Ou em Java:

\[code language="java"\] response.addHeader(&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;quot;Link: &amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;lt;estilos.css&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;gt;; rel=preload; as=style&amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;amp;quot;); \[/code\]

## Como ver o push

E como saber se tudo está funcionando e ver na prática a diferença? Ao acessar a timeline dos requests no DevTools ou alguma outra ferramenta, você vai observar que a resposta do CSS vem imediatamente após o HTML. Ela foi pushada antes mesmo do HTML ser parseado pelo navegador.

Eu gosto de usar o WebPageTest. E ao observar o waterfall conseguimos ver duas coisas:

[![Screenshot 2016-03-22 17.09.44](https://blog.caelum.com.br/wp-content/uploads/2016/03/Screenshot-2016-03-22-17.09.44-1024x359.png)](http://www.webpagetest.org/result/160322_ER_1837/1/details/)

Primeiro repare que o CSS é baixado logo após o HTML. Dá pra ver analisando a barrinha azul do segundo request logo após o fim da barrinha azul do primeiro (HTML). E ao clicar em algum recurso que foi pushado, o WebPageTest mostra uma janela onde é possível ver **Loaded By HTTP/2 Server Push**.

No site do Alura, [usamos push para CSS e algumas web fonts](https://blog.caelum.com.br/performance-web-no-mundo-real-porque-o-site-do-alura-voa/).

## Porque Server Push é excelente

O server push é muito simples e elegante de usar. Coloca um header simples e pronto, a mágica tá lá. Bem melhor que fazer inlines estranhos e atrapalhados na mão.

E ele brilha no fato de ser totalmente integrado ao protocolo e ao cache do navegador. Se o recurso já estiver no cache, o navegador pode instantaneamente recusar o push e evitar download duplicado. Os recursos pushados continuam sendo URLs e recursos independentes, logo possuem seus próprios caches individuais que podem ser bem otimizados.

Com o push e com o [multiplexing do HTTP/2](https://blog.caelum.com.br/as-fantasticas-novidades-do-http-2-0-e-do-spdy/), diminui também a necessidade de se concatenar arquivos CSS e JS, e de se fazer sprites de imagens. Você pode pushar vários arquivos CSS de uma vez o que seria quase como concatenar e inlinear tudo.

O Server Push é uma das melhores novidades do HTTP/2. O suporte nos servidores ainda não é universal mas tem melhorado. Alguns navegadores HTTP/2 às vezes não suportam o push e o ignoram (como o Safari 9.0). Mas o suporte está crescendo rápido!

Alguns servidores têm experimentado com formas mais automáticas e espertas de fazer o push. [O Jetty por exemplo possui um filtro](http://www.eclipse.org/jetty/documentation/current/http2-configuring-push.html) que analisa o tráfego do seu site e descobre automaticamente quais recursos vale a pena pushar. E faz isso de forma transparente, basta habilitar o recurso.

E você já usa HTTP/2 no seu site? Tem casos de uso para o server push?
