---
title: "Performance Web no mundo real: porque o site da Alura voa"
date: "2016-03-07"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Quando [lançamos o novo site da Alura esse mês](http://blog.alura.com.br/o-site-do-alura-esta-de-cara-nova/) uma das coisas que mais chamou a atenção de todos foi a altíssima performance do site. Postei um vídeo de _Antes de Depois_ no Facebook e no Twitter que bombou:

\[video width="904" height="400" mp4="http://blog.alura.com.br/wp-content/uploads/2016/03/video-1.mp4"\]\[/video\]

Repare como o novo site já está todo renderizado **em apenas 1.5s**. Enquanto o antigo _começava_ a aparecer em 5.5s.

Muita gente perguntou como fizemos, então este um post focado na **nossa estratégia de performance no novo site da Alura**. Temos também um post com foco em explicar [nossa arquitetura por trás dos panos](http://blog.alura.com.br/a-arquitetura-do-novo-site-do-alura/).

## O básico de performance Web

Há anos que [falo de Performance na Web](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/) com foco em Front-end. No site da Alura, antes das otimizações agressivas, fizemos todo o feijão com arroz que todo mundo já sabe.

- GZIP
- Minificar CSS e JS
- Comprimir HTML
- CSS no <head>
- JS no fim do </body>
- Otimizamos as imagens (com [svgo](https://github.com/svg/svgo) em 99% dos casos)
- Cache-Control e Expires agressivo (até pro HTML!)

Mas só isso não fez a gente chegar _lá_. Tínhamos alguns gargalos importantes.

## Progressive Enhancement

O maior erro do mundo front-end atual é depender demais de JavaScript _#prontofalei_. Single Page Applications, Angular, React e cia são inimigos da performance (e da acessibilidade, do SEO e de mais coisas). [Frameworks pesados são desnecessários em muitos casos](http://blog.alura.com.br/quem-disse-que-eu-preciso-de-jquery/). Como no novo site da Alura.

HTML simples consegue te levar muito longe hoje em dia. E com um CSS bacana você faz muito sem precisar de JS. Para todo resto, um JavaScriptzinho aqui e ali resolve (sem jQuery). Resultado? Seu site é mais rápido, mais acessível e o Google te ama.

O bom e velho [Progressive Enhancement](https://blog.caelum.com.br/aplicando-o-progressive-enhancement/) é o coração da performance do novo site da Alura.

## Carregamento assíncrono e não-blocante

Claro que várias coisas no site exigem JavaScript. E aí só colocar o JavaScript no fim da página não resolve. Tudo ainda é carregado de forma blocante e sequencial. A resposta é [abusar do carregamento assíncrono](https://blog.caelum.com.br/otimizacoes-na-web-e-o-carregamento-assincrono/).

Todos os nossos JavaScripts são carregados com **async**. Por exemplo:

<script src="js/home.js" async></script>

O [suporte nos navegadores](http://caniuse.com/#search=async) hoje é excelente e poderíamos até colocar as chamadas no <head> sem perda de performance.

Além disso, na nossa página, há um **iframe do Vimeo** com um vídeo de apresentação da Alura. Ele é totalmente secundário na página, mas era carregado na hora pelo browser. A solução foi **adiar o carregamento** dele também, criando o iframe num JS assíncrono.

## Deveria ser crime não usar HTTP/2 hoje em dia

Uma das grandes vantagens de rodar no Google App Engine é ter HTTP/2 (e QUIC) automaticamente. Basta subir o site em HTTPS e já ganhamos [as magias do HTTP/2](https://blog.caelum.com.br/as-fantasticas-novidades-do-http-2-0-e-do-spdy/). E, com elas, muitas [otimizações de performance de antigamente mudam](https://blog.caelum.com.br/o-que-muda-nas-praticas-de-otimizacoes-de-performance-na-web-com-o-http-2-0-e-o-spdy/).

Em particular, não é preciso mais bitolar em concatenar arquivos CSS e JS agressivamente e nem criar sprites pra tudo. Isso não quer dizer que, no lugar do seu 1 arquivão de CSS concantenado você vai carregar 5467 arquivinhos. Isso pode estressar a rede e o browser.

No site da Alura, quebramos o CSS em 10 arquivos diferentes que são carregados individualmente e conforme a necessidade de cada página. Ainda há uma concatenação mas bem menos agressiva, o que facilita as coisas (no fonte temos por volta de 40 arquivos separados).

Outro ponto é que não nos preocupamos tanto com as imagens fora de sprites. Os ícones dos cursos por exemplo, são arquivos svg individuais. Mas mesmo carregando a lista com os [200 cursos da Alura](https://www.alura.com.br/cursos-online-tecnologia) você repara que a conexão multiplexada do HTTP/2 dá conta.

## Server Push ❤

O consenso do povo de performance Web hoje em dia é que o foco deve ser **remover todo e qualquer recurso blocante do carregamento inicial da página**. É o que o pessoal chama de otimizar o **_Critical Path_**. E o maior inimigo aqui é o CSS. Ele fica no <head> e bloqueia a renderização. Chegam até a recomendar não usar um arquivo .css e embutir o código CSS inicial numa tag <style> inline. Tudo pra destravar o _critical path_.

Na home da Alura a gente tem 2 arquivos CSS. Requisitá-los é algo que atrasa a renderização. O browser baixa o HTML, descobre os <link>, faz os requests do CSS e só então pode exibir a página. Dá pra ver isso num gráfico:

[![Screenshot 2016-03-02 16.43.11](http://blog.alura.com.br/wp-content/uploads/2016/03/Screenshot-2016-03-02-16.43.11-300x69.png)](http://blog.alura.com.br/wp-content/uploads/2016/03/Screenshot-2016-03-02-16.43.11.png)

Repare o tempão entre o HTML (1ª linha) e os CSS (2ª e 3ª linha).

Uma das coisas mais legais do HTTP/2 é o **Server Push**. Com ele, conseguimos enviar várias Respostas HTTP com base em apenas uma Requisição. Na prática, significa que **quando o browser requisita o HTML eu já posso mandar os CSS junto** antes mesmo dele requisitar. Isso é melhor que deixar o CSS inline pois continua sendo um recurso separado, com cache independente e o browser pode cancelar o _push_ se já tiver o recurso.

Observe a diferença brutal:

[![Screenshot 2016-03-02 16.47.10](http://blog.alura.com.br/wp-content/uploads/2016/03/Screenshot-2016-03-02-16.47.10-300x82.png)](http://blog.alura.com.br/wp-content/uploads/2016/03/Screenshot-2016-03-02-16.47.10.png)

## @font-face é lento

O elefante na sala por aqui eram as fontes customizadas. O [Thiago](https://twitter.com/thiagovilaca), nosso designer, fez uma criação linda usando [Open Sans, do Google Fonts](https://www.google.com/fonts/specimen/Open+Sans). Usar é fácil: só colocar **um CSS na página com <link>** e pronto.

Mas, Deus, isso é lento. **Muito lento.** Tipo intoleravelmente lento. É o que mais atrasava nossa renderização. É um CSS no <head> que conecta em outro domínio e faz mais um monte de requests. Isso demora demais. Ainda mais quando já otimizamos todo o resto, aí fica gritante o impacto de um mero @font-face externo.

A solução seria tirar a fonte e tacar Arial em tudo. Performance FTW! Mas o Thiago iria me matar. E os usuários não teriam esse design lindo que temos hoje. Então a estratégia foi tentar aliviar um pouco a pancada.

Primeiro passo é colocar tudo no servidor da Alura. **Usar as fontes localmente ao invés do servidor do Google deixa tudo mais rápido.** Perde-se o hipotético cache entre sites, mas me parece um preço razoável. Só isso já melhora mas podemos dar o passo seguinte: **Server Push nas fontes**!

Sabemos que o browser vai precisar das fontes pra renderizar a página e isso atrasa toda a renderização. Então assim como fizemos com os CSSs, podemos enviar as fontes via Server Push também. Elegante e rápido.

[![Screenshot 2016-03-02 17.57.09](http://blog.alura.com.br/wp-content/uploads/2016/03/Screenshot-2016-03-02-17.57.09-300x108.png)](http://blog.alura.com.br/wp-content/uploads/2016/03/Screenshot-2016-03-02-17.57.09.png)

## Mais otimizações

O principal é o que vimos até agora, mas várias outras coisas melhoram a performance.

Colocamos um **cache agressivo** em todas as coisas do site. A maioria dos _assets_ tá com cache de 1 ano como de praxe. Mas fomos além e cacheamos até o HTML. Sim, _Expires_ na resposta HTML do servidor. Usamos meia hora. Isso faz o browser guardar a página localmente e a navegação voa. Além disso, até o GAE cacheia as respostas num proxy deles lá. Claro, isso não é pra todos os projetos. Só fizemos no site porque as respostas são quase estáticas e não variam por usuário.

Outro ponto foi pensar bem as animações do site e colocar o máximo de trabalho possível na GPU. Fazemos as **animações com CSS3** priorizando propriedades aceleradas - como **transform** e **opacity**. Animar o SVG da home deu um certo trabalho também para ficar otimizada (que merece outro post).

O resultado é a grande diferença do site novo com relação ao antigo. **E usuários mais felizes.** O que você achou da performance do novo site? E que técnicas você já tem aplicado aí nos seus projetos?
