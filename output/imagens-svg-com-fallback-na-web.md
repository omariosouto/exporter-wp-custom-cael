---
title: "Imagens SVG com fallback na Web"
date: "2014-05-09"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

SVG é o formato vetorial para imagens mais famoso da Web. Existe há muitos anos mas só nos últimos anos ganhou maiores adeptos. Existem vários motivos pra se usar vetores, sendo o principal a independência de tamanho e resolução. Algo particularmente muito útil em design responsivo e aparelhos com diferentes densidades de pixels.

Usar SVG hoje é bastante simples, basta uma tag IMG comum:

\[code language="html"\] <img src="icone.svg"> \[/code\]

Ou, se usar CSS, basta o background-image correto:

\[code language="css"\] .conteudo { background-image: url(icone.svg); } \[/code\]

O único problema desse mundo ideal é justo o _suporte nos navegadores_. SVG tem excelente suporte, de todos os browsers modernos. O IE desde a versão 9 já suporta. As grandes exceções, porém, são IE8 e anteriores, e Android 2.x. São navegadores mais antigos que não se aplicam a todos os públicos, então talvez não afete tanto seu caso - no site da Caelum, por exemplo, com público mais atualizado, esses navegadores são bastante inexpressivos.

Mas muitos outros sites têm requisitos diferentes. Recentemente lançamos a **MusicDot**, uma escola de música online, com [cursos online de teclado, piano, violão etc](http://www.musicdot.com.br). O público é bastante geral - músicos, amadores, interessados por música etc. E muitos desses alunos usam navegadores antigos.

No [site do MusicDot](http://www.musicdot.com.br/?home), usamos muitos ícones e quisemos usar SVG. Mas preocupados com fallback caso o navegador seja incompatível.

Usando CSS, a solução mais comum é usar _Modernizr_. Ele coloca algumas classes a mais no elemento raiz da página que nos permitem detectar o suporte a SVG e, assim, carregar a imagem correta:

\[code language="css"\] .svg .conteudo { background-image: url(logo-musicdot.svg); } .no-svg .conteudo { background-image: url(logo-musicdot.png); } \[/code\]

No caso da tag IMG, não há uma solução tão elegante. Algumas pessoas defendem o uso de um SRC dinâmico, testando antes o suporte a SVG para aí atribuir o SRC adequado. O ruim dessa solução é que ela compromete a performance, já que adia o carregamento da imagem até essa resolução ser feita. E, se pensarmos que apenas navegadores muito antigos não suportam SVG, seria uma solução que sacrifica a maioria, que tem navegadores modernos.

Uma solução simples e intermediária é trabalhar com o onerror na imagem:

\[code language="html"\] <img src="musicdot.svg" onerror="this.src='musicdot.png'"> \[/code\]

![](http://www.musicdot.com.br/images/musicdot/logo-footer.svg)

Ele tenta baixar o ícone SVG e, no caso de problemas, como falta de suporte, baixa a versão PNG. Nos poucos navegadores ainda sem suporte (menos de 5% da população geral), haverá 2 requests, desperdiçando um pouco a banda. Mas parece uma solução justa que prioriza a maioria dos navegadores compatíveis, sem deixar os antigos na mão.

É essa solução que usamos no novo site do MusicDot e em outros projetos nossos. E você, o que acha? Tem usado SVG já em produção? De que maneiras?
