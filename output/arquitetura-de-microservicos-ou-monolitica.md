---
title: "Arquitetura de microserviços ou monolítica?"
date: "2015-03-17"
author: "adriano.almeida"
authorEmail: "adriano.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

\[embed\]https://www.youtube.com/watch?v=uMb2X-8FBAo\[/embed\]

Dentro da Caelum, temos a experiência de termos criado várias aplicações web, seja para nós mesmos, para clientes ou para iniciativas nossas. Uma dessas aplicações de vital importância para nós é o nosso ERP. É um sistema desenvolvido internamente e que cuida basicamente de toda a empresa, desde o financeiro, RH, pagamento, até as matrículas e contratos dos alunos, contatos com clientes, alocação de instrutores e várias outras funcionalidades. Todas essas funcionalidades estão agrupadas dentro desse grande sistema, fazendo dela uma aplicação monolítica, ou seja, uma aplicação feita em uma só unidade.

\[caption id="attachment\_6687" align="aligncenter" width="300"\][![Sistema monolítico](https://blog.caelum.com.br/wp-content/uploads/2015/01/monolitico.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/monolitico.png) Um único sistema, com todos os módulos dentro dele\[/caption\]

 

Como tudo em desenvolvimento de software, existem vantagens e desvantagens nos sistemas monolíticos. Um dos principais pontos negativos é que você tem um grande [ponto único de falha](http://en.wikipedia.org/wiki/Single_point_of_failure "Single Point of Failure"), que significa que se houver algum erro no cadastro de funcionários que deixe o sistema fora do ar, isso vai levar junto todo o sistema, incluindo funcionalidades que não possuem nenhuma relação com essa funcionalidade, como por exemplo a geração de contrato para alunos. Outro ponto negativo é a base de código, que se torna muito extensa, podendo deixar novos membros do projeto menos produtivos por algum tempo, já que a complexidade do código é bem maior.

Por outro lado, temos um sistema cujo o deploy é fácil de ser feito, já que o banco de dados facilmente evoluirá junto para todas as funcionalidades e há apenas um ponto onde o deploy precisa ser feito. Além disso, não há duplicidade de código e classes necessárias entre os diferentes módulos, já que todas elas fazem parte da mesma unidade.

**O cenário da Casa do Código**

Ao criarmos a [Editora Casa do Código](http://www.casadocodigo.com.br "Editora Casa do Código"), decidimos seguir outro caminho para o e-commerce. Lá não podemos correr o risco de ficar com a loja fora do ar caso alguma funcionalidade periférica falhe, então quebramos nossa arquitetura em serviços menores. Dessa forma, há um sistema principal, que é a loja e está hospedada no Shopify, e vários outros sistemas que gravitam em torno dela. Temos uma aplicação que faz a liberação dos e-books para os clientes, outro que contabiliza os royalties para autores, outro para cuidar da logística de envio dos livros impressos para o cliente, um painel de visualização dos livros comprados, um para fazer a liberação de vale presentes e outro para promoções.

\[caption id="attachment\_6688" align="aligncenter" width="500"\][![Arquitetura em microserviços](https://blog.caelum.com.br/wp-content/uploads/2015/01/microservicos.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/microservicos.png) Vários sistemas que são notificados por outro via HTTP quando um determinado evento ocorre. Cada sistema decide o que fazer com o JSON que é enviado para ele\[/caption\]

 

Dessa forma, quando um evento acontece na nossa loja online, os diferentes sistemas precisam ser notificados. Para essas notificações usamos requisições HTTP, assim, quando uma compra é confirmada na loja online, todos os sistemas recebem em um Endpoint essa requisição HTTP, contendo um JSON com todos os dados da compra. Cada sistema decide o que fazer com as informações recebidas, de acordo com a necessidade. Por exemplo, o sistema de liberação dos e-books verifica se a compra possuía e-books e gera os links de download para o comprador, enquanto que o sistema de logística já dá baixa no estoque quando a compra é de um impresso, além de notificar as pessoas quando algum livro está ficando com estoque crítico. Essa característica importante para o maior desacoplamento dos serviços é conhecida como [_Smart endpoints, dumb pipes_](http://stackoverflow.com/questions/26616962/microservices-what-are-smart-endpoints-and-dumb-pipes "Smart endopoints, dumb pipes"), que em uma tradução livre pode ser entendida como Endpoints inteligentes e fluxos simples.

Essa independência entre os diferentes serviços também traz consigo algumas vantagens e também desvantagens. Pelo lado positivo, minimizamos a existência daquele ponto único de falha. Caso algo dê errado no sistema que contabiliza os royalties, a loja continua no ar, os clientes continuam conseguindo baixar seus e-books, ou seja, não compromete a execução de outros servidos. Além disso temos sistemas com base de código menor, facilitando a barreira inicial na compreensão do projeto para um novo membro do time. Outro fator de suma importância é que conseguimos facilmente trabalhar com diferentes tecnologias. Temos serviços desenvolvidos em Java com VRaptor, Java com Play Framework, Rails e novos serviços sendo criados em PHP.

Por outro lado, há uma intersecção de código entre todos os sistemas, gerando uma repetição de código. Por exemplo, o código que recebe a requisição, transforma o JSON vindo do Shopify nos objetos que queremos trabalhar é o mesmo para todos os projetos, assim como classes de modelo. Minimizamos esse impacto com a criação de bibliotecas, que são compartilhadas entre os projetos. Além disso, temos uma pequena duplicidade de informações que raramente sofrem alterações entre os serviços, já que cada um possui sua própria instância de banco de dados.

Essa arquitetura onde temos um grande sistema quebrado em serviços menores e mais leves, por critério de funcionalidades de negócio, integrados via HTTP (ou alguma arquitetura de mensageria) é o que forma a famosa [**arquitetura de microserviços**](http://martinfowler.com/articles/microservices.html "Microserviços"). Evidentemente, existem outros casos de arquiteturas de microserviços que podem usar diferentes tecnologias, bancos de dados compartilhados entre serviços, serviços que se comunicam com outros serviços e assim por diante.

Faz parte do papel do arquiteto de software tomar decisões baseadas em trade-offs. Nunca teremos uma solução perfeita. Sempre precisamos escolher entre vantagens e desvantagens, como foi o caso da Caelum com seu ERP monolítico e da Casa do Código com a utilização de microserviços. O importante é analisar calmamente qual é a melhor abordagem para cada situação. E você, já trabalhou em algum projeto com arquitetura de microserviços? O que achou? E se quiser saber mais sobre mobile, microserviços, APIs e vários outros assuntos, não deixe de visitar a [Mobile Conf](http://www.mobileconf.com.br "Mobile Conf 2015"), dia 30/05 no Rio de Janeiro!
