---
title: "TV Digital, Java e Brasil"
date: "2009-03-10"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A história da plataforma Java é no mínimo curiosa. Começou a ser pensada por um pequeno grupo dentro da Sun, o _Green Team_ - liderado por James Gosling - que tinha por objetivo levar a computação a pequenos dispositivos, possibilitando a criação de aplicações embarcadas e portáveis. Nesta época, a linguagem ainda se chamava _Oak_.

![Primeiro dispositivo de demonstração da plataforma Java](https://blog.caelum.com.br/wp-content/uploads/2009/03/star7.jpg "star7")

Primeiro dispositivo de demonstração da plataforma Java

O mais curioso é ver que uma das [primeiras motivações](http://java.sun.com/features/1998/05/birthday.html) para a plataforma era ser embarcada em [set-top boxes](http://pt.wikipedia.org/wiki/Set-top_box). Java foi criado por um motivo (aplicações embarcadas), foi lançado por outro (Internet, Java Applets) e só foi fazer sucesso mesmo por um outro motivo: aplicações que rodam no servidor!

Depois de tantos anos, quem diria que Java seria uma das principais plataformas para desenvolvimento de aplicações embarcadas em pequenos dispositivos, com o Java ME? Quem diria que Java seria a principal plataforma para desenvolvimento de aplicações de interatividade para a TV Digital?

Hoje pelo mundo, existem diversos padrões de middleware para execução de aplicações para a TV Digital. Dos principais padrões, boa parte suporta aplicações escritas em algum sabor de XML, como o [DVB-HTML](http://en.wikipedia.org/wiki/DVB-HTML), o [BML](http://en.wikipedia.org/wiki/Broadcast_Markup_Language) e o [Ginga-NCL brasileiro](http://www.gingancl.org.br/). A outra grande parte das especificações deriva do JavaTV e suporta aplicações escritas em Java, como o [Globally Executable MHP](http://www.mhp.org/) (GEM) e o [Ginga-J](http://ginga.org.br/).

![ginga](https://blog.caelum.com.br/wp-content/uploads/2009/03/ginga.png "ginga")

No Brasil, está sendo proposto um middleware que integre as duas abordagens, chamadas declarativa e procedural, com o Ginga-NCL e o Ginga-J juntos constituindo o middleware Ginga. A parte “declarativa” - Ginga-NCL - já foi aberta ao público há bastante tempo, porém o Ginga-J veio sofrendo problemas com royalties relativos ao GEM para abrir seu código fonte.

Em um acordo com o governo brasileiro, a Sun criou uma alternativa ao GEM, livre de royalties. É assim que nasce o projeto [Java DTV](http://www.robglidden.com/2009/01/royalty-free-java-dtv/), que teve a primeira versão lançada em dezembro, mas foi relançado agora em fevereiro, incorporando mudanças sugeridas pelo [fórum SBTVD](http://www.forumsbtvd.org.br/). Isso permite que logo tenhamos uma nova versão opensource do Ginga-J; o que facilitaria o trabalho dos fabricantes de set-top box para incluir o Ginga em seus dispositivos. Além disso, o Ginga-J opensource diminui muito a barreira para que nós, desenvolvedores Java, possamos testar a plataforma e aprender como desenvolver aplicações para a TV Digital de acordo com os padrões brasileiros.

O potencial de mercado para aplicações de TV Digital interativas no Brasil é imenso. Tenho a sensação de que no Brasil a TV aberta tem um papel muito mais importante (e influente) na vida das pessoas. Se as aplicações interativas vão mesmo dar certo dependerá de um monte de fatores; só o futuro nos dirá. Mas as discussões já começaram e você pode ter certeza que tem bastante gente de olho nas oportunidades.

Que tal imaginar uma aplicação (em Java) que mostre as estatísticas em jogos de futebol? Ou que tal votar no paredão do Big Brother pela própria TV ao invés de precisar usar o telefone? Que tal ainda, nossas mães poderem comprar um daqueles pingentes de terceiro olho enquanto assistem a [novela da globo](http://caminhodasindias.globo.com/)? Que tal propagandas direcionadas a cada espectador, algo como um Google Adsense nas TVs?

![Terceiro Olho - Caminho das Índias](https://blog.caelum.com.br/wp-content/uploads/2009/03/terceiroolho-150x150.jpg "Terceiro Olho - Caminho das Índias")

Eu e o Paulo Silveira estivemos em um [pré-evento organizado pela Sun](http://blogs.sun.com/tvdigital/entry/the_dawn_of_a_new), onde o Java DTV foi anunciado. O portal [Convergência Digital fez a cobertura](http://www.convergenciadigital.com.br/cgi/cgilua.exe/sys/start.htm?infoid=17956&sid=8) e deu para ter um gostinho do que está por vir neste evento que a Sun está organizando sobre TV Digital: [**java@tvdigital**](http://blogs.sun.com/tvdigital/entry/the_dawn_of_a_new), que acontecerá no dia **02 de abril**, na [Universidade Anhembi Morumbi](http://portal.anhembi.br/publique/cgi/cgilua.exe/sys/start.htm?sid=95).

Fique de olho! Nos vemos por lá.
