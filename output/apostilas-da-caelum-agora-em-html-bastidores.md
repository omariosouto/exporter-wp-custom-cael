---
title: "Apostilas da Caelum agora em HTML: bastidores"
date: "2012-10-08"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Há poucos dias, a Caelum disponibilizou suas conhecidas [apostilas de computação](http://www.caelum.com.br/apostilas/) em mais um formato: o HTML, pra visualização direta no navegador. Confira:

- [Java e Orientação a Objetos](http://www.caelum.com.br/apostila-java-orientacao-objetos/)
- [Java para Desenvolvimento Web](http://www.caelum.com.br/apostila-java-web/)
- [Desenvolvimento Ágil para Web com Ruby on Rails 3](http://www.caelum.com.br/apostila-ruby-on-rails/)
- [Desenvolvimento Ágil para a Web 2.0 com VRaptor, Hibernate e AJAX](http://www.caelum.com.br/apostila-vraptor-hibernate/)
- [Algoritmos e Estrutura de Dados em Java](http://www.caelum.com.br/apostila-java-estrutura-dados/)

Isso se soma aos downloads que já são oferecidos em PDF e formatos de ebook - EPUB e mobi. **São apostilas de Java, Ruby on Rails, Web, estrutura de dados e VRaptor**.

![](https://blog.caelum.com.br/wp-content/uploads/2012/10/apostilas.jpg "apostilas")

## Conteúdo independente de formato

Por trás da flexibilidade das apostilas, está o [Tubaina](https://github.com/caelum/tubaina), um projeto opensource da Caelum de mais de 5 anos de maturidade. Com uma linguagem de marcação simples, lembrando markdown, os instrutores escrevem os textos independentemente do formato final.

Tudo é commitado num repositório Git e depois buildado num servidor de [integração contínua](https://blog.caelum.com.br/integracao-continua-de-projeto-java-com-jenkins/). A partir do mesmo fonte, geramos as saídas para impressão, PDF para download, ebooks em EPUB e mobi, e o HTML.

O Tubaina está por trás também do sucesso da editora [Casa do Código](http://www.casadocodigo.com.br), permitindo agilidade aos autores, facilidade de revisão e qualidade nos livros e ebooks finais.

## Design focado na leitura

Com o conteúdo em HTML, o próximo passo era definir o design. Resolvemos priorizar a leitura do usuário e prover a interface mais simples, direta e clean quanto fosse possível.

Investimos um bom tempo na **tipografia** das apostilas. Usamos a fonte [Merriweather](http://www.google.com/webfonts/specimen/Merriweather), via o [Google Web Fonts](http://www.google.com/webfonts/). É uma fonte elegante, levemente serifada, e de ótima leitura. Além disso, foi dada uma atenção especial ao font-size e ao line-height, assim como a largura da linha e o contraste de cores.

Tudo pra deixar a leitura mais confortável.

![](https://blog.caelum.com.br/wp-content/uploads/2012/10/screenshot.png "screenshot")

## Rápido, simples e otimizado

O foco no conteúdo também passou pela [performance](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/). Optamos por [não usar jQuery na página](https://blog.caelum.com.br/nao-use-jquery-no-seu-site-mobile-conheca-o-zepto-js/), e sim apenas [micro-frameworks leves de JavaScript](http://microjs.com/#) e APIs padrões - como a [History API](https://blog.caelum.com.br/como-nao-aprender-design-de-urls-os-hashbang/). E tudo é [carregado assíncronamente](https://blog.caelum.com.br/otimizacoes-na-web-e-o-carregamento-assincrono/), para ótima performance.

Pro CSS, usamos o [LESS](https://blog.caelum.com.br/css-facil-flexivel-e-dinamico-com-less/). Os estilos foram criados com alguns efeitos CSS3 mas pensando em [progressive enhancement](https://blog.caelum.com.br/css3-e-progressive-enhancement/). Usamos também **icon fonts** com a ferramenta [IcoMoon](http://icomoon.io/app/) pra ícones flexíveis e independentes de resolução.

## Multi dispositivo com design responsivo

A ideia das apostilas em HTML é facilitar o acesso pra divulgarmos ainda mais seu conteúdo. E é impossível falar de conteúdo acessível sem citar mobilidade e dispositivos como smartphones e tablets.

As apostilas são construídas com [web design responsivo](http://sergiolopes.org/responsive-web-design/), usando [media queries](https://blog.caelum.com.br/flexibilidade-em-paginas-para-dispositivos-moveis-com-media-queries/) e conteúdo flexível.

Embora tenhamos bons leitores de ebook para celulares, os formatos epub/mobi são difíceis de adaptar a telas pequenas quando pensamos em livros técnicos, com muito código. Na versão HTML, há um cuidado para tornar a leitura fácil em todas as telas, inclusive de códigos.

No futuro, vamos suportar também a AppCache API pra permitir a leitura offline das apostilas.

## Atenção às URLs, bookmarks, compartilhamento e SEO

Prestamos uma atenção especial aos endereços das apostilas e sua organização de capítulos e conteúdos - afinal, [URIs legais não mudam nunca](http://www.w3.org/Provider/Style/URI.html). Dá pra linkar para um capítulo ou seção da apostila e aí mandar por email, postar numa dúvida de [fórum](http://www.guj.com.br/) ou compartilhar no Facebook.

Isso é bom pra indexação do Google também - SEO - o que torna ainda mais fácil buscar dentro do conteúdo da apostila (tem uma caixa de busca direto no header da apostila).

Para aprender mais sobre Web Design Responsivo, recomendamos o [primeiro livro em português](http://www.casadocodigo.com.br/products/livro-web-design-responsivo "Livro Web Design Responsivo") sobre o assunto, lançado pela [Editora Casa do Código](http://www.casadocodigo.com.br "Casa do Código").

**Dê uma olhada nas novas [apostilas da Caelum](http://www.caelum.com.br/apostilas/) em HTML e deixe sua opinião.**
