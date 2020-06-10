---
title: "CSS3 e o futuro da Web"
date: "2010-02-18"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O mundo do desenvolvimento Web passa atualmente por grandes mudanças. Com as aplicações Web exigindo cada vez mais dos navegadores, uma verdadeira guerra está sendo travada no _client-side_ da Web. O até então onipresente Adobe Flash vem recebendo [críticas](http://www.informationweek.com/blog/main/archives/2008/03/jobs_flash_not.html "Steve Jobs fala que Adobe é preguiçosa") e [mais](http://blog.mozilla.com/blog/2010/01/27/firefox-for-maemo-rc3-available-now/ "Mozilla desabilita suporte a Flash no Nokia N900") [críticas](http://ln.hixie.ch/?start=1265967771&count=1 "Adobe sabota votação do HTML5"). Com suas próprias alternativas, [Microsoft](http://silverlight.net/ "Silverlight"), [Oracle](http://www.infoq.com/br/news/2010/02/javafx-continua-ativo "JavaFX") e [Google](http://code.google.com/apis/o3d/ "O3D") tentam entrar nesse mercado. E, ainda parecendo velhos e ultrapassados em comparação a tudo isso, estão **HTML**, **CSS** e **JavaScript**.

[![](https://blog.caelum.com.br/wp-content/uploads/2010/02/2b1ab_Faceook_html5-css3-300x205.jpg "2b1ab_Faceook_html5-css3")](https://blog.caelum.com.br/wp-content/uploads/2010/02/2b1ab_Faceook_html5-css3.jpeg)

Mas, se depender dos novos **HTML5** e **CSS3**, o cenário será bem diferente no futuro. Com o grande apelo de serem padrões abertos implementados nativamente nos navegadores, essas novas especificações prometem simplificar bastante o desenvolvimento de aplicações ricas no _client-side_ sem o uso de plugins.

Só para falar do CSS3, assunto desse post, podemos citar entre suas novas capacidades de formatação visual, as queridas [bordas redondas](http://www.css3.info/preview/rounded-border/), o uso de [sombras](http://www.css3.info/preview/text-shadow/), [múltiplos backgrounds](http://www.css3.info/preview/multiple-backgrounds/), [layouts multi-colunas](http://www.css3.info/preview/multi-column-layout/), [novos seletores](http://www.css3.info/preview/attribute-selectors/), uso de [fontes externas](http://www.css3.info/preview/web-fonts-with-font-face/), [transformações 2D e 3D e animações](http://www.the-art-of-web.com/css/css-animation/). São recursos que facilitam bastante a tarefa do WebDesigner ao criar interfaces ricas e bonitas.

Embora não seja tão recente assim - o CSS3 data de [antes de 2001](http://www.w3.org/TR/css3-roadmap/ "CSS3 Roadmap") -, o suporte nos navegadores ainda é bem precário. Firefox, Safari, Chrome e Opera [lideram o movimento](http://www.quirksmode.org/css/contents.html "Suporte CSS nos navegadores"), mas ainda estão longe de implementar todos os recursos. O IE tem algumas poucas coisas implementadas em sua [versão 8](http://www.css3.info/css3-features-in-ie8/) e uma promessa dos desenvolvedores de melhorar um pouco mais na [futura versão 9](http://blogs.msdn.com/ie/archive/2009/11/18/an-early-look-at-ie9-for-developers.aspx).

Para demonstrar o uso de alguns recursos do CSS3, recriamos o logotipo da Caelum inteiramente usando recursos do CSS3, como _border-radius_, pseudo-elementos _:after_ e _:before_ e _@font-face_. O HTML da página é simplesmente: `  <div class="caelum">     <strong>Caelum</strong>     <em>Ensino e Inovação</em>   </div>`

E, sem usar _nenhuma_ imagem (nem JavaScript/canvas/SVG), desenhamos o logo da Caelum. [Veja a demonstração](http://sergiolopes.github.com/css3-experimentos/logo-caelum-css3/caelum-css3.html "Logo da Caelum com CSS3 puro") usando alguma versão recente do Firefox (3.5+), Safari/Chrome ou Opera (10.50+) e veja também o [código CSS](http://github.com/sergiolopes/css3-experimentos/blob/gh-pages/logo-caelum-css3/estilo.css). E, por ser tudo CSS, sem imagens, podemos mudar o tamanho do logo a vontade, como mostra [esse outro exemplo](http://sergiolopes.github.com/css3-experimentos/logo-caelum-css3/caelum-css3-tamanhos.html).

![](http://www.caelum.com.br/imagens/cursos/wd43-icon.gif)CSS3, HTML5 e outros tópicos da "nova Web" são também assuntos do novo treinamento da Caelum, [WD-43: Desenvolvimento Web com HTML, CSS e JavaScript](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/). O CSS3 é peça importante no futuro da Web. Seus diversos recursos mudarão a forma como fazemos WebDesign.
