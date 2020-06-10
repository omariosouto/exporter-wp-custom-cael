---
title: "Hipermídia e contratos dinâmicos: menor acoplamento"
date: "2009-12-17"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nos últimos anos você vem comprando livros em um website: você acessa o site inicial www.amazon.com, procura pelo livro que deseja comprar, adiciona-o ao seu carrinho, escolhe o método de pagamento e finaliza a compra.

Na época do Natal, o site muda: existe agora uma promoção de fim de ano e você se depara com um conteúdo inesperado: existem funcionalidades e informações novas (como um programa de desconto através de cupons). Como reage um humano ao encontrar a mudança com novas possibilidades de iteração em um site?

- Gritar: "contrato violado! não comprarei mais nada!
- Ignorar as novas informações e executar o processo
- Usar o intelecto humano e se aproveitar das novas informações

Como humanos sabemos o quão natural é agir de maneira a ignorar as informações - caso elas não contribuam com meu objetivo - ou tirar proveito delas.

A opção 1 só se concretiza caso existisse um comprometimento total a maneira que o site disponibilizava suas informações e ao processo: se meu acoplamento for alto e o que eu espero seja fixo, imutável. Infelizmente robôs não são ainda capazes de raciocionar como nós e executar a última opção.

O conteúdo hipermídia permite evoluir o servidor com funcionalidades e dados sem quebrar os clientes consumidores **por padrão**. Ninguém deixaria de comprar pois fornecemos funcionalidades e dados novos em relação aos recursos disponibilizados.

Isso permitiu a evolução de sites por diversos anos sem que usuários enviassem emails para o responsável reclamando da nova função que foi adicionada, dizendo que não utilizarão o sistema pois existe conteúdo extra.

