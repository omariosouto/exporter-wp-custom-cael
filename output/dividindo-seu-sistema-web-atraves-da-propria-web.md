---
title: "Dividindo seu sistema web através da própria web"
date: "2012-08-28"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Sistemas que vivem isolados e sozinhos são raridade. É comum que sistemas integrem-se com outros, sejam novos ou legados. Como fazer essa integração? Temos várias respostas já bem estudadas e debatidas. Mas vamos além: não é interessante criar sistemas dessa forma, quebrado em menores?

Cada vez mais temos pequenas aplicações, mais focadas. As grandes aplicações agora são compostas por diversas outras menores, sem que o usuário final perceba. Uma página que te mostra o preço de um livro pode, na verdade, estar consultando uma dezena de subsistemas independentes.

É uma forma de diminuir o acoplamento, diminuir pontos únicos de falhas e poder trabalhar mais independentemente em subsistemas distintos. O [keynote do QCon 2012 desse ano do Netflix](https://speakerdeck.com/u/benjchristensen/p/performance-and-fault-tolerance-for-the-netflix-api-qcon-sao-paulo) mostrou esse aspecto.

Por exemplo, na Amazon o sistema de recomendação de produtos pode ser carregado através de um sistema secundário, assim como os dados de um sistema de reviews:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/08/recomendacoes.png "Recomendações podem ser feitas através de um sistema externo")](https://blog.caelum.com.br/wp-content/uploads/2012/08/recomendacoes.png)

Além do http, a html na camada de apresentação permite novamente a qualquer desenvolvedor iniciante incluir em seus sistemas partes de sistemas externos que contribuem para a criação de uma aplicação diferente.

Por exemplo, o Caelum Online possui um sistema de pontuação e de badges que permite ao aluno crescer e demonstrar seu conhecimento a medida que aprende e ensina dentro da plataforma:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/08/pontos.png "Pontos no caelum online")](https://blog.caelum.com.br/wp-content/uploads/2012/08/pontos.png)

[![](https://blog.caelum.com.br/wp-content/uploads/2012/08/badges2.png "Lista de badges atuais do Caelum Online")](https://blog.caelum.com.br/wp-content/uploads/2012/08/badges2.png)

Mas poderia ser demais para a plataforma de ensino online da Caelum ter/ser um sistema de badges. O código dela está ligado a uma solução de educação e o código de badges faz parte de um outro sistema de gamification, um sistema que poderia ser inclusive utilizado por outros projetos. Seria ruim se um desenvolvedor da equipe da Caelum que quisesse melhorar o sistema de badges tivesse de conhecer a plataforma de ensino e vice-versa.

Extraímos esse e outros pequenos sistemas e colocamos em um site externo. Agora como integrar os dois lados? O http e o html facilitam a integração, [com rest por exemplo](https://blog.caelum.com.br/diminuindo-acoplamento-de-sistemas-com-rest-e-video/). São os mesmos Do lado do sistema que controla badges nos cadastramos e cadastramos uma badge para o mínimo de 1000 pontos:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/08/melbadger2.png "Criando badges no melbadger")](https://blog.caelum.com.br/wp-content/uploads/2012/08/melbadger2.png)

Do lado da plataforma de ensino, adicionamos um iframe (integração via html com um simples http GET por baixo do pano):

`<iframe src="http://www.melbadger.com:80/1/all/555" ></iframe>`

E a cada nova atividade que vale ponto, fazemos um post através do seguinte código em Ruby:

\[ruby\] uri = URI.parse "http://melbadger.com/my\_tenant\_secret/uri/add" Net:Http.post\_form uri, {"data\[category\]" => "points", "data\[quantity\]" => 200, "data\[enduser\_id\]" => 1} \[/ruby\]

Ou em Java:

\[java\] PostMethod post = new PostMethod("http://melbadger.com/my\_tenant\_secret/uri/add"); post.addParameter("data\[category\]", "points"); post.addParameter("data\[quantity\]", "200"); post.addParameter("data\[enduser\_id\]", "1"); new HttpClient().execute(post); \[/java\]

E pronto. Toda vez que um usuário atingir uma determinada pontuação, a integração entre os dois sistemas cuidará de mostrar a lista de badges atualizada. Mais: podemos codificar, através de mecanismo simples, uma forma de se precaver no caso do sistema de gamification estar caído, para que o site principal não exiba mensagens de erro desnecessariamente.

Extraindo um sistema assim permite evoluí-lo de maneira simples e razoavelmente independente. Mas isso não significa que não há cuidados a serem tomados, como o tempo de carregamento de uma página, o [número de requests simultâneos](https://blog.caelum.com.br/otimizacoes-na-web-e-o-carregamento-assincrono/) e [diversas outras otimizações](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/) da web que são importantes.
