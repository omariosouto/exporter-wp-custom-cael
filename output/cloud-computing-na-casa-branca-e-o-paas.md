---
title: "Cloud Computing na Casa Branca e o PaaS"
date: "2010-05-14"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Não é novidade empresas economizarem milhares de dólares ao adotarem o cloud, [como foi o caso do New York Times](http://open.blogs.nytimes.com/2007/11/01/self-service-prorated-super-computing-fun/). Agora, foi a vez do **governo** norte americano, que [deve economizar mais de um milhão de dólares até 2011](http://radar.oreilly.com/2010/05/white-house-moves-recoverygov.html) através do Amazon EC2.

\[caption id="attachment\_2499" align="aligncenter" width="300" caption="Governo americano começa a adotar o cloud."\][![](https://blog.caelum.com.br/wp-content/uploads/2010/05/whitehouse-300x159.png "whitehouse cloud computing")](https://blog.caelum.com.br/wp-content/uploads/2010/05/whitehouse.png)\[/caption\]

O Amazon EC2 permite inúmeras configurações diferentes e você pode tomar diversas decisões: quantos servidores precisa, como fazer o balancing, quando colocar ou retirar um novo servidor, etc. Ele te serve com a infraestrutura que você definir, abstraindo o hardware, daí o termo **I**nfrastructure **a**s **a** **S**ervice (_IaaS_).

A outra opção é a **P**latform **a**s **a** **S**ervice (_PaaS_), que **restringe** suas opções: essa modalidade costuma definir frameworks, uso de threads, conexões, sistema de arquivos e até mesmo qual banco de dados você vai utilizar.

O Google App Engine (GAE) é hoje o líder em plataforma como serviço. Você tem uma série de restrições para utilizá-lo, e o único banco de dados que pode utilizar é um [não relacional](https://blog.caelum.com.br/bancos-de-dados-nao-relacionais-e-o-movimento-nosql/), o BigTable do Google. Nesse mesmo grupo, encaixam-se o SalesForce ([Force.com](http://www.force.com)), o Heroku e o [recentemente anunciado VMForce](http://blogs.vmware.com/console/2010/04/vmforce-and-vmwares-open-paas-strategy.html) (junto com a SpringSource).

**Por que então alguém utilizaria o _PaaS_ em vez de _IaaS_, se teremos restrições?**

Como Krishnan Subramanian descreve [em](http://www.cloudave.com/link/paas-is-the-future-and-heroku-wants-to-be-part-of-it-part-1) [três](http://www.cloudave.com/link/paas-is-the-future-of-cloud-services-vmforce-a-marriage-of-convenience) [artigos](http://www.cloudave.com/link/paas-is-the-future-of-the-cloud-services-heroku-is-ready-to-be-there), o PaaS abstrai todo o ambiente e middleware para você, até mesmo as decisões operacionais e de configuração. Ele vai além, e diz que o PaaS é quem vai ganhar grande parte do mercado, dado que cada vez mais as aplicações, mesmo as que não necessitam (ao menos por enquanto) de grande escalabilidade, vão migrando para o Cloud. Colocar sua aplicação no PaaS é muito mais simples que no IaaS, mas você tem de pagar o preço: suas restrições.

A [Caelum usa o Cloud do Google App Engine](https://blog.caelum.com.br/o-cloud-computing-e-inevitavel/) há mais de 6 meses, e isso já nos salvou de picos de acesso ao sistema de reserva e acesso ao site, como quando [a Info Online noticiou nossas apostilas](http://info.abril.com.br/noticias/carreira/aprenda-java-de-graca-na-internet-24112009-9.shl) ou quando enviamos [nossa newsletter](http://www.caelum.com.br/newsletter/) para os milhares de usuários cadastrados. E, mesmo para um site pequeno como o da Caelum, sem grandes requisitos de escalabilidade, a tranquilidade de saber que ele não ficará indisponível por causa de picos momentâneos é o grande atrativo. Além, claro, da enorme redução de trabalho com infraestrutura e custo.

E você, planeja migrar sua aplicação para o Cloud e diminuir a dor de cabeça com escalabilidade? Ela está apta para enfrentar bancos não relacionais ou restrição a threads e file system no PaaS, ou você vai direto para o IaaS, tendo total controle porém mais trabalho operacional?
