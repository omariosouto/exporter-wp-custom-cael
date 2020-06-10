---
title: "Como começar com SEO e as boas práticas básicas"
date: "2014-06-11"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Durante o tempo em que você clicou e aguardou esse post carregar, mais alguns sites eram publicados na internet. Estamos rapidamente nos aproximando de [mais de 1 bilhão](http://www.internetlivestats.com/total-number-of-websites/) de sites online, espalhados por todos os cantos do mundo. Deixando de lado esse universo e focando em um nicho mais específico, ainda assim o número de sites é enorme.

Vamos pegar o exemplo de lojas online de camisetas no mercado exterior. Uma rápida pesquisa no google por "online t shirt store" devolve para mim mais de 360 milhões de páginas como resultados orgânicos, ou seja, que não envolvem resultados patrocinados.

[![Pesquisa por online t shirt store](https://blog.caelum.com.br/wp-content/uploads/2014/05/Screen-Shot-2014-05-29-at-3.38.45-PM.png)](https://blog.caelum.com.br/wp-content/uploads/2014/05/Screen-Shot-2014-05-29-at-3.38.45-PM.png)

Já imaginou você, criando sua loja online de camisetas para exportar pro mercado exterior e concorrendo com todas essas páginas de diversos sites para aparecer na busca do Google? Em algum lugar dos resultados da busca você estaria, então isso já é bom, certo? Na verdade não é bem assim. [Estudos mostram](http://chitika.com/google-positioning-value) que na realidade, a partir da 5a posição no resultado da busca, menos de 6% das pessoas clicam para acessar a página. Ou seja, se você não aparece no topo dos resultados, como as pessoas vão encontrar sua loja online?

**Faça seu site aparecer bem nos resultados**

Quando desenvolvemos um site, fazemos ele para que uma pessoa o acesse e entenda o que está lá. Na verdade, oferecer para os usuários um conteúdo logicamente estruturado, compreensível, de qualidade e relevante já é um grande passo para conseguir aparecer bem nos resultados de busca.

O tempo inteiro, [robôs dos](https://support.google.com/webmasters/answer/182072?hl=en "GoogleBot") [mecanismos de busca](http://en.wikipedia.org/wiki/Bingbot "BingBot") estão acessando os diversos sites da internet, analisando seu conteúdo e diversas características para saber em qual posição ele deve ser exibido. Todas essas características vão dizer o quão relevante o seu site é e quanto mais ele for, mais a frente ele aparecerá nos resultados.

Então o seu objetivo quando desenvolver uma página é justamente chegar nesse bom rankeamento. O lado positivo é que existem diversas boas práticas para otimizar seu site para os mecanismos de busca, o famoso Search Engine Optimization - SEO.

**Algumas boas práticas de SEO**

Um dos pilares para conseguir um bom ranking nos mecanismos de busca está no conteúdo oferecido no seu site. Os robôs dos mecanismos de busca são muito inteligentes. Imagine esses robôs como usuários extremamente exigentes que estão acessando o seu site. Ou seja, ele quer uma boa usabilidade, organização do conteúdo, qualidade no texto, links bons etc.

Pegando o site da loja online de camisetas, os mecanismos de busca esperam por uma página com um objetivo claro. Se é uma loja, ao acessá-la, o mecanismo precisa saber que é uma loja (por exemplo, pelos preços, a ocorrência de termos como "comprar", ofertas etc). Links fáceis de encontrar também contribuem nisso, além da qualidade gramatical do conteúdo da página.

Outro fator importante e também ligado ao conteúdo são os sites que linkam para o seu site. Quanto mais bem rankeados eles forem, melhor. Quanto mais relacionados ao conteúdo do seu site, melhor.

Todos esses aspectos de conteúdo, ou seja, não técnicos, de alguma forma também estão ligados com as otimizações técnicas que podemos fazer.

Uma das principais e extremamente simples de se implementar é fazer com que todas as páginas possuam um `title` único. Fazemos isso nas páginas dos sites da [Caelum](http://www.caelum.com.br), [Alura](http://www.alura.com.br "Alura") e [Casa do Código](http://www.casadocodigo.com.br). Tome cuidado com a densidade de palavras chave. Um mau exemplo aqui seria usarmos o título "Cursos de Java, cursos de HTML, cursos de .NET e cursos de Agile". Repare o termo "Curso" sendo forçado ali no título. Note também que até para nós, seres humanos, a leitura é desagradável. Também é para os robôs. Uma boa dica aqui é definir o que você mostra na sua página em no máximo 7 palavras.

Esse título geralmente é o que aparecerá logo no começo do resultado da sua página nas buscas:

[![Resultado da Caelum no Google](https://blog.caelum.com.br/wp-content/uploads/2014/05/google_caelum.png)](https://blog.caelum.com.br/wp-content/uploads/2014/05/google_caelum.png)

Outra otimização simples e obrigatória é a definição da descrição da sua página. Para isso, use a tag ``<meta>`:` ``

\[code language="html"\] <meta name="description" content="Nesse post você vai aprender como começar com SEO, além de ter as primeiras dicas para colocar em prática agora mesmo no seu site." /> \[/code\]

Essa é uma forma de resumir para o mecanismo de busca sobre o que sua página trata. Apenas fique atento, pois não necessariamente essa descrição será usada na hora de mostrar o seu site nos resultados. Isso vai depender de diversos fatores, entre eles os termos buscados pelo usuário. No lugar dessa descrição, poderá ser usado o conteúdo da sua página, então é bom cuidar também do restante dela.

**Otimizando textos, links e imagens**

A forma como você estrutura o texto na sua página é muito importante. Mostre títulos e chamadas em tags de `h1`, `h2`, `h3` etc e evite conteúdos longos nelas. Utilize a tag `p` para manter seus textos. Dessa forma os robôs vão saber também estruturar o conteúdo para mostrar suas páginas na busca dos usuários. Pode parecer básico demais, mas uma olhada rápida no código fonte de diversos sites famosos vai te mostrar que não é bem assim. Repare nesse trecho que mostra como fazemos na [página de cursos da Caelum](https://www.caelum.com.br/cursos-java/):

\[code language="html"\] <h2 class="bs-subtitulo">Formação Java</h2> <p> Quer entrar no promissor mercado de programação Java? Estude os cursos mais reconhecidos do Brasil. </p>

<p> A <em>Formação Java Caelum</em> é um pacote de <strong>três cursos de Java</strong> que totalizam <strong>100h de aula</strong>. Vamos desde o início do Java e da Orientação a Objetos e aprofundamos no Java na Web, que é o foco do mercado. </p> \[/code\]

Nas imagens, procure sempre dar uma descrição a elas, utilizando o atributo `alt` da tag `img`. Isso também vai fazer com que suas imagens apareçam bem na busca por imagens, um recurso que pode ser bastante poderoso por exemplo para blogs, dependendo do assunto abordado.

\[code language="html"\] <img src="imagem.png" alt="Pesquisa por online t shirt store" /> \[/code\]

Já os links são os artigos mais traiçoeiros para quem está começando. Para começar, evite usar termos como "clicar", "ir", "veja" e outros que não descrevam o conteúdo que está sendo linkado, pelo contrário, use um texto que consiga identificar o que vai ser linkado. Por exemplo, "Conheça também os cursos de Java" é um texto mais descritivo para um link que te enviaria para a página de cursos de Java da Caelum. Com isso, teríamos:

\[code language="html"\] <a href="https://www.caelum.com.br/cursos-java/">Conheça também os cursos de Java</a> \[/code\]

Outro detalhe fundamental quando linkamos para um outro site é garantir que estamos linkando para um site confiável e relacionado ao assunto da nossa página. Imagine que um spammer apareça aqui no blog da Caelum linkando para seu site. Uma forma de garantir que não estamos referendando aquele site, é através do atributo `rel="nofollow"`. Isso indica para o robô que ele não deve levar aquele link em consideração na hora de analisar a relevância e qualidade do seu conteúdo. Com isso, poderíamos aqui, por exemplo, linkar para o site da CocaCola:

\[code language="html"\] temos muitos usuários do blog que  <a href="http://www.cocacola.com.br" rel="nofollow">adoram beber CocaCola</a> \[/code\]

**Acompanhe os resultados**

Antes de mais nada, é importante ter em mente que o resultado das alterações que você fizer não aparecerão de imediato. Aliás, pode levar até semanas para você sentir alguma diferença. O importante é que você acompanhe os resultados das mudanças. Para isso, você pode usar o [Google Webmaster Tools](https://www.google.com/webmasters/tools/). Ele é uma ferramente gratuita onde você consegue saber quais termos os usuários buscam para cair em seu site, sua posição média por termo, sites que linkam para você, pedir para o robô de busca passar novamente no seu site caso você tenha feito alguma mudança e vários outros insights que podem te ajudar tanto na otimização do seu negócio como do seu site.

**E dá para ir muito mais além**

Essas são apenas algumas poucas otimizações que podem ser feitas. As possibilidades são muitas. Você pode melhorar suas URLs, trabalhar corretamente com os Status Codes, lidar com conteúdo duplicado e muito mais.

Se você estiver criando conteúdo original e de qualidade, já é um grande passo rumo a um ranking bom nas buscas. No [Workshop Tirando sua Startup do Papel](https://www.caelum.com.br/workshop-tirando-sua-startup-do-papel/) nós também discutimos o papel do SEO no negócio, quais otimizações se preocupar no começo e como obter usuários para o seu site de forma orgânica.

E você, usa alguma outra otimização nos seus sites?
