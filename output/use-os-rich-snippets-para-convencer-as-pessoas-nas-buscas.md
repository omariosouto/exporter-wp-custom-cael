---
title: "Use os Rich Snippets para aumentar conversão nas buscas"
date: "2015-09-01"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando uma pessoa está buscando algo no Google ou qualquer outro buscador e nossa página (junto de outras centenas) é mostrada para ela nos resultados orgânicos, temos que convencê-la de que é o nosso resultado o mais interessante e que merece ganhar o clique.

A cada dia que passa, essa briga para atrair as pessoas à clicarem no resultado do seu site é mais intensa, já que cada vez mais a concorrência nos resultados das páginas de busca ficam maiores, com resultados patrocinados, orgânicos, mapas, artigos etc disputando espaço.

Para que possamos fazer esse convencimento, a princípio temos apenas alguns caracteres, divididos entre título e descrição da página, além de uma URL. Parece pouco, não?

\[caption id="attachment\_7083" align="aligncenter" width="587"\][![Página da Caelum na Busca](https://blog.caelum.com.br/wp-content/uploads/2015/08/Screen-Shot-2015-08-25-at-11.23.23-AM.png)](https://blog.caelum.com.br/wp-content/uploads/2015/08/Screen-Shot-2015-08-25-at-11.23.23-AM.png) Página da Caelum na Busca\[/caption\]

Dependendo do seu site e do negócio dele, pode ser que seja necessário algumas informações a mais. Já pensou um e-commerce deixando visível já na página de resultados do Google que seu produto está em um preço promocional? Ou então, um resumo da avaliação positiva dada pelos clientes aos serviços prestados por sua empresa? Ou datas em que acontecerão eventos da sua empresa? Bem, as possibilidades são enormes, mas com bastante frequência precisaremos enriquecer esses resultados de busca, mostrar informações a mais, além dos tradicionais título, descrição e URL, e convencer mais usuários de que o nosso resultado é o mais interessante.

Veja o que o AllRecipes, famoso site americano de receitas, faz para nos convencer de que o resultado dele é o mais interessante:

\[caption id="attachment\_7084" align="aligncenter" width="567"\][![Resultado da busca do AllRecipes com diversas informações interessantes a mais](https://blog.caelum.com.br/wp-content/uploads/2015/08/Screen-Shot-2015-08-25-at-10.38.22-AM.png)](https://blog.caelum.com.br/wp-content/uploads/2015/08/Screen-Shot-2015-08-25-at-10.38.22-AM.png) Resultado da busca do AllRecipes com diversas informações interessantes a mais\[/caption\]

Bem mais atraente, não? Mas como podemos fazer isso? Como enriquecer o resultado da nossa página nas buscas do Google? O segredo está no uso dos dados estruturados, suportados pelos buscadores. São pequenos pedacinhos de metadados, em HTML, que quando colocados em suas páginas, fazem com que os buscadores entendam apresentem trechos extras para sua página nos resultados das buscas, chamado de Rich Snippet ("pedaço rico", em uma tradução livre).

Com isso, podemos enriquecer uma página de um produto, para mostrar o seu preço, moeda e também se há estoque, com poucas linhas de código. A primeira tarefa é indicar, no HTML, a partir de onde está o conteúdo da página que fala sobre o produto. Fazemos isso, através dos atributos itemscope e itemtype:

\[code language="html"\] <div itemscope itemtype="http://schema.org/Product"> <h1>Título do produto</h1> <p>Descrição do produto...</p>

<p>Preço: R$49,90</p>

<p>Em estoque</p> </div> \[/code\]

O `itemscope` indica que ali está sendo aberto um escopo para falar de alguma informação importante. Qual informação? Justamente a identificada pelo atributo `itemtype`. Note que o valor do atributo é `http://schema.org/Product`. Essa é a identificação do metadado que vamos usar. O metadado de produtos. Existem vários outros disponíveis e inclusive há na [própria documentação do Google uma lista completa dos suportados](https://developers.google.com/structured-data/rich-snippets/).

Dentro da div, agora podemos indicar qual o preço do produto. Fazemos isso através da identificação de uma Offer (oferta), que é uma propriedade do escopo de produto chamada `offers`:

\[code language="html"\] <div itemscope itemtype="http://schema.org/Product"> <h1>Título do produto</h1> <p>Descrição do produto...</p>

<p itemprop="offers" itemscope itemtype="http://schema.org/Offer"> Preço: R$49,90 </p>

<p>Em estoque</p> </div> \[/code\]

O escopo `Offer` possui diversas informações que podem ser dadas, como preço, moeda e a disponibilidade. Vamos começar definindo o preço e moeda, através da adição das propriedades price e priceCurrency:

\[code language="html"\] <div itemscope itemtype="http://schema.org/Product"> <h1>Título do produto</h1> <p>Descrição do produto...</p>

<p itemprop="offers" itemscope itemtype="http://schema.org/Offer"> Preço: R$ <span itemprop="price">49,90</span> <meta itemprop="priceCurrency" content="BRL" /> </p>

<p>Em estoque</p> </div> \[/code\]

Envolvemos o preço dentro da tag span com o itemprop="price", já que essa informação é visível na tela. Por outro lado, o código da moeda não é uma informação visível. Não ao menos no formato em que ela deve ser informado, ISO 4217, onde R$ são BRL. Para isso, deixamos a informação no código fonte da nossa página, mas invisível ao usuário, através da tag meta.

Falta agora só colocarmos a disponibilidade. Note que a informação "Em estoque" está em uma tag fora da oferta. Ela é um outro `p`. No entanto, a disponibilidade **é uma informação da oferta**. Ou seja, precisamos fazer com que a delimitação do escopo da oferta envolva também a disponibilidade. Podemos fazer isso adicionando uma nova tag `div`, que envolva ambos os `p` e passando o escopo `Offer` para ela:

\[code language="html"\] <div itemscope itemtype="http://schema.org/Product"> <h1>Título do produto</h1> <p>Descrição do produto...</p>

<div itemprop="offers" itemscope itemtype="http://schema.org/Offer"> <p> Preço: R$ <span itemprop="price">49,90</span> <meta itemprop="priceCurrency" content="BRL" /> </p> </div>

<p>Em estoque</p> </div> \[/code\]

Pronto, agora o estoque está dentro do escopo da oferta. Só precisamos indicar o seu metadado:

\[code language="html"\] <p itemprop="availability" href="http://schema.org/InStock"/> Em estoque </p> \[/code\]

Com isso, teremos um resultado parecido com esse aqui:

\[caption id="attachment\_7085" align="aligncenter" width="589"\][![Página com informações de preço](https://blog.caelum.com.br/wp-content/uploads/2015/08/Screen-Shot-2015-08-25-at-11.14.41-AM.png)](https://blog.caelum.com.br/wp-content/uploads/2015/08/Screen-Shot-2015-08-25-at-11.14.41-AM.png) Página com informações de preço\[/caption\]

Mas as possibilidades não acabam por aí. Existe as propriedades e snippets para reviews, receitas, eventos e várias outras informações. Todas elas funcionam de forma similar à de produtos e possuem o mesmo próposito: "convencer a pessoa a entrar em seu site e aumentar a conversão". E você já usa? Quais informações do seu site irá enriquecer nas páginas de busca?

No [livro de SEO da Casa do Código](http://www.casadocodigo.com.br/products/livro-seo) e no [curso online de SEO do Alura](https://www.alura.com.br/cursos-online-front-end) também existem diferentes exemplos de uso dos dados estruturados e vários outros assuntos de SEO abordados.
