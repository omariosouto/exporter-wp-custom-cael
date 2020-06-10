---
title: "Escrevendo e migrando aplicações para o Google App Engine"
date: "2009-11-17"
author: "pmatiello"
authorEmail: "pedro.matiello@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Recentemente [migramos nosso site para o Google App Engine](https://blog.caelum.com.br/o-cloud-computing-e-inevitavel/) (GAE), o serviço de cloud computing do Google, fazendo uso do suporte a aplicações Java. A ideia é que você desenvolva sua webapplication normalmente e faça o upload do war para os servidores do Google usando um SDK, que também possui um servidor local para desenvolvimento. Pronto! Sua aplicação web agora oferece alta escalabilidade e disponibilidade.

Algumas restrições existem, no entanto. Vamos passar por elas e comentar cada uma, que no início podem ser uma barreira para o desenvolvedor. Para começar, há uma [whitelist especificando as classes do Java que são suportadas](http://code.google.com/appengine/docs/java/jrewhitelist.html). As classes da biblioteca padrão não listadas são bloqueadas. Pode parecer inconveniente, mas estas limitações são necessárias para garantir a segurança e escalabilidade do serviço — basta lembrar que uma mesma máquina é dividida entre muitas aplicações, e que cada aplicação pode estar rodando em um grande número de servidores.

O Google App Engine também não permite que novos arquivos sejam criados pela aplicação no servidor, já que isso poderia implicar em problemas de sincronização entre os sistemas de arquivos de suas muitas instâncias. Também não é oferecido um banco de dados relacional, mas sim uma [datastore sobre o BigTable](http://code.google.com/appengine/docs/java/datastore/overview.html), que pode ser acessada pelas APIs do [JDO](http://code.google.com/appengine/docs/java/datastore/usingjdo.html) e da [JPA](http://code.google.com/appengine/docs/java/datastore/usingjpa.html). Relacionamentos _many-to-many_, consultas com join, agregações (sum, avg, max, etc) e polimórficas não são suportadas, mas a maior parte das demais operações funciona como de costume sem a necessidade de maiores mudanças. Trata-se de uma característica comum dos [bancos de dados não-relacionais](https://blog.caelum.com.br/bancos-de-dados-nao-relacionais-e-o-movimento-nosql/), que abrem mão de algumas funcionalidades para obter maior desempenho, escalabilidade e disponibilidade. Pode ser trabalhoso migrar de um banco de dados relacional para o BigTable, além de ser uma tecnologia proprietária do Google.

Outra limitação importante é a impossibilidade de criar sockets e Threads. Todavia, o App Engine possui mecanismos próprios que permitem a realização de [requisições HTTP](http://code.google.com/appengine/docs/python/urlfetch/) e o [envio de emails](http://code.google.com/appengine/docs/java/mail/) (com restrições na especificação do remetente). Em breve uma API de agendamento de tarefas será disponibilizada para o Google App Engine Java.

Ainda, o suporte a Expression Language vem desativado por padrão. Para ativá-lo, é necessário adicionar a seguinte linha em todos os arquivos JSP que fazem uso de EL: `<%@ page isELIgnored="false" %>`

No caso de arquivos de tags, deve-se adicionar: `<%@ tag isELIgnored="false" %>`

A tag `<include-prelude>` no `web.xml` também é ignorada, mas é possível contornar o problema adicionando algo como a linha abaixo no começo dos arquivos JSP: `<%@ include file="../prelude.jspf" %>`

Um problema de outra é ordem é a inicialização e a manutenção das instâncias, conhecido como **[cold start](http://www.mail-archive.com/google-appengine-java@googlegroups.com/msg00164.html)**. Quando sua aplicação é chamada pela primeira vez, várias instâncias são produzidas de forma distribuída pelos servidores da nuvem. Este processo é bastante lento, podendo consumir vários segundos, e depende também da velocidade do contexto da sua aplicação web. Os acessos seguintes são respondidos com baixa latência, mas, após um período de inatividade, as instâncias são destruídas, exigindo que todo o lento processo de inicialização seja refeito pelo serviço em um próximo acesso. Para evitar isso, a prática mais comum tem sido usar o [agendador de tarefas](http://code.google.com/appengine/docs/java/config/cron.html) do próprio App Engine para visitar alguma página do site a cada poucos minutos — e esta é uma solução que [também possui seus problemas](http://groups.google.com/group/google-appengine/browse_thread/thread/22692895421825cb/).

Por causa da grande quantidade de componentes restritos, muitos frameworks, como Spring, implementações de JSF, mapeadores XML, exigem ajustes para rodar no GAE. O mesmo se aplica ao VRaptor 3 e, para facilitar o trabalho dos usuários, muitos deles oferecem uma versão voltada para o uso no Google App Engine, incluindo todas as alterações necessárias. No caso do VRaptor 3, você pode obter um _blank-project_ pro GAE na [página de downloads](http://code.google.com/p/vraptor3/downloads/list) e usa-lo como esqueleto de um novo projeto, que pode ser facilmente importado e modificado no Eclipse (existe um plugin do Google bastante prático, mas um [build.xml adequado ao GAE](http://code.google.com/appengine/docs/java/tools/ant.html#The_Complete_Build_File) é uma alternativa bastante razoável).

Em post anterior, o Paulo Silveira já havia falado, de forma geral, [sobre as vantagens de manter sua aplicação no cloud](https://blog.caelum.com.br/o-cloud-computing-e-inevitavel/), mesmo quando o volume de requisições não é grande o bastante para se representar um gargalo, como é o nosso caso e pode ser visto pelos paineis de controle do GAE:

![Gráfico de requisições por segundo no www.caelum.com.br](https://blog.caelum.com.br/wp-content/uploads/2009/11/chart.png)

_Requisições por segundo no www.caelum.com.br_

Sobre o Google App Engine, em particular, vale ainda acrescentar que a administração é bastante simples, com relatórios e estatísticas apresentados de forma coerente, e também que é possível executar múltiplas versões da sua aplicação facilmente. O serviço ainda é beta [e tem seus problemas ocasionais](http://code.google.com/status/appengine/detail/datastore/2009/10/22), mas, até o momento, tem se comportado de forma bastante satisfatória. Considere o uso do cloud para sua próxima aplicação e livre-se de boa parte da preocupação com hardware, grids, clusters e infraestrutura.
