---
title: "Diminuindo acoplamento de sistemas com REST, e video!"
date: "2010-05-28"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muitos ainda me perguntam qual é a real vantagem do REST sobre os modelos mais tradicionais . Ao integrar sistemas, a implementação de acesso ou de [processos](http://en.wikipedia.org/wiki/Business_process_management) costuma ser feita de [maneira sequencial](http://en.wikipedia.org/wiki/BPEL#The_BPEL_language), onde esperamos resultados específicos de nosso servidor.

Esperar um resultado específico de um servidor é criar um grande acoplamento, e desejamos diminuir isso.

Em [clientes REST](http://restfulie.caelum.com.br), e em business processes modelados com REST, atingimos um nível de desacoplamento bem maior. Imagine o sistema a seguir ([entre outros](http://github.com/caelum/restfulie-provisioning/blob/master/test.rb#L77)) que efetuaria compras em um site como [a amazon](http://www.amazon.com):

`Quando tem um item que quero comprar E tem um carrinho de compras Mas nao desejei nada Entao deseje este produto primeiramente Quando tem um item que quero comprar E tem um carrinho de compras Entao deseje este produto Quando estou no carrinho E ainda quero comprar produtos Entao começe novamente Quando tem um pagamento Entao prepare o pagamento`

O código acima funciona e pode ser visto em execução na terceira parte da série Rest do zero. Nesse processo modelado como Rest, diversas evoluções poderão ocorrer no servidor sem quebrar o cliente, como por exemplo:

1\. Após adicionar um produto ao carrinho, caso o servidor passe a redirecionar para uma sugestão de produto ao invés do carrinho, o cliente continua funcionando normalmente.

2\. Caso não encontre um dos produtos, o cliente consegue pagar.

3\. Caso o servidor não possua o produto, passará um link para outra empresa que vende ele, e eu executarei o processo na outra empresa, sem perceber nada.

4\. Se eu desejar fazer a compra desde o início em outro site, que não a amazon, basta mudar o ponto de entrada de uma URI da amazon para uma URI de outro sistema.

Note que nos beneficiamos com [compatibilidade com código antigo](http://www.ibm.com/developerworks/java/library/ws-soa-backcomp/), [futuro](http://en.wikipedia.org/wiki/Forward_compatibility) e ainda somos capazes de atingir novos objetivos em outros sistemas sem saber da existência dos mesmos. Além disso passamos a poder comparar preço em diversos sites sem mudar uma linha de código, utilizando media types conhecidos (como [Atom](http://en.wikipedia.org/wiki/AtomPub) ou [RDFa](http://www.w3.org/TR/xhtml-rdfa-primer/)).

**Esse poder não é alcançado com modelos tradicionais de processo,** e [por vezes é deixado de lado](http://soa.sys-con.com/node/291050). É isso que REST e hipermídia traz para nossos sistemas, por exemplo.

Os modelos tradicionais acabam [utilizando mensagens que são comandos](http://bill-poole.blogspot.com/2008/04/avoid-command-messages.html) ao invés de trocar documentos. Apesar de nossa DSL parecer um comando ela é somente uma DSL customizável, executando a troca de documentos que você desejar.

O trabalho de mapear nossos processos de maneira mais genérica através do uso de [hipermídia](http://en.wikipedia.org/wiki/HATEOAS) foi descrito em [artigos publicados recentemente no portal da ACM](http://portal.acm.org/toc.cfm?id=1798354&type=proceeding&coll=GUIDE&dl=GUIDE&CFID=91726189&CFTOKEN=81407006).

Imaginemos agora o processo tradicional:

1. Executa uma busca
2. Adiciona o produto ao carrinho
3. Executa outra busca
4. Adiciona o produto ao carrinho
5. Paga

Em um sistema de web services clássico, onde temos diversos serviços, como o servidor me diria que devo pagar em outro sistema sem que eu precise escrever nem uma linha de código? Isso iria quebrar o meu cliente, e gerar a necessidade de codificar já pensando nessas falhas e eventuais evoluções do sistema.

Se o servidor passou a suportar somente uma determinada quantidade de produtos no seu carrinho, seu cliente quebrará pois não foi feito adapatdo a isso, o cliente REST se adapta e compra o que for possível.

Se nosso produto ou empresa utiliza esses modelos tradicionais de business process e web services e [cada mudança no serviço está implicando em altos custos de mudança nos clientes](http://www.manageability.org/blog/archive/20021119%23lt_p_gt_in_a/view), temos um grande indício de que esse modelo tradicional apresenta o alto acoplamento que uma arquitetura REST está tentando combater.

Serviços web tradicionais **[desacoplam](http://www.ibm.com/developerworks/webservices/library/ws-soa-tightcoupling/index.html)** um pouco mais que outras maneiras tradicionais de RPC, [mas ainda mantém muito acoplamento](http://intertwingly.net/blog/2008/03/23/Connecting#c1206306269).

**Se o custo de manutenção de seu código cliente está alto, é porque seu web service te acoplou demasiadamente ao servidor e aos schemas relacionados.**

A seguir você encontra um vídeo de introdução ao acoplamento que o método tradicional apresenta:

[Minimizando acoplamento com REST](http://vimeo.com/12085657) from [Caelum](http://vimeo.com/user1362352) on [Vimeo](http://vimeo.com).
