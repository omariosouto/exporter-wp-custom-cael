---
title: "Como realizar testes A/B com o Google Analytics"
date: "2016-04-11"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando colocamos um site nosso no ar, sempre queremos que as pessoas atinjam os objetivos dela dentro das páginas. No caso de um e-commerce, o objetivo principal deve ser a realização da compra, enquanto para outros tipos de site, pode ser um contato enviado pelo cliente, pedido de um orçamento e assim por diante. De toda forma, há uma ação que esperamos que seja feita pelo cliente e geralmente chamamos essa ação de "conversão".

Para aumentar a chance de atingirmos esses objetivos, podemos fazer diversas melhorias no nosso site. Temos a possibilidade de mudar frases, posicionamento de botões e até mesmo a inclusão de novas funcionalidades. Mas como saber se essas mudanças foram efetivas? Como saber se elas deram o resultado esperado e não fizemos besteira no nosso site, colocando no ar algo que diminui essas conversões?

Por exemplo, no site da [MirrorFashion](http://www.mirrorfashion.net), que desenvolvemos durante o [nosso curso de HTML, CSS e JavaScript](https://www.caelum.com.br/curso-html-css-javascript/), uma loja fictícia de roupas, poderíamos testar qual _Call to Action_ é mais interessante para o botão que realizar a compra. O simples "Comprar" ou será que "Compre e parcele em 10x" é mais interessante?

\[caption id="attachment\_7519" align="aligncenter" width="969"\][![Call to Action Mirror Fashion](https://blog.caelum.com.br/wp-content/uploads/2016/03/site_mirror_fashion.png)](https://blog.caelum.com.br/wp-content/uploads/2016/03/site_mirror_fashion.png) Call to Action Mirror Fashion\[/caption\]

Poderíamos simplesmente fazer a mundança no botão e colocarmos a nova versão no ar e depois de algum tempo, por exemplo, 15 dias, colhermos os resultados e compararmos com o período anterior para sabermos se houve variação.

No entanto, com essa abordagem, estaríamos sujeitos há vários fatores sensíveis aos dias em que as variações estivessem no ar. Por exemplo, e se no primeiro período tívessemos mais acessos? E se ganhamos destaque de um anúncio no segundo período, que fez com que as pessoas comprassem mais? Repare que isso influenciaria o teste que queremos fazer, que é saber qual chamada é mais efetiva.

Para essa situação, a solução ideal é testar as duas variações ao mesmo tempo. É justamente nisso que consiste os testes A/B. **É você ter duas versões de uma página que você quer testar e realizar o teste simultaneamente**. Ao final do período do experimento, você poderá dizer qual foi a versão mais efetiva e aí decidir qual é a versão que irá para o ar.

Mas como realizar esses testes? Imagine a complexidade: primeiro é preciso ter as duas versões da página a testar, em seguida é preciso sortear qual versão vai ser mostrada para cada usuário e registrar que aquele usuário que viu determinada versão, só poderá ver essa versão até o fim do teste, além disso tudo, também é preciso agregar os resultados. Complexo, não?

Justamente por isso, precisamos utilizar alguma ferramenta que nos auxilie nesses testes. Existem diversas disponíveis no mercado, como o [Optimizely](https://www.optimizely.com/), [VWO](https://vwo.com/) e o [Convert](http://www.convert.com/), que são soluções pagas, mas extremamente completas. Para uma alternativa gratuita, o próprio [Google Analytics](http://www.google.com/analytics/) possui esse recurso, conhecido dentro dele como "Experimentos" e é o que vamos usar.

Para criarmos um novo teste A/B no Analytics, basta irmos, dentro do Analytics, em "Comportamento" -> "Experimentos" e lá criarmos um novo.

\[caption id="attachment\_7520" align="aligncenter" width="429"\][![Tela de criação de experimento](https://blog.caelum.com.br/wp-content/uploads/2016/03/criar_experimento_ab.png)](https://blog.caelum.com.br/wp-content/uploads/2016/03/criar_experimento_ab.png) Tela de criação de experimento\[/caption\]

Ao criar o novo experimento, temos que preencher algumas informações, como o nome do teste, a porcentagem do tráfego que verá as variações (inclusive a versão original) e nas opções avançadas, dizemos que as variações vão ser distribuídas igualmente entre os visitantes, além do tempo que o experimento irá ficar ativo (por padrão 2 semanas) e também o intervalor de confiança, que por padrão indica que a versão vencedora do teste só será definida quando estatisticamente houver 95% de certeza, dessa forma, é a cada 100 testes que realizarmos, 95 vezes ele terá o mesmo resultado, ou seja, teríamos uma alta confiança no que fizemos.

Quanto maior essa margem de confiança, mais difícil é declarar um vencedor, por outro lado, quanto menor, menos confiável será o resultado do seu teste (você poderia, por exemplo, declarar o botão "Comprar" vencedor, sendo que na verdade, a outra versão é mais efetiva).

Nessa mesma tela, você também terá que configurar um objetivo para o seu experimento. Nele você indicará qual é o critério que o Analytics vai utilizar para saber quem está sendo mais efetivo. Nosso critério serão as compras do e-commerce, ou seja, qual é a versão que trará mais lucro para o negócio. Vamos escolher essa opção dentro do template e vamos configurar que quem vê a página /thank\_you é quem comprou, já que essa página só é exibida para os clientes que concluíram a compra.

\[caption id="attachment\_7521" align="aligncenter" width="628"\][![Goal Setup de testes A/B](https://blog.caelum.com.br/wp-content/uploads/2016/03/goal_setup_ab.png)](https://blog.caelum.com.br/wp-content/uploads/2016/03/goal_setup_ab.png) Goal Setup de testes A/B\[/caption\]

Agora precisamos dizer quais páginas terão diferentes versões exibidas. Colocamos o endereço das duas páginas, no nosso caso, é o mesmo endereço, mas a variação recebe um parâmetro na requisição, para poder decidir quando mostrar a versão diferente. Dessa forma, a versão original está em [mirrorfashion.net/produto/moletom-abercrombie-vinho](http://mirrorfashion.net/produto/moletom-abercrombie-vinho) enquanto a variação 1 estará em [mirrorfashion.net/produto/moletom-abercrombie-vinho?variation=a](http://mirrorfashion.net/produto/moletom-abercrombie-vinho?variation=a). Escolhemos um produto específico que será mostrada a página original e outro que será mostrado com o novo Call to Action.

\[caption id="attachment\_7522" align="aligncenter" width="704"\][![Definição das variações](https://blog.caelum.com.br/wp-content/uploads/2016/03/ab_versoes.png)](https://blog.caelum.com.br/wp-content/uploads/2016/03/ab_versoes.png) Definição das variações\[/caption\]

Por fim, precisamos adicionar o código do Analytics para o experimento em nosso site, na página em que há o conteúdo original. O que esse código fará é basicamente o sorteio, vendo se o cliente já viu alguma das variações e caso não viu, decidirá qual das versões ele irá acessar, redirecionando-o.

\[caption id="attachment\_7523" align="aligncenter" width="446"\][![Experimento configurado](https://blog.caelum.com.br/wp-content/uploads/2016/03/experimento_configurado.png)](https://blog.caelum.com.br/wp-content/uploads/2016/03/experimento_configurado.png) Experimento configurado\[/caption\]

Pronto, agora seu experimento está ativo e em execução e você já consegue visualizá-lo na sua tela de experimentos.

\[caption id="attachment\_7524" align="aligncenter" width="1056"\][![Experimentos cadastrados](https://blog.caelum.com.br/wp-content/uploads/2016/03/experimentos_cadastrados.png)](https://blog.caelum.com.br/wp-content/uploads/2016/03/experimentos_cadastrados.png) Experimentos cadastrados\[/caption\]

Com o passar do tempo, novos dados serão agregados e você poderá vê-los nessa mesma tela, bastando escolher qual experimento deseja ver as informações, caso tenha vários cadastrados (você vai ver que essa brincadeira é viciante).

\[caption id="attachment\_7525" align="aligncenter" width="1046"\][![Experimentos cadastrados](https://blog.caelum.com.br/wp-content/uploads/2016/03/resultados_experimento.png)](https://blog.caelum.com.br/wp-content/uploads/2016/03/resultados_experimento.png) Experimentos cadastrados\[/caption\]

Com isso você poderá testar diferentes hipóteses de uma maneira simples e rápida, com poucas mudanças no seu site, bastando que haja um suporte para as duas (ou mais) variações a serem testadas. Com os números em mãos, é possível aplicar [diferentes](https://en.wikipedia.org/wiki/Chi-squared_test) [métodos](https://en.wikipedia.org/wiki/Odds_ratio) [estatísticos](https://en.wikipedia.org/wiki/Confidence_interval) para entender a confiança e relevância dos resultados, para aí sim tomar uma decisão com base em maiores certezas e dados que indicam esse caminho. Também é fundamental ter em mente o tamanho da amostragem necessária para que o teste seja relevante.

Como disse, uma vez que você começa a fazer alguns testes e começa a encontrar resultados, o processo se torna viciante. Sempre temos algo que queremos mudar no site, mas uma vez que você tem consciência de que a mudança pode trazer resultados negativos, é sempre recomendado testar as mudanças antes de oficializá-las.

E você, já faz testes A/B em seus sites? Quais testes fez ou faria? Conseguiu algum resultado surpreendente?
