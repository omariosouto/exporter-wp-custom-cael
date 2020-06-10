---
title: "Qual é a velocidade de carregamento do seu site?"
date: "2013-05-21"
author: "luiz.real"
authorEmail: "luiz.real@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O desempenho de um site web é muito importante para manter suas visitas. Um site com desempenho ruim pode afastar seus clientes e diminuir suas vendas, como mostram [diversos estudos realizados e coletados neste post do Sérgio Lopes](http://sergiolopes.org/tweetables-performance-web-otimizacoes/), aqui da Caelum.

Para otimizar o desempenho de um site, é essencial saber por onde começar. Para ajudar nisso, existem diversas ferramentas que analisam o seu site e mostram o que está demorando para carregar e o que você pode fazer para melhorar o seu site, entre elas o [Google PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights), o [WebPageTest.org](http://www.webpagetest.org/) e o [YSlow!](http://developer.yahoo.com/yslow/).

O problema é que a gente sempre desenvolve sites no localhost e, com exceção do YSlow!, que pode ser testado localmente, essas ferramentas analisam apenas sites online. Além disso, no localhost a transferência de dados é quase imediata, não temos limitação de banda, o que muitas vezes acaba mascarando problemas de desempenho no carregamento das páginas. Depois de trabalhar muito num site, quando o colocamos em produção, acabamos descobrindo, por exemplo, que nossas imagens estão muito pesadas, ou temos Javascript demais sendo carregado por página, ou que usuários sem acesso a internet rápida estão sofrendo.

Há o [Web Page Test](http://www.webpagetest.org/), que pode gerar um video passo a passo do carregamento da sua página, possibilitando até a escolha da banda:

<iframe src="http://player.vimeo.com/video/66096283" width="500" height="427" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

Ele também dá dados sobre o carregamento de cada um dos recursos do site:

[![Screen Shot 2013-05-13 at 4.21.32 PM](https://blog.caelum.com.br/wp-content/uploads/2013/05/Screen-Shot-2013-05-13-at-4.21.32-PM-300x217.png)](https://blog.caelum.com.br/wp-content/uploads/2013/05/Screen-Shot-2013-05-13-at-4.21.32-PM.png)

Pode ser chato ficar o tempo todo pedindo pro WebPageTest gerar um novo vídeo. Dá para simular essa limitação de banda no localhost com algumas ferramentas, entre elas uma chamada [**iprelay**](http://www.stewart.com.au/ip_relay/).

Se você tiver um servidor rodando na porta 8080 e quiser simular um acesso remoto a ele, você pode rodar o iprelay com o seguinte comando:

\[code\] iprelay -b2500 9090:localhost:8080 \[/code\]

E pronto! Agora, em vez de acessar a porta 8080, você pode acessar o localhost na porta 9090 e se sentir nas nossas belas redes 3G ou na era da Internet discada, com 2,5 KB de banda. Você controla o quanto de banda quer pelo argumento `-b`, passando a ele a taxa de bytes/segundo que deseja.

O iprelay é um script em Perl e, portanto, pode ser utilizado em qualquer sistema operacional com suporte a essa linguagem, o que inclui praticamente todos os sistemas mais utilizados atualmente.

E você, já viu se seu site está respondendo de maneira aceitável para usuários sem internet muito rápida?
