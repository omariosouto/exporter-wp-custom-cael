---
title: "As 7 práticas para um site otimizado"
date: "2010-07-29"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Todo mundo gosta de sites rápidos. Seus primos não sabem dizer se você tem um arquitetura escalável, se seu banco NoSQL é mais robusto ou se é melhor usar Web Services SOAP ou REST. Mas eles sabem dizer duas informações com precisão: se seu site é bonito e se ele é rápido. **Performance**, medida pelo usuário, é o que te diz se a velocidade de resposta da sua aplicação é aceitável. E, assim como usuários não se cansam de bordas redondas e sombrinhas bonitas, sempre haverá otimizações possíveis para tornar seu site mais rápido.

#### \[Update\] Interessado em Otimizações Web na prática com truques do básico ao avançado? [Confira a grade do futuro curso online de otimizações da Caelum!](http://www.caelum.com.br/curso/online/otimizacoes-web/)

Inspirado pela recente edição 2010 da [Velocity Conference](http://en.oreilly.com/velocity2010) organizada pela O'Reilly e por uma [palestra recente que dei com o Alberto Souza aqui na Caelum](https://blog.caelum.com.br/ultimos-aprendizados-e-inovacoes-na-caelum/), além de discussões que aparecem no [nosso curso que trata de HTML, CSS e Javascript](http://www.caelum.com.br/curso/wd-43-desenvolvimento-web-html-css-javascript/), selecionei um punhado de dicas para deixar qualquer site ou aplicação Web mais rápido em instantes. Meu TOP 7 não pretende ser uma lista completa e nem detalhada de boas práticas. É mais um guia rápido sobre tudo o que você deveria fazer em qualquer site antes de perguntar a opinião dos seus primos - ou do seu chefe, ou de qualquer usuário. Eis então:

![](https://blog.caelum.com.br/wp-content/uploads/2010/07/Train+series+-+Motion+blur+of+a+fast+moving+train.-e1280395129893.jpeg)

### TOP 7 das Otimizações na Web:

  

**0\. [Habilite GZIP](http://www.brunopereira.org/otimizacao/2010/06/20/acelerando-seu-site-parte1-compressao/) em todas as suas páginas**. Se você ainda não fez isso, pare imediatamente de ler esse post e faça. É o passo zero, nem vou contá-lo. Dicas de como fazer no [Tomcat](http://tomcat.apache.org/tomcat-7.0-doc/config/http.html#Standard_Implementation), [Jetty](http://blog.max.berger.name/2010/01/jetty-7-gzip-filter.html), [Apache](http://httpd.apache.org/docs/2.0/mod/mod_deflate.html), [IIS](http://www.microsoft.com/technet/prodtechnol/WindowsServer2003/Library/IIS/25d2170b-09c0-45fd-8da4-898cf9a7d568.mspx?mfr=true).

**1\. Agrupe arquivos JavaScript/CSS**. 90% do trabalho de otimização consiste em diminuir o número de requests feito na página e o peso de cada um deles. Você usa JQuery (ou qualquer outro framework) com JQuery UI e mais meia dúzia de plugins, sem contar o código JS da sua aplicação? Junte tudo no menor número de arquivos possíveis para evitar muitos requests. Mesma coisa com CSS, um arquivo com tudo basta. Mas gosta de manter as coisas organizadas ao invés de escrever um arquivo com milhares de linhas? Então junte essas coisas dinamicamente como faz [o pessoal do Digg](http://cotnet.diggstatic.com/js/loader/464/JS_Libraries,jquery%7CClass%7Canalytics%7Clightbox%7Clabel%7Cjquery-dom%7Cjquery-cookie) ou faça o serviço no build da aplicação como preferimos [aqui na Caelum](http://www.caelum.com.br/css/caelum.package.css).

**2\. Comprima o JavaScript/CSS**. Você escreve JS/CSS elegante, bem documentado, com código organizado e variáveis de nomes legíveis. Mas seus primos não se importa com isso, eles querem um site rápido e isso significa não trafegar no request bytes e mais bytes de documentação ou de código legível. Comprima todo o seu código JavaScript e CSS usando o [YUI Compressor](http://developer.yahoo.com/yui/compressor/) ou o [Google Closure Compiler](http://code.google.com/closure/compiler/). Faça isso em build time ou dinamicamente.

**3\. Otimize suas imagens com Smush-it**. Nem todos os KB de suas imagens são necessários para o cliente. Arquivos JPEG possuem uma série de metadados e PNGs possuem palhetas de cores. Remova vários KBs desnecessários de suas imagens usando o [Yahoo Smush-it](http://www.smushit.com/ysmush.it/) em todas elas.

**4\. Coloque CSS no topo e JavaScript embaixo**. Simples assim. Referencie os seus CSS no <head> com <link> para evitar o [flash effect](http://en.wikipedia.org/wiki/Flash_of_unstyled_content), e coloque seus scripts logo antes do fechamento do <body> para não atrasar a renderização da tela.

**5\. Não redimensione imagens no HTML**. Não use os atributos _width_ e _height_ das imagens para redimensionar seu tamanho. Sirva as imagens já com tamanho certo e otimizadas. Mas coloque sempre o _width_ e _height_ de todas as imagens para ajudar o browser nos cálculos da renderização da página.

**6\. Configure o Expires corretamente**. Use o cache do navegador para prover uma melhor experiência ao usuário no segundo request - seja no retorno ao site ou navegando para a próxima página. Permita também que Proxies entre seu servidor e o cliente cacheiem elementos do seu site para evitar um download demorado. Configure o [header de Expires](http://developer.yahoo.com/performance/rules.html#expires) de todo seu conteúdo estático (JS, CSS, imagens) com alguns dias de duração.

**7\. Use YSlow e PageSpeed**. Depois que você fez todas as otimizações anteriores, instale o [YSlow](http://developer.yahoo.com/yslow/) e o [PageSpeed](http://code.google.com/speed/page-speed/) para um diagnóstico detalhado de seu site. Monitore sua performance constantemente e vá implementando sempre novas otimizações. Seus usuários agradecem.

  

Esse **TOP 7** são todas as otimizações que você deveria ter feito _ontem_ em qualquer site seu antes de começar a falar em performance. Há muitas outras práticas, algumas mais complicadas, como [CSS Sprites](http://spriteme.org/), [usar CDNs](http://developer.yahoo.com/performance/rules.html#cdn), [otimizar seus cookies](http://code.google.com/speed/page-speed/docs/request.html#MinimizeRequestSize), fazer [preload](http://developer.yahoo.com/performance/rules.html#preload) e [postload](http://developer.yahoo.com/yui/imageloader/) de conteúdo, ou [comprimir seu HTML](http://code.google.com/p/htmlcompressor/). Dependendo da tecnologia que você usa no seu sistema, você terá maior ou menor facilidade de aplicar todas essas práticas: no JSF e ASP.NET você terá benefícios para desenhar sua interface, mas muito mais trabalho de fazer esse ajuste fino; com Struts, Rails, ASP.NET MVC, VRaptor e frameworks action based, será o contrário.

**Bônus: Onde ir depois?**

- [Práticas de performance do pessoal do Yahoo YSlow](http://developer.yahoo.com/performance/rules.html)
- [Boas práticas do pessoal do Google PageSpeed](http://code.google.com/speed/page-speed/docs/rules_intro.html)
- [Slides e vídeos das palestras do Velocity 2010 com o que há de mais novo em discussão](http://en.oreilly.com/velocity2010)

E você, que outras práticas aplica no seu website?

#### \[Update\] Interessado em Otimizações Web na prática com truques do básico ao avançado? [Confira o novo curso online de otimizações da Caelum!](http://www.caelum.com.br/curso/online/otimizacoes-web/)