**Hipermídia [permite um baixo acoplamento](http://jim.webber.name/2008/04/19/30b4f0e9-f67a-4310-bf38-ca0a3423206e.aspx) entre o cliente e o servidor** e pode ser levado para o mundo da automatização: a web dos sistemas. Na web humana, validamos nossos contratos com o usuário final através do uso de testes end-to-end, verificando a existência de funcionalidades como o usuário o faria.

Diversas opções de ferramentas como [selenium-rc](http://selenium-rc.seleniumhq.org/) e [webdriver](http://code.google.com/p/webdriver/) fornecem funcionalidades para garantir que o [comportamento esperado](http://behaviour-driven.org/) não será quebrado com novos releases.

Eles não validam tudo retornado pela requisição, dando espaço para a ::forward-compatibility::, a capacidade de evoluir nosso sistema no servidor sem quebrar o comportamento esperado. Por exemplo, adicionar novas funcionalidades ou campos não relativos ao teste não deve quebrar o mesmo.

Na web para sistemas integrados, a representação mais comum é o [xml](http://www.w3.org/XML/), [que não suporta conteúdo hipermídia](http://en.wikipedia.org/wiki/Hypermedia), uma vez que uris devem ser tratadas como texto (de acordo com a especificação) então acabamos criando nossos próprios [media-types](http://www.w3.org/TR/CSS2/media.html), como vnd/caelum+xml, onde há a definição de como elas devem ser tratadas: [o nosso próprio micro formato.](http://www.google.com/url?q=http://jim.webber.name/downloads/presentations/2007-11-Qcon/A%2520Couple%2520of%2520Ways%2520to%2520Skin%2520an%2520Internet-Scale%2520Cat.ppt&ei=C8wfS_2UEoqHuAeZorD0Cw&sa=X&oi=nshc&resnum=1&ct=result&cd=1&ved=0CAoQzgQoAA&usg=AFQjCNEHzQJVbgNxn6Dgstl7dZiFpR7mUQ)

Existem diversas alternativas para criar esquemas [forward](http://en.wikipedia.org/wiki/Forward_compatibility) e [backward](http://en.wikipedia.org/wiki/Backward_compatibility) compatíveis mas infelizmente esse não é o comportamento padrão de arquivos como o formato ::xsd:: e arquitetos não se lembram disso ao definir seus esquemas, o suporte é opcional.

Dentre essas opções, a mais fácil e possivelmente perigosa envolve [permitir qualquer tipo de conteúdo em qualquer campo](http://bill.burkecentral.com/2009/03/31/rest-needs-polymorphic-xsd/#comment-2520), enquanto outra solução [envolve o uso de tipos polimórficos](http://www.ibm.com/developerworks/xml/library/ws-tip-xsdchoice.html): um perigoso início de schema-hell controlando diversas versões para uma mesma funcionalidade.

[Micro formatos como os que podemos criar permitem a definição de uma estrutura fixa e uma dinâmica](http://www.infoq.com/articles/subbu-allamaraju-rest): um contrato parcialmente fixo, com garantias para validação e compatibilidade, além de parcialmente dinâmico, com liberdade para evolução, diminuindo o acoplamento que seu sistema possuia ao utilizar um esquema totalmente fixo.

Mas a responsabilidade de não quebrar o contrato original fixo ainda é do servidor.

Na web humana, _xhtml_ permite validar a estrutura (o contrato) enquanto é responsabilidade sua (seus testes) não remover o campo de busca de livro, caso contrário o processo não se completa.

Enquanto esquemas permitem a validação de dados, os testes permitem a validação dos processos. Ambos devem ser escritos de maneira a permitir a evolução desacoplada do servidor e do cliente. E quais seriam então as partes dinâmicas do meu contrato?

Os possíveis estados de seu recurso podem variar com o tempo: uma aplicação para empréstimo pode ser só aprovada ou recusada, mas com o passar do tempo a empresa pode decidir a existência de um novo estado: "prolongado".

As relações entre seu recurso e outros recursos também variam: um cliente pode ter uma lista de serviços contratados atualmente, acessando a sua representação via links. É natural imaginar que surjam novos serviços e que o cliente mude suas contratações.

As transições e operações disponíveis para seus recursos também são dinâmicas: [suportando um método HTTP novo](http://rest.blueoxen.net/cgi-bin/wiki.pl?HttpMethods) ou um novo link não quebra a existência de clientes que consomem as transições e operações existentes até então.

Todo esse dinamismo é guiado através de hiperlinks e conteúdo hipermídia. Como os clientes terão certeza que não quebramos o contrato dinâmico?

Da mesma maneira que implementamos [testes para garantir o comportamento esperado](http://dannorth.net/introducing-bdd), precisamos deles para garantir que o processo não é alterado no servidor.

Os testes end-to-end são a única garantia de que não quebramos os processos junto ao cliente, seja ele humano ou outro serviço.

[Esquemas xml](http://www.w3.org/XML/Schema) [podem ser usados de maneira a garantir flexibilidade e compatibilidade](http://guilhermesilveira.wordpress.com/2009/12/03/resteasy-where-did-the-hypermedia-go-to/#comment-51), mas não é o comportamento padrão de tal ferramenta: depende muito mais do usuário conhecer e fazer o uso adequado dela.

[ATOM](http://www.subbu.org/blog/2009/04/atom-is-not-soap) é um exemplo que suporta **por padrão** contratos dinâmicos: ao seguir o [Must Ignore](http://www.xml.com/pub/a/2003/12/03/versioning.html), ganhamos forward e backward compatibility. Contratos dinâmicos fornecem dicas para os frameworks, permitindo ao servidor guiar o cliente naquilo que pode executar ou acessar.

A consequência principal de [contratos dinâmicos é o baixo acoplamento](https://blog.caelum.com.br/2008/02/18/os-7-habitos-dos-desenvolvedores-de-webservices-altamente-eficazes/#comment-30904).

[O Restfulie](http://www.github.com/caelum/restfulie-java) foca no poder do hipermídia como facilitador na evolução a médio e longo prazo: não são URIs elegantes ou a adoção do protocolo HTTP sozinhos que criam sistemas de baixo acoplamento.
