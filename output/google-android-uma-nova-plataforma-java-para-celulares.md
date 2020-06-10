---
title: "Google Android: uma nova plataforma Java para celulares?"
date: "2010-03-23"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![android google](http://upload.wikimedia.org/wikipedia/en/a/a5/Android-logo.jpg) O [Google Android](http://developer.android.com/) foi criado como um sistema operacional para celulares, tendo seu primeiro lançamento no mercado em setembro de 2008. Como o mercado o tem recebido?

A plataforma Android permite programar utilizando a linguagem Java, porém ela não roda bytecodes no padrão da JVM. O plugin do Android para Eclipse faz uma conversão do bytecode padrão JVM para o formato do Android, permitindo que possamos usar todo o poder da IDE sem nos preocupar com detalhes do bytecode.

Mas por que o Google não optou pelo Java ME? Por que não pelo padrão de bytecode da JVM?

Apesar do código da JVM da Sun hoje ser GPL2, os termos de licença são um pouco diferentes para o Java ME. Com o objetivo de poder lançar o Android como opensource e para não ter de submeter pedidos para o JCP (tendo em vista o longo tempo que se leva entre uma versão e outra do MIDP) [o Google optou por usar a máquina virtual Dalvik, dessa forma se distanciando do JCP e Sun](http://www.betaversion.org/~stefano/linotype/news/110/), além dos problemas legais, mas sem se afastar da enorme quantidade de desenvolvedores que, como nós, já trabalham com Java e gostam da linguagem.

Esse truque também esquenta o mercado a favor Android em relação ao IPhone: há uma quantidade muito maior de desenvolvedores que conhecem Java comparada com a quantidade dos conhecedores de Objective C.

Não é por acaso que o Motorola Droid vendeu 1 milhão de unidades mais rápido que o iPhone, e hoje [são vendidos mais de 60 mil celulares com Android **por dia**](http://www.mobilecrunch.com/2010/02/16/google-now-shipping-60000-android-handsets-per-day/), quase 22 milhões por ano, [ameaçando passar o IPhone antes de 2012](http://www.computerworld.com/s/article/9139026/Android_to_grab_No._2_spot_by_2012_says_Gartner).

E não é apenas no campo do celular que o Android tem obtido êxito: tablets, netbooks, máquinas digitais, [televisão](http://www.nytimes.com/2010/03/18/technology/18webtv.html) e até mesmo [carros que usam a plataforma do Google](http://thenextweb.com/mobile/2010/03/19/worlds-androidequipped-car-enters-production/) estão começando a aparecer, fazendo com que ela atinja mercados que o Java ME não conseguiu grande penetração, e quem sabe o Android acabe [convergindo com o Google ChromeOS](http://news.cnet.com/8301-30684_3-10402653-265.html).

[![curso android](http://www.caelum.com.br/imagens/cursos/fj57-icon.png)](http://www.caelum.com.br/curso/fj-57-desenvolvimento-google-android/) A plataforma do Google permite que tenhamos todo o poder da linguagem Java, e, somado às APIS específicas do Android, podemos utilizar o GPS, interferir em chamadas, usar o acelerometro, etc, permitindo criar aplicações completas, como vemos no [curso de Android](http://www.caelum.com.br/curso/fj-57-desenvolvimento-google-android/).

O Android é uma plataforma completa para o desenvolvimento de aplicações dos pequenos dispositivos. E com a diferença (vantagem? desvantagem?) de não ter um comitê para especificar as novas versões: updates são lançados com frequência, e já estamos na versão 2.1. Será que finalmente temos alguém para competir com a onipresença do Symbian?
