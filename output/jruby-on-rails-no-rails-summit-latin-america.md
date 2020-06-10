---
title: "JRuby on Rails, no Rails Summit Latin America"
date: "2008-10-21"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O evento foi **sensacional** e anda fazendo muito barulho na comunidade de desenvolvedores. Muitas pessoas colocaram fotos no [flickr](http://flickr.com/photos/tags/railssummit/), além de postar em [blogs](http://akitaonrails.com/2008/10/20/rails-summit-sucesso-comunidade) e no [twitter](http://blogblogs.com.br/livestream/name/railssummit) sobre o evento.

Minha apresentação falou um pouco sobre as vantagens de se ter aplicações Ruby on Rails rodando sobre a máquina virtual Java. Uma das melhores plataformas de execução de código da atualidade (se não for a melhor).

[JRuby on Rails](http://www.slideshare.net/fabiokung/jruby-on-rails-presentation?type=powerpoint "JRuby on Rails")

View SlideShare [presentation](http://www.slideshare.net/fabiokung/jruby-on-rails-presentation?type=powerpoint "View JRuby on Rails on SlideShare") or [Upload](http://www.slideshare.net/upload?type=powerpoint) your own. (tags: [jruby](http://slideshare.net/tag/jruby) [java](http://slideshare.net/tag/java))

Os slides não devem fazer muito sentido para quem não esteve na palestra. Na verdade eles servem mais para me guiar enquanto estou falando, do que para passar qualquer informação. De qualquer forma, fica como referência para quem estiver interessado. Pena que o slideshare bagunçou um pouco alguns slides que estavam cheios de animação.

Falei um pouco sobre as diferenças entre a MRI e o JRuby em relação ao gerenciamento de memória, garbage collection, JIT compilation e threads nativas. Também teve discussão sobre como o JRuby é até agora o único que se beneficia da thread-safety no Rails 2.2, um pouco de `invokedynamic`, [MLVM (Da Vinci Machine)](http://openjdk.java.net/projects/mlvm/) e código Ruby usando bibliotecas em Java. Java como plataforma (não a linguagem) é a principal vantagem do JRuby na minha opinião, e a palestra girou em torno disso.

Tiveram só dois demos, porque eu já estava sendo expulso da sala. No primeiro mostrei o [Ribs](http://github.com/olabini/ribs/tree/master) do [Ola Bini](http://olabini.com/blog/2008/09/announcing-ribs-002/), que permitirá o uso do [Hibernate](http://hibernate.org) em projetos JRuby. O segundo foi o demo do [JMaglev](http://fabiokung.com/2008/10/08/jruby-sharing-objects-across-multiple-runtimes-jmaglev/), usando [Nailgun](http://www.martiansoftware.com/nailgun/), já que tinha bastante gente que achava que o [vídeo que postei](http://fabiokung.com/2008/10/08/jruby-sharing-objects-across-multiple-runtimes-jmaglev/) era _fake_. ;-)

Muito obrigado a todos pelo feedback e **não percam o próximo**!
