---
title: "Apostilas da Caelum offline com Application Cache do HTML 5"
date: "2013-03-05"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Você já conhece as [apostilas abertas](http://www.caelum.com.br/apostilas/) da Caelum. Disponíveis gratuitamente desde 2004 para download, elas são um diferencial nessa proposta pioneira da Caelum de difundir o conhecimento.

E as apostilas estão em **constante evolução e inovação**. Sempre atualizamos o conteúdo com novos exercícios e mais explicações - aliás, é por isso que você deve sermpre baixar a versão mais nova na [página oficial](http://www.caelum.com.br/apostilas/). E sempre tentamos inovar na distribuição da apostila.

![](https://blog.caelum.com.br/wp-content/uploads/2012/10/apostilas.jpg "apostilas")

Na começo, publicamos **PDFs** pra você baixar e imprimir. Com a popularização dos ereaders, começamos a publicar versões de **ebook em ePub e mobi** (Kindle). Mas, às vezes, você quer apenas olhar um assunto rapidamente, ou mandar um link sobre algum tópico pra um amigo. Pra isso, lançamos 6 meses atrás as **apostilas em HTML pra ler no navegador**. Não precisa baixar nada, só clicar, ler, navegar, linkar.

A última inovação que acabamos de colocar no ar é suporte a **leitura offline na versão HTML**. Ou seja, você não precisa mais baixar o PDF ou os ebooks se quiser ler quando não estiver conectado a Internet. A versão HTML funciona em qualquer navegador moderno, incluindo **tablets e smartphones**, já que tem uma interface responsiva.

As novas apostilas HTML com suporte a offline juntam as vantagens das versões para download com as vantagens da Web. Para instalar, acesse o menu superior da apostila e vá na opção _Leitura offline_:

- [Java e Orientação a Objetos](http://www.caelum.com.br/apostila-java-orientacao-objetos/)
- [Java para Desenvolvimento Web](http://www.caelum.com.br/apostila-java-web/)
- [Laboratório Java com Testes, XML e Design Patterns](http://www.caelum.com.br/apostila-java-testes-xml-design-patterns/)
- [Desenvolvimento Ágil para Web com Ruby on Rails 3](http://www.caelum.com.br/apostila-ruby-on-rails/)
- [Desenvolvimento Ágil para a Web 2.0 com VRaptor, Hibernate e AJAX](http://www.caelum.com.br/apostila-vraptor-hibernate/)
- [Algoritmos e Estrutura de Dados em Java](http://www.caelum.com.br/apostila-java-estrutura-dados/)

E, claro, as outras opções para download (PDF, ePub, mobi) continuam disponíveis. Você escolhe a opção que for melhor pra você.

### A implementação

O suporte a leitura offline é implementado usando a [Application Cache API](https://developer.mozilla.org/en-US/docs/HTML/Using_the_application_cache) disponível no HTML 5. O [suporte nos navegadores](http://caniuse.com/offline-apps) é muito bom: Chrome, Firefox, Opera, Internet Explorer (a partir da versão 10), iOS, Android e Blackberry.

A experiência na criação desse recurso gerou uma [palestra sobre o HTML5 offline e Application Cache](http://sergiolopes.org/palestra-appcache-html5-offline/) que você pode consultar para aprender mais. Se estiver curioso, dê uma espiada no [arquivo de manifest](http://www.caelum.com.br/apostila-java-orientacao-objetos/offline/cache.manifest) da apostila FJ-11. É um arquivo texto bem simples de ler, e ele lista todos os componentes da apostila que serão baixados pelo navegador. A partir dele que é feita a instalação com a Application Cache API.

Essa novidade ainda está em beta, apesar dos testes extensivos que fizemos. Teste você também e deixe sua opinião nos comentários!
