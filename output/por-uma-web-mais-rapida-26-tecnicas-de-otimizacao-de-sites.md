---
title: "Por uma Web mais rápida: 26 técnicas de otimização de Sites"
date: "2011-09-12"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

_A primeira versão desse post surgiu de uma palestra no QCon sobre otimização de performance de carregamento de páginas na Web. Desde então, ele virou o post mais acessado aqui no blog e gerou dezenas de outros artigos, palestras e cursos na Alura sobre performance. As dicas aqui continuam valendo (claro!) mas temos mais técnicas mais recentes - veja [outros posts](https://blog.caelum.com.br/?s=performance+web) aqui no blog e não deixe de conferir os [cursos de](https://www.alura.com.br/curso-online-otimizacao-performance-web) [performance Web](https://www.alura.com.br/curso-online-performance-http2-critical-path) na Alura._

<iframe src="http://www.slideshare.net/slideshow/embed_code/9209041?rel=0" width="510" height="426" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>

[\[Slides\] Por uma web mais rápida: técnicas de otimização de Sites (slides)](http://www.slideshare.net/caelumdev/qcon-2011-por-uma-web-mais-rpida-tcnicas-de-otimizao-de-sites "Por uma web mais rápida: técnicas de otimização de Sites")

Por que otimizar nossos Sites é tão importante? Simplesmente porque nenhum usuário gosta de perder tempo esperando o carregamento das páginas. É extremamente irritante. E isso tem graves consequências, como mostram esses estudos:

- O **Yahoo!** descobriu que, para cada 400ms de melhora na performance, seu tráfego aumenta em 9% ([fonte](http://www.slideshare.net/stoyan/yslow-20-presentation)).

- Ao cortar 2,2s da landing page do Firefox, a **Mozilla** aumentou o número de downloads em 15%, totalizando um ganho de mais de 60 milhões de cópias por ano ([fonte](http://blog.mozilla.com/metrics/category/website-optimization/)).

- A **Amazon** concluiu que apenas 100ms de melhora aumentam 1% seu faturamento ([fonte](https://sites.google.com/site/glinden/Home/StanfordDataMining.2006-11-28.ppt?attredirects=0)).

- Em um de seus vários experimentos, o **Google** aumentou o número de resultados por página de 10 para 30. Isso aumentou o tempo de carregamento de 0.4s para 0.9s, o que diminuiu em 20% o tráfego das buscas ([fonte](http://glinden.blogspot.com/2006/11/marissa-mayer-at-web-20.html)).

- A **Microsoft** mostrou que 2s a mais de latência no Bing diminuíam o faturamento em 4,3% ([fonte](http://velocityconference.blip.tv/file/2279751/)).

- Em um experimento, a **Caelum** subiu o tamanho de sua página de 100kb para 300kb, aumentando o número de requests de 12 para 42, o que resultou em um crescimento no tempo de carregamento de 2s para 6s. Os impactos foram queda de 21% no tempo que os usuários ficam no Site, menos 28% pageviews e uma queda de 18% no _conversion rate_.

- Há diversos outros [experimentos publicados](http://www.webperformancetoday.com/2010/06/15/everything-you-wanted-to-know-about-web-performance/), incluindo um estudo que mostra uma relação entre sites lentos e queda da pressão arterial ([fonte](http://www.websiteoptimization.com/speed/tweak/psychology-web-performance/)).

![](https://blog.caelum.com.br/wp-content/uploads/2011/09/otimizacoes.png)

Mas falar de otimizações de browser em um evento como o QCon pode parecer básico. Tantos arquitetos, discussões de alto nível sobre novas plataformas como Node.JS e processamento assíncrono, diversas palestras sobre cloud computing, todo mundo usando memcache e tantas outras técnicas avançadas.

Então, para contextualizar mais ainda a discussão, [analisei os dados dos Sites de todos os participantes do evento](https://gist.github.com/1210374). Com base nos e-mails dos inscritos (descartando webmails como GMail, Hotmail etc), separei mais de 100 domínios para testes, incluindo grandes portais brasileiros, empresas de tecnologia, Sites do governo e outros. Submeti todos ao [WebPageTest.org](http://www.webpagetest.org) e compilei os resultados.

O número mais importante, o tempo total de carregamento, já dá uma ideia da situação. Os sites analisados demoram, em média, **9 segundos para carregar**, sendo que alguns demoraram mais de 40s. E a grande verdade é que a performance final para o usuário depende muito mais do _client-side_ que do _server-side_. De todos os Sites analisados, a esmagadora maioria, 75%, tinha um tempo de processamento de menos de 400ms no servidor.

#### Interessado em Desenvolvimento e Otimizações Web? [Confira os cursos de Front End da Caelum!](http://www.caelum.com.br/cursos-web-front-end/)

Há muito espaço para melhora na maior parte dos Sites. E a palestra aborda diversos tópicos de otimização.

# Técnicas de otimização

- [#1 - Habilite GZIP](#habilite-gzip)
- [#2 - Minifique JavaScript](#minifique-js)
- [#3 - Minifique CSS](#minifique-css)
- [#4 - Comprima o HTML](#comprima-html)
- [#5 - Não redimensione imagens no HTML](#redimensione-imagens)
- [#6 - Otimize as imagens](#otimize-imagens)
- [#7 - Pense no formato das imagens](#formato-imagens)
- [#8 - Diminua cookies e headers](#cookies-headers)
- [#9 - Junte arquivos JavaScript](#arquivos-js)
- [#10 - Juntar arquivos CSS](#juntar-css)
- [#11 - Use Sprites](#sprites)
- [#12 - Use Data URIs](#uris)
- [#13 - Configure os headers](#configure-headers)
- [#14 - Tire firulas do design](#design)
- [#15 - Especifique o tamanho das imagens](#tamanho-imagens)
- [#16 - Coloque o JavaScript no fim](#js-no-fim)
- [#17 - Coloque o CSS no topo](#css-no-topo)
- [#18 e #19 - Adie o carregamento do que puder e Abuse do carregamento assíncrono](#assincrono)
- [#20 - Otimize o First-View e o Above the Fold Time](#otimize-fv-ft)
- [#21 - Design performático](#design-performatico)
- [#22 - Automatize](#automatize)
- [#23 - Use ferramentas de diagnóstico](#diagnostico)
- [#24 - Pré-carregue componentes](#componentes)
- [#25 - Escreva código eficiente](#codigo-eficiente)
- [#26 - Dispare logo o onload](#onload)
- [Quebre as regras](#quebre-regras)
- [Referências](#referencias)

## Diminua o tamanho dos requests

Boa prática em qualquer aplicação distribuída, diminuir o _payload_, ou seja, o volume de dados trafegados, é um princípio básico. E há diversas formas de se fazer isso.

Na análise das páginas dos participantes do evento, **a média de tamanho foi de 860kb**. Além disso, 35% dos Sites têm mais de 1MB de tamanho, sendo que o pior caso chegava a ter incríveis 8MB.

![](https://blog.caelum.com.br/wp-content/uploads/2011/09/compressao.jpg)

### #1 - Habilite GZIP

[Há muito tempo](https://blog.caelum.com.br/top-7-praticas-para-um-site-otimizado/) que falo que habilitar o GZIP no servidor é o primeiro passo. Não leva mais de 30s para ser feito e é suportado em todos os navegadores e servidores dos últimos 15 anos. Com ele, todo conteúdo textual (HTML, CSS, JS etc) é comprimido antes de ser enviado para o cliente, chegando a cortar mais de 50% do tráfego total.

Apesar disso, na análise dos Sites, descobri que 43% de todos os requests textuais não estavam gzipados. Mais: **apenas 14% dos Sites tinha gzip em todos os requests**.

### #2 - Minifique JavaScript

Um código JS bem feito é bem documentado, todo identado e possui nomes de variáveis legíveis. Mas nada disso conta na execução do script, e trafegar esse monte de bytes é desperdício. Em poucos minutos, é possível integrar uma ferramenta de minificação de JavaScript, como [YUI Compressor](http://developer.yahoo.com/yui/compressor/), [Google Closure Compiler](http://code.google.com/closure/compiler/) ou [UglifyJS](https://github.com/mishoo/UglifyJS).

Muitos Sites costumam incluir alguns scripts já minificados como o jQuery (com seu arquivo jquery-min). Apesar disso, dos Sites analisados, **apenas 13% minificavam todos os JavaScripts**.

### #3 - Minifique CSS

Minificar os arquivos CSS é igualmente importante. Para isso, podemos usar o [YUI Compressor](http://developer.yahoo.com/yui/compressor/) e até o [LESS compiler](http://lesscss.org/).

### #4 - Comprima o HTML

O código HTML também pode ser comprimido, removendo espaços, comentários e até certas aspas e fecha tags desnecessárias. O Google, por exemplo, não fecha `</html>` ou `</body>` em sua home, já que isso não prejudica nenhum navegador e economiza mais alguns bytes.

O excelente [HTML Compressor](http://code.google.com/p/htmlcompressor/) permite diversas otimizações e é bastante configurável. Comprimir o HTML não costuma ser simples porque, em geral, tratamos com páginas dinâmicas.

![](https://blog.caelum.com.br/wp-content/uploads/2011/09/Untitled.jpg)

### #5 - Não redimensione imagens no HTML

Princípio básico das otimizações, nunca deixe para diminuir imagens setando o `width` e o `height` no HTML ou CSS. Sempre redimensione o arquivo original e referencie o tamanho direto.

### #6 - Otimize as imagens

Dentro de um arquivo PNG ou JPG muitos dados são guardados. Além das informações da imagem em si, há metadados como EXIF, e às vezes, até uma miniatura da própria imagem. Nada disso é necessário para renderizar a imagem na tela e podem ser removidos. Esse tipo de otimização, chamada de _lossless_, sem perda de qualidade visual, é o mínimo que toda aplicação deveria fazer. Com o [Smush.it](http://smushit.com), por exemplo, basta enviar as imagens e recebê-las de volta otimizadas. Outras ferramentas incluem o [ImageOptim](http://imageoptim.pornel.net/) além de diversas opções em linha de comando para automatização (pngquant, optipng, pngcrush, pngout, advpng).

Otimizar imagens é essencial porque costumam ser a parte mais pesada das páginas Web. Na análise dos Sites dos conferencistas, em média, **47% dos bytes da página eram de imagens**. Apesar disso, apenas 15% dos Sites otimizam todas as imagens. Para 1/4 dos Sites, otimizar as imagens economizaria mais de 40% dos bytes totais.

Mas é possível também aplicar transformações _lossy_, isto é, diminuir a qualidade das imagens em prol de uma melhora na performance. Muitos designers nem cogitam essa possibilidade, mas fato é que muito no mundo é _lossy_. Um MP3, por exemplo, diminui a qualidade do áudio original para favorecer a mobilidade. Diminuir a qualidade de uma foto JPEG com pouco impacto visual perceptível pode trazer imensos ganhos. O novo serviço [JPEGMini](http://www.jpegmini.com/main/home), por exemplo, tem um avançado algoritmo que diminui a qualidade de JPEGs baseado em um modelo matemático que simula as características da percepção humana. O resultado são imagens com menos da metade do tamanho e visualmente idênticas.

Para saber mais, veja a [palestra de Billy Hoffman na Velocity Conf 2011](http://velocityconf.com/velocity2011/public/schedule/detail/18229).

### #7 - Pense no formato das imagens

Os formatos de imagens usáveis na Web são PNG, JPEG e GIF. Mas qual usar e como escolher o formato correto? Há diversos fatores. PNG e GIF mantém fidelidade da imagem; já o JPEG é um formato _lossy_ cuja compressão sempre causa perda de qualidade (aliás, é por isso que jamais se deve editar JPEGs diretamente; cada vez que se salva o arquivo, perde-se qualidade). Com isso, um PNG costuma ser mais interessante para gráficos quando fotos ficam com tamanhos menores em JPEGs.

Outro ponto é que PNGs suportam transparência com canal alpha, equanto que GIFs têm apenas transparência total e JPEG não suporta transparência. De modo geral, PNGs hoje são superiores a GIFs e o único motivo para usar o último é quando há animações.

Mas mesmo ao usar PNG, é possível escolher entre dois formatos diferentes: PNG8 e PNG24. O PNG clássico é o de 24bits que suporta milhões de cores e é exportado por padrão por todas as ferramentas gráficas. Mas o PNG8 diminui a palheta para 8bits, 256 cores no máximo, assim como os antigos GIFs. A vantagem do PNG8 é que tem um tamanho bem menor e é muito útil para imagens com poucas cores (logos, gráficos simples etc).

![](https://blog.caelum.com.br/wp-content/uploads/2011/09/Untitled1.jpg)

### #8 - Diminua cookies e headers

Um último passo para diminuir o tráfego é minimizar os headers envolvidos na requisição e na resposta. Destes, os cookies costumam ser o pior vilão pois são enviados a cada request entre cliente e servidor. Há até a recomendação de [servir conteúdos estáticos de domínios sem cookies](http://code.google.com/speed/page-speed/docs/request.html#ServeFromCookielessDomain).

## Diminua a quantidade de requests

Outro princípio base de qualquer sistema distribuído, minimizar o número de invocações remotas é essencial para boa performance. Na Web, isso significa diminuir os requests feitos na página para recursos externos - arquivos JS, CSS, imagens, Flash, vídeos etc.

No estudo dos Sites dos participantes da QCon, a média de requests é de 65 por página, sendo que 17% possuem mais de 100 requests.

### #9 - Junte arquivos JavaScript

A análise mostrou que, em média, os Sites chamam 10 arquivos JavaScript externos. O pior caso era um Site que chamava incríveis 54 arquivos JS. A solução é simples: juntar vários arquivos e diminuir o total. O ideal é isso ser feito dinamicamente pela aplicação ou em build time, para que não seja necessário fazer manualmente. Não existe um número ideal de arquivos JS, mas eu recomendo entre 3 e 5 arquivos, no máximo.

### #10 - Juntar arquivos CSS

O mesmo princípio vale para arquivos CSS. Nesse caso, recomendo 1 ou 2 arquivos externos. Na análise dos Sites, a média foi de 5 arquivos por página, sendo que o pior caso chegou a 24 arquivos CSS externos.

### #11 - Use Sprites

Aplicar a mesma ideia de juntar arquivos a imagens não é tão simples. As sprites são imagens que unem diversas outras em uma só, como essa usada no Google:

![](http://www.google.com/images/nav_logo83.png)

O problema é criar essas imagens, algo difícil de se automatizar, e de gerenciar o CSS necessário para posicionar cada pedaço no lugar certo. Há ideias para automatização como o [Sprite.me](http://spriteme.org/), o [SpriteMapper](http://yostudios.github.com/Spritemapper/), o [SpriteCow](http://www.spritecow.com/) e o [SmartSprites](http://csssprites.org/).

Ao criar sprites, muitos pontos precisam ser pensados: é difícil lidar com imagens que precisem de repetições. É preciso pensar em espaçamentos entre as imagens para facilitar o posicionamento no CSS, ao mesmo tempo que não se deixe a sprite com área muito grande, o que causaria um grande uso de memória no navegador. É preciso pensar na similaridade das imagens para que, ao juntá-las, não tenhamos uma palheta de cores maior e pouca compressão. Temos que decidir o formato da sprite (PNG? JPEG?) e como unificar as mesmas características de qualidade e compressão de diversas imagens.

É complicado fazer Sprites mas, essencial para uma otimização séria. Na análise dos Sites dos participantes da QCon, **mais da metade referencia 36 imagens ou mais**. No pior caso, 144 imagens foram incluídas em uma única página.

### #12 - Use Data URIs

Esse é um recurso perigoso mas extremamente útil. Assim como podemos embutir conteúdos de CSS e JS no meio do HTML quando conveniente, com as _[data URIs](http://en.wikipedia.org/wiki/Data_URI_scheme)_, podemos embutir imagens e outros elementos binários. Não é um recurso para ser abusado, já que aumenta o tamanho do HTML e não permite que a imagem seja cacheada e referenciada em lugares diferentes. É possível usar data URIs em arquivos CSS cacheáveis, prática mais recomendada.

A ideia é transformar os dados binários de uma imagem em base64 e colocar diretamente no src de uma imagem. Um problema é que só há suporte no IE a partir da versão 8, apesar de todos os outros browsers suportarem há bastante tempo. Isso não impede que se sirva versões diferentes do Site para browsers antigos, por exemplo. O Google Images é um exemplo emblemático de uso de [data URIs](http://en.wikipedia.org/wiki/Data_URI_scheme).

![](https://blog.caelum.com.br/wp-content/uploads/2011/09/Untitled2.jpg)

### #13 - Configure os headers

A estratégia aqui é permitir que recursos estáticos sejam [cacheados pelo navegador](http://code.google.com/speed/page-speed/docs/caching.html), evitando novos requests quando o usuário visitar a página novamente. Em poucos segundos é possível configurar o servidor para servir requests com valores apropriados de `Expires`, `Last-Modified`, `Cache-Control` e `ETag`.

No estudo dos Sites, descobri que ao acessar a mesma página duas vezes, o segundo acesso dispara, em média, 46 novos requests. E, ainda, 87% dos Sites disparam mais que 10 requests nesse segundo acesso, um forte indício de que pouco se usa o recurso de cache do navegador. Um valor razoável seria manter esse número em menos de 5 requests e, se possível, apenas 1 ou 2.

### #14 - Tire firulas do design

Polêmico, esse tópico propõe que o layout seja feito com performance em mente. Talvez muitos designers discordem, mas os números mostram que velocidade é mais importante para o usuário do que designs detalhistas e carregados.

Essa prática inclui diminuir a dependência do layout por imagens externas, evitar o uso de fontes externas, não usar Flash para design, entre outros.

## Performance é medida pela percepção do usuário

A percepção humana é muito menos racional do que imaginamos. E, na Web, isso reflete diretamente em como os usuários percebem a velocidade do Site (e credibilidade, atratividade etc). Há estudos que mostram que a percepção de tempo de carregamento dos usuários é, em média, 15% pior que a real. E, quando perguntados tempos depois sobre a performance de um Site, a lembrança costuma ser 35% pior que a velocidade real ([fonte](http://www.webperformancetoday.com/2010/06/15/everything-you-wanted-to-know-about-web-performance/)).

Na Web, o número de segundos para a página carregar e a nota do YSlow são métricas importantes, mas não são tudo. Performance Web tem tudo a ver com com Usabilidade e UX, e é o usuário final quem fala o quão rápido está um Site ou não.

Existem diversas técnicas de otimização que não afetam o tempo de carregamento da página e talvez não melhorem sua nota no YSlow. São técnicas para tornar a percepção do usuário mais favorável ao seu Site, dando a sensação de velocidade mesmo que o relógio diga o contrário.

### #15 - Especifique o tamanho das imagens

Fácil de implementar, essa prática tem um grande impacto visual para o usuário. Quando não especificamos o tamanho da imagem, o navegador não reserva um espaço na página para ela. O efeito é que, quando a imagem chega, ela é posicionada em seu lugar empurrando o restante do conteúdo. Isso dá a sensação de que os elementos da página estão se movendo e que ela ainda não está carregada. É uma péssima prática de usabilidade e dá a sensação de mais lerdeza.

Simplesmente [especificar o tamanho correto da imagem no width e height do HTML ou CSS](http://code.google.com/speed/page-speed/docs/rendering.html#SpecifyImageDimensions) faz com que o browser já reserve o espaço para a imagem mesmo antes de seu download acabar. Quando ela chega, o navegador não precisa abrir espaço e empurrar os outros elementos, dando a sensação de que a página está sendo carregada mais rapidamente. Além disso, ao evitar o reposicionamento de elementos, evita-se [reflows e repaints](http://www.phpied.com/rendering-repaint-reflowrelayout-restyle/) desnecessários.

### #16 - Coloque o JavaScript no fim

Código JavaScript, interno ou externo, bloqueia a renderização de tudo que vem abaixo dele. Em alguns browsers, bloqueia inclusive o download de componentes seguintes. Para evitar esse bloqueio, coloque o JavaScript o mais pra baixo que puder; se possível, logo antes de fechar o body.

O efeito mais comum de JavaScript mal posicionado é a tela ficar em branco durante muito tempo, adiando o início da renderização. O senso comum manda colocar os JavaScripts no head, o que causa o bloqueio da renderização da tela toda. Na análise dos Sites dos conferencistas, **a média de espera para o início da renderização foi de 4,3s**. O pior caso deixava o browser totalmente branco durante 25s. O Site da Caelum, por exemplo, inicia a renderização em aproximadamente 0,7s.

![](https://blog.caelum.com.br/wp-content/uploads/2011/09/Untitled3.jpg)

### #17 - Coloque o CSS no topo

O CSS também bloqueia a renderização da tela mas, neste caso, isso é melhor na maioria das vezes. Colocar o CSS para baixo causaria o efeito conhecido como _[FOUC - Flash of Unstyled Content](http://en.wikipedia.org/wiki/Flash_of_unstyled_content)_. A página é renderizada sem estilo e, quando o CSS acaba de baixar, ela é redesenhada com o estilo.

Embora isso faça com que a página comece a ser mostrada antes para o usuário, na maioria dos Sites, mostrar uma página sem estilos não tem valor algum para o usuário. O layout fica sem estrutura, sem organização, e o efeito é aparentar que a página está demorando mais para carregar. Por isso, [coloque o CSS no topo do documento](http://code.google.com/speed/page-speed/docs/rendering.html#PutCSSInHead), de preferência no head.

### #18 e #19 - Adie o carregamento do que puder e Abuse do carregamento assíncrono

Uma das técnicas avançadas mais discutidas ultimamente - e das mais eficazes - é o uso de carregamento assíncrono de componentes da página. Isso evita bloqueios na renderização e faz a página ficar mais responsiva. [Fiz um post só sobre esse assunto aqui no blog.](https://blog.caelum.com.br/otimizacoes-na-web-e-o-carregamento-assincrono/)

### #20 - Otimize o First-View e o Above the Fold Time

Como diz o ditado, a primeira impressão é a que fica. Isso significa otimizar ao máximo a primeira visita do usuário, aquele onde o cache do navegador vai estar vazio, aquela quando ele ainda não conhece seu Site, e aquele onde ele traz todas suas expectativas.

É preciso entender como a interação do usuário acontece, e priorizar os componentes e a ordem da renderização da página para esse primeiro contato. Há quem fale em uma nova métrica, o _Above the Fold Time_ - AFT - que mede o tempo de tempo de carregamento do início da página, "acima da dobra", aquela parte que o usuário vê sem fazer scroll. O [scroll infinito](http://www.infinite-scroll.com/) do Twitter é um exemplo disso; os dados mais para baixo só são carregados quando o usuário faz scroll até lá. Ou ainda a página de produtos da Amazon que deixa para carregar alguns componentes do fim da página só quando o usuário chega lá.

Mesmo sem usar técnicas avançadas como o scroll infinito, priorizar o AFT é pensar na ordem dos elementos no HTML para que as coisas que serão exibidas antes carreguem primeiro. É adiar aquele widget do Facebook que fica no fim da página para ser carregado só depois do onload, por exemplo. É prover um menu de opções que não dependa de JavaScript para funcionar, já que este será executado por último. No fim, é focar na primeira experiência do usuário e adiar tudo aquilo que não é necessário para isso.

### #21 - Design performático

Uma área que mereceria um post por si só é como o design da página pode afetar a percepção de velocidade do usuário. O Google Reader antigamente usava um fundo azul claro em sua barra lateral e resolveu experimentar trocar para branco. Quando perguntados, os usuários, em sua maioria, afirmaram que o "novo Site" era mais rápido que o anterior ([fonte](http://www.phpied.com/psychology-of-performance/)).

A _[Psicologia](http://www.phpied.com/psychology-of-performance/) da [Performance](http://velocityconf.com/velocity2010/public/schedule/detail/13019)_ envolve muitos tópicos de design, UX e usabilidade. Uma layout mais leve, uma arquitetura de informação mais simples, um _call-to-action_ mais direto influenciam diretamente em como os usuários percebem a performance.

## Bônus: mais tópicos

![](https://blog.caelum.com.br/wp-content/uploads/2011/09/Untitled4.jpg)

### #22 - Automatize

O segredo para um Site com performance consistente e durável é automatizar o processo. Faça um script que minifique JS e CSS, automatize a otimização das imagens, etc. Qualquer processo repetitivo deve ser automatizado, caso contrário será logo deixado de lado pela equipe.

### #23 - Use ferramentas de diagnóstico

Monitore suas páginas constantemente com ferramentas de diagnóstico. Há diversas disponíveis:

- Painéis de monitoramento dos próprios navegadores, como [Firebug](http://getfirebug.com), Chrome Developer Tools, [Opera Dragonfly](http://www.opera.com/dragonfly/), e [IE Dev Toolbar](http://www.microsoft.com/download/en/details.aspx?id=18359) já nos dão muitas informações.

- Fora isso, as clássicas extensões [YSlow](http://developer.yahoo.com/yslow/) e [PageSpeed](http://code.google.com/speed/page-speed/docs/extension.html) (disponíveis para Chrome e Firefox) apresentam relatórios completos com dicas e problemas de performance.

- O PageSpeed em particular possui [uma versão online](http://pagespeed.googlelabs.com/) muito fácil de usar.

- Outra ferramenta online fantática é a [WebPageTest.org](http://www.webpagetest.org), usada para gerar as estatísticas usadas nessa apresentação. Ela permite testar do IE6 ao IE10, além de Chrome e Firefox. Gera screenshots, gráficos, grava vídeos comparativos e provê uma análise com dicas de otimização. Além disso, [o framework é aberto e pode ser rodado internamente](https://sites.google.com/a/webpagetest.org/docs/private-instances) ou instalado em uma imagem da Amazon EC2.

- O [Google Analytics](http://analytics.google.com) pode ser configurado para [guardar estatísticas da velocidade do site](http://www.google.com/support/analyticshelp/bin/answer.py?hl=en&answer=1205784&topic=1282106). O interessante é usar essa capacidade para cruzar dados com outras métricas para descobrir os impactos da performance sobre os usuários da sua aplicação.

- O [Google Webmasters Tools](https://www.google.com/webmasters/tools/) mostra informações sobre a performance do site em comparação com os outros sites indexados pelo Google.

### #24 - Pré-carregue componentes

Em alguns casos, ao visitar uma página, você quase que certamente sabe qual será a próxima página visitada pelo usuário. Pense na home do Google que certamente será seguida por uma visita à página de resultados da busca. Ou ainda em um fluxo de fechamento das compras de uma loja virtual em vários passos.

Nessas situações, pode ser interessante que a primeira página pré-carregue elementos que serão utilizados na página seguinte. CSS, JavaScript e imagens podem ser pré-carregados via código, antecipando o download de componentes que tem grande chance de serem usados depois. Obviamente, essa prática só deve ser usada se você configurou os headers de cache apropriadamente, senão os componentes serão baixados duas vezes.

Essa prática tem um imenso ganho de velocidade ao visitar a segunda página. Deve-se, porém, tomar cuidado para que não se prejudique a performance da primeira. O ideal é fazer esses pré-carregamentos somente quando a página inicial terminar de carregar todos os seus componentes, possivelmente depois do onload da mesma.

### #25 - Escreva código eficiente

Em alguns raros casos o performance pode ser bastante afetada por más práticas de codificação de HTML, CSS ou JavaScript. Note, porém, que a maioria das técnicas mais eficazes costumam ser pouco influenciadas por micro otimizações de código; o problema costuma ser mais estrutural.

Porém, quando a aplicação é carregada, cheia de recursos ricos, o código pode ser otimizado. Há diversos [seletores CSS que deixam a renderização bem mais lenta](http://code.google.com/speed/page-speed/docs/rendering.html#UseEfficientCSSSelectors). [O alto número de elementos no DOM também diminui a performance](http://developer.yahoo.com/performance/rules.html#min_dom). [Usar iframes em excesso então é um quase um crime](http://developer.yahoo.com/performance/rules.html#iframes). E há também as práticas de [otimização de código e lógica JavaScript](http://www.phpied.com/extreme-javascript-optimization/), como quebrar longas tarefas em pedaços menores com WebWorkers ou setTimeout, evitando bloqueio da tela.

### #26 - Dispare logo o onload

O evento de onload é esperado por muitos scripts para que sua execução prossiga. Além disso, costuma ser a métrica usada para medir performance em ferramentas como Analytics e Webmasters Tools. Desde 2010, o Google usa a [velocidade do Site](http://googlewebmastercentral.blogspot.com/2010/04/using-site-speed-in-web-search-ranking.html) como uma das variáveis do rankeamento das buscas, e isso tem a ver com o onload. Depois do disparo do onload, o indicador de carregamento do navegador para de girar, dando a impressão de fim do carregamento.

Há vários motivos para se otimizar o tempo de onload, muitos explicados por [Steve Souders](http://stevesouders.com/) em [seus](http://www.amazon.com/High-Performance-Web-Sites-Essential/dp/0596529309/ref=sr_1_1?s=books&ie=UTF8&qid=1315598131&sr=1-1) [livros](http://www.amazon.com/Even-Faster-Web-Sites-Performance/dp/0596522304/ref=sr_1_1?s=books&ie=UTF8&qid=1315598155&sr=1-1). Abusar do carregamento assíncrono ajuda a implementar essa prática.

## Quebre as regras

Não se prenda a números exatos que sua ferramenta de diagnóstico dá. Quebre algumas regras quando elas se mostrarem melhores para o usuário. A técnica de pre-carregar componentes da página seguinte vai piorar sua nota no YSlow, pois o número de requests aumenta, o tamanho total aumenta; mas é uma técnica que sabemos que melhora a performance para o usuário.

Várias das outras regras podem ser quebradas em casos específicos. Há quem diga que home pages de portais deveriam embutir CSS e JS diretamente no HTML para economizar requests. Há quem pregue que o JavaScript não deve estar no final da página, mas logo depois do fold, já que não há muito problema em bloquear a renderização da parte não visível da página. Há situações onde você quer redimensionar imagens no HTML, como ao fazer um [design responsivo](http://www.alistapart.com/articles/responsive-web-design/).

**Não se prenda a números, foque na experiência do usuário.**

## Referências

- [Meus](https://blog.caelum.com.br/top-7-praticas-para-um-site-otimizado/) [posts](https://blog.caelum.com.br/otimizacoes-na-web-e-o-carregamento-assincrono/) anteriores aqui no blog sobre performance.

- Conjunto de práticas propostas pelo [Yahoo!](http://developer.yahoo.com/performance/rules.html) e [Google](http://code.google.com/speed/page-speed/docs/rules_intro.html).

- Livros do Steve Souders, [High Performance Web Sites](http://www.amazon.com/High-Performance-Web-Sites-Essential/dp/0596529309/ref=sr_1_1?s=books&ie=UTF8&qid=1315598131&sr=1-1) e [Even Faster Web Sites](http://www.amazon.com/Even-Faster-Web-Sites-Performance/dp/0596522304/ref=sr_1_1?s=books&ie=UTF8&qid=1315598155&sr=1-1).

- [Book of Speed](http://www.bookofspeed.com/), de Stoyan Stefanov e [seu blog](http://www.phpied.com/).

- Palestras muito boas do evento VelocityConf de [2011](http://velocityconf.com/velocity2011/public/schedule/proceedings), [2010](http://velocityconf.com/velocity2010/public/schedule/proceedings), [2009](http://velocityconf.com/velocity2009/public/schedule/proceedings) e anos anteriores.

- [PerfPlanet](http://www.perfplanet.com/), um agregador de blogs de excelentes autores sobre Web Performance.

- Excelente coleção de links da StrangeLoop em seu [Web Performance Hub](http://www.strangeloopnetworks.com/web-performance-optimization-hub/).

#### Interessado em Desenvolvimento e Otimizações Web? [Confira os nossos cursos de Front End!](http://www.caelum.com.br/cursos-web-front-end/)
