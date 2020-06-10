---
title: "Otimizações na Web e o carregamento assíncrono"
date: "2011-04-28"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

No fim de março, colocamos no ar a nova [Home page da Caelum](http://www.caelum.com.br/). Com mais conteúdo relevante, integração com nosso [Blog](https://blog.caelum.com.br/), [Twitter](https://twitter.com/caelum) e [Facebook](https://www.facebook.com/caelumbr), a página passou a ser um **desafio de performance**.

Somos bitolados por [otimizações Web](https://blog.caelum.com.br/top-7-praticas-para-um-site-otimizado/) aqui na Caelum então muitas otimizações já estavam integradas ao Site. A primeira versão, logo após a fase de design e codificação, tirava uma invejável nota **90** no PageSpeed do Google. Mas sua performance não era aceitável para nós. Com um pouco mais de esforço e técnicas mais avançadas, conseguimos o seguinte resultado (antes e depois):

<iframe src="http://player.vimeo.com/video/22112582" width="560" height="252" frameborder="0"></iframe>

[Vídeo Antes e Depois das Otimizações na Home da Caelum](http://vimeo.com/22112582).  
[Veja também quadro a quadro a brutal diferença entre as duas páginas.](http://www.webpagetest.org/video/compare.php?tests=110402_WJ_A7C0-r%3A1-c%3A0,110412_PF_CRBZ-r%3A2-c%3A0&thumbSize=200&ival=100)

Após as modificações, a nota no PageSpeed até subiu um pouco, para [**95**](http://pagespeed.googlelabs.com/#url=http_3A_2F_2Fwww.caelum.com.br_2F&mobile=false), mas o impacto para o usuário final é muito maior. Ao preocupar-se com otimizações Web, o uso de ferramentas como o PageSpeed ou YSlow é muito importante, mas ainda mais crucial é executar testes frequentes e analisar os tempos. A ferramenta [WebPageTest](http://www.webpagetest.org/), usada no vídeo acima, é excelente para esse fim.

O último post aqui do Blog sobre [otimização de performance na Web](https://blog.caelum.com.br/top-7-praticas-para-um-site-otimizado/) mostou 7 práticas simples e altamente recomendadas para melhorar sensivelmente a performance de qualquer página Web, como usar GZIP, agrupar e comprimir CSS e JavaScript, colocar CSS no topo e JavaScript embaixo da página, entre outras. Mas há muitas outras práticas interessantes, como o carregamento assíncrono de JavaScript e componentes não essenciais à página.

## Carregamento assíncrono de JavaScript

![](https://blog.caelum.com.br/wp-content/uploads/2011/04/High-Speed-Chinese-Train.jpg)

Colocar arquivos JavaScript no `<head>` ou espalhado no meio do HTML é má prática há anos. A boa prática é sempre jogar para antes do `</body>`, uma das [regras principais analisadas pelo YSlow](http://developer.yahoo.com/performance/rules.html#js_bottom). Mas será que jogar o JavaScript para o fim da página é suficiente?

**Muitos navegadores não suportam download em paralelo de arquivos JavaScript** quando usamos a tag `<script>`, independente da posição em que apareça. E não apenas navegadores antigos como IE6 ou 7; Chrome, Opera, Firefox3 e outros engrossam essa lista. [Steve Souders](http://stevesouders.com/), papa de otimizações Web, sugeriu diversas técnicas em seu livro [_Even faster Web Sites_](http://www.amazon.com/Even-Faster-Web-Sites-Performance/dp/0596522304/ref=sr_1_1?ie=UTF8&s=books&qid=1302856659&sr=8-1). Começaram a surgir então ideias e [bibliotecas com objetivo de carregar JavaScript em paralelo assincronamente](http://net.tutsplus.com/articles/web-roundups/for-your-script-loading-needs/), como [LABjs](http://labjs.com/), [HeadJS](http://headjs.com/) e [ControlJS](http://stevesouders.com/controljs/), do próprio Steve.

A página da Caelum usa o **LABjs**, mas independentemente de framework, o importante é carregar assincronamente seus arquivos JavaScript, principalmente se vários arquivos são usados na página. Além do download paralelo, o evento _onload_ dispara mais cedo, executando os callbacks associados a ele e dando uma medida melhor de performance da página.

É preciso tomar cuidado, porém, com a ordem de execução dos scripts caso haja dependências entre eles (muito comum ao usar um framework como JQuery). As ferramentas citadas possuem suporte para manter a ordem de execução. Um outro problema é quando há uso do `document.write`, algo que é má prática há muito tempo mas infelizmente ainda muito usado.

Usar o LABjs é bastante simples:

\[javascript\] // carrega 3 scripts em paralelo mas mantém ordem de execução $LAB.script('jquery.js').wait() .script('plugin.jquery.js').wait() .script('app.js');

// carrega e executa outro script em paralelo, com callback $LAB.script('sem-dependencias.js').wait(function() { alert('Callback executando quando script carrega'); }); \[/javascript\]

O HTML5 especificou um novo atributo [`async`](http://www.w3schools.com/html5/tag_script.asp) na tag `<script>` mas poucos browsers suportam. O IE possui um atributo proprietário [`defer`](http://www.w3schools.com/html5/att_script_defer.asp) há muito tempo, com propósito parecido. Enquanto não há uma solução padrão e portável, **o uso de algum framework de carregamento assíncrono é recomendado**.

## Adiando o carregamento de conteúdo secundário

Além de JavaScripts assíncronos, é possível melhorar bastante a performance deixando para carregar certos componentes mais tarde, apenas quando necessários. A [nova home da Caelum](http://www.caelum.com.br/) tem fotos grandes com chamadas principais rotacionando. É um efeito muito comum atualmente, mas um grande desafio de performance, já que essas imagens costumam ser grandes e pesadas.

Nossa primeira implementação consistia em um HTML simples com tags `<img>` apontando para cada imagem, um pouco de CSS para mostrar apenas uma imagem por vez e um código JQuery para alternar as imagens com um efeito legal. É uma implementação usada por muitos Sites e plugins do JQuery.

Mas colocar as `<img>` direto no HTML fazia o navegador carregar todas essas imagens conforme ia lendo o HTML, mesmo que 3 das 4 imagens só fossem aparecer para o usuário muito tempo depois. Era gasto um tempo precioso do carregamento da página, que podia ser usado carregando outros componentes mais essenciais para a renderização inicial, como outras imagens do layout e scripts.

**A solução foi carregar assincronamente, via JavaScript, as imagens secundárias**, deixando inicialmente apenas a primeira imagem com HTML normal. Usamos os [data attributes do HTML5](http://ejohn.org/blog/html-5-data-attributes/) para criar atributos próprios no HTML que referenciam os endereços das imagens secundárias:

\[html\] <ul> <li> <p>Conheça os cursos de Java da Caelum</p> <img alt="Cursos Java" src="banner\_01.jpg" width="960" height="330" /> </li> <li data-img-src="banner\_02.jpg"> <p>Veja os projetos da Caelum</p> </li> <li data-img-src="banner\_03.jpg"> <p>Baixe as apostilas gratuitas</p> </li> </ul> \[/html\]

Repare como apenas o primeiro banner possui a `<img>` direto no HTML. Os demais apenas declaram os caminhos em atributos `data-` que não são interpretados pelo navegador. Uma função JavaScript pode, então, ler esses valores e gerar as tags `<img>` de forma assíncrona, depois que a página foi carregada:

\[javascript\] $(function() { setTimeout(function(){ $('li\[data-img-src\]').each(function(){ var src = $(this).attr('data-img-src'); $('<img>').attr('src', src).appendTo('ul'); }); }, 600); }); \[/javascript\]

Usando JQuery, selecionamos todos os `<li>` que possuem o atributo `data-img-src`. Criamos, então, um novo elemento `<img>` com o `src` apontando para o endereço da imagem. Repare que tentamos adiar esse carregamento o máximo possível, já que a segunda imagem só aparecerá para o usuário depois de vários segundos. No código acima, agendamos o script para rodar 600 milissegundos após o carregamento da página.

Observe no gráfico de conexões HTTP ao longo do tempo como o primeiro banner é carregado no início junto com o restante da página e os demais são carregados bem depois:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/04/WebPagetest-Test-Details-Dulles-_-www.caelum.com_.br_-04_15_11-18_59_33.png "Gráfico de conexões do caelum.com.br")](http://www.webpagetest.org/result/110415_4C_DVKV/3/details/)

Uma preocupação possível com essa prática é com usuários com JavaScript desabilitado ou navegadores limitados. É preciso pensar bem nesse caso e oferecer uma boa experiência para o usuário em todas as situações. Mas as imagens rotativas dependem de JavaScript para funcionar; logo, mesmo sem o carregamento assíncrono das imagens secundárias, o efeito não funcionaria e apenas a primeira imagem (em HTML e CSS puros) seria mostrada. Portanto, não há impacto para o usuário em usar a solução JavaScript para carregamento das imagens. Um impacto possível é que os buscadores não enxergam mais as imagens secundárias e, portanto, estas não serão indexadas. Não é um problema no nosso caso, mas pode ser um detalhe importante em outros cenários.

## Widgets externos assincronamente

**Outro bom exemplo para carregamento assíncrono é dos widgets do Facebook**, usados na home da Caelum com um _Like Button_ no topo e um _Like Box_ no corpo da página. Onipresentes hoje na Web, esses widgets são importados na página com um `<iframe>`. O `<iframe>` tem a vantagem de carregar paralelamente com a página, mas ainda trava o onload da página até que acabe de carregar - e os widgets do Facebook são gigantescos, com mais de [50 requests, 250 KB e 5 segundos](http://www.webpagetest.org/result/110415_M8_DVPK/1/domains/) para carregar mesmo em navegadores modernos.

Steve Souders mostrou que a tag `<iframe>` é o [elemento mais caro a ser criado no DOM](http://stevesouders.com/efws/costofelements.php), ordens de magnitude mais lento, mesmo vazio. Mas o principal problema é o onload da nossa página passar a depender do onload do widget, que é grande e lento. **Devemos otimizar o tempo que o onload dispara**, pois isso dispara os callbacks de onload (bastante comuns) e porque o indicador de carregamento do navegador para de girar após o onload (dando a sensação de rapidez pro usuário).

**A melhor estratégia é carregar o `<iframe>` após o onload via JavaScript**, ainda mais se não é algo crítico, como o widget do Facebook. O código é bastante simples:

\[javascript\] $(window).load(function(){ $('#facebook\_like\_box') .html('<iframe src="https://www.facebook.com/plugin...></iframe>'); }); \[/javascript\]

Esse carregamento assíncrono do Facebook foi o responsável pela maior parte da otimização final mostrada no vídeo do início do post. Repare no gráfico a seguir como o carregamento todo dos widgets é feito apenas após o onload e, apesar de grande e lento, não afeta a performance do restante da página:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/04/Connection-View-waterfall-diagram-2.png "Facebook carregando depois do onload")](http://www.webpagetest.org/result/110415_M8_DVPK/3/details/)

Como o widget demora bastante para carregar, o resto da página (já bastante otimizada) aparecerá rapidamente mas o widget bem depois. Para minimizar esse efeito, o header da página da Caelum carrega primeiro um botão like de mentira, com uma imagem simples copiando o visual do widget do Facebook. Quando o widget acaba de carregar, ele é inserido no lugar dessa imagem falsa com precisão, a ponto de ser quase imperceptível para o usuário. Se o usuário clicar na imagem falsa antes do widget carregar, será levado para o Facebook da Caelum; mesmo comportamento se o JavaScript estiver desabilitado. É um truque simples que traz a sensação de alta performance ao carregar a imagem falsa logo, apesar de ser tudo assíncrono e demorado.

## Mais ideias

Há muitas outras ideias para carregamento assíncrono. Os desenvolvedores do SlideShare, por exemplo, recentemente mostraram o impacto de [carregar as imagens lazy apenas quando o usuário fizer scroll para vê-las](http://engineering.slideshare.net/2011/03/faster-page-loads-with-image-lazy-loading/). Há inclusive quem defenda uma nova métrica, o [_Above The Fold (AFT) Time_ apresentado na Velocity Online 2011 mês passado](http://www.webperformancetoday.com/2011/03/16/above-the-fold-time-web-performance-measurement/), que leve em conta apenas os objetos necessários para a primeira impressão do usuário.

**O importante é otimizar o tempo de carregamento inicial da página, adiando tudo aquilo que não é essencial para o usuário visualizar de primeiro.**
