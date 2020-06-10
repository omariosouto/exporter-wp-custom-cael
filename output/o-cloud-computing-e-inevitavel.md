---
title: "O cloud computing é inevitável?"
date: "2009-10-28"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A [Caelum.com.br](http://www.caelum.com.br) roda agora no _cloud_. Por que? Vale a pena? Temos tantos acessos assim?

[![](https://blog.caelum.com.br/wp-content/uploads/2009/10/cloud-computing.jpeg "cloud-computing")](https://blog.caelum.com.br/wp-content/uploads/2009/10/cloud-computing.jpeg)

Entre os exemplos clássicos do bom uso do _cloud_ temos [o sucesso do New York Times](http://open.blogs.nytimes.com/2007/11/01/self-service-prorated-super-computing-fun/): eles conseguiram digitalizar em PDF mais de 4 terabytes de edições do jornal muito antigas (1851-1922) usando 100 máquinas e gastando apenas 240 dólares! O _cloud_ é perfeito para situações em que você precisa de muito desempenho e recursos por um curto período de tempo.

Essa definitivamente não é a necessidade da Caelum em rodar em um _cloud_. Qual seria então?

Temos várias opções para você ter seu próprio _cloud_: através do [TerraCotta](http://www.terracotta.org/), pelo [VMWare](http://www.vmware.com/solutions/cloud-computing/) ou pelo [Grid Gain](http://www.gridgain.com/). Usando esses softwares você mesmo precisa prover sua infraestrutura de máquinas, o que pode ser trabalhoso. Um nível mais abstrato e encapsulado seria usar o [EC2](http://aws.amazon.com/ec2/) da Amazon: ele já fornece as máquinas, facilitando muito a manutenção, e cobra por tempo de processamento (eliminando o gasto inerente ao tempo ocioso que suas máquinas poderiam ficar). No Brasil, a [Locaweb](http://www.locaweb.com.br/cloud) foi a pioneira ao oferecer esse serviço.

Por último, temos hosts que já provêm tudo para você: a infraestrutura e o software para permitir a escalabilidade. O [Google App Engine](http://code.google.com/appengine/) é um desses hosts.

Aqui, sim, entra a Caelum e entra também o interesse das empresas de hosting: se o _cloud_ é mais barato para os fornecedores por compartilhar recursos ociosos, e ainda oferece altíssima escalabilidade e disponibilidade para os clientes, temos então um casamento de interesses. Essa relação ganha-ganha fortalece muito a tendência da adoção de uma arquitetura mais elástica. Os clientes também ganham uma enorme independência de hardware, terceirizando os grandes problemas que acabam acontecendo quando temos servidores dedicados: esses servidores não são à prova de bala. Para a Caelum, mesmo sem necessidade de rodar em 100 servidores, é interessante ter o poder de fazer o deploy de nossas aplicações Web sem ter de nos preocupar se "é suficiente X micros e Y de RAM para esta aplicação?", por menor que ela seja.

O site da Caelum usava [VRaptor 3](http://vraptor.caelum.com.br) como controlador, JSP e taglibs na visualização e Hibernate para persistência. Para colocá-lo no _cloud_ do Google App Engine, a única grande mudança que fizemos foi migrar a camada de persistência para o [BigTable](http://code.google.com/appengine/docs/java/datastore/). Trata-se de um banco de dados não relacional proprietário, que possui uma API específica, mas também há uma implementação (não completa) de [JPA](http://code.google.com/appengine/docs/java/datastore/usingjpa.html) para ele, o que facilita bastante a migração.

Desvantagens? O Google App Engine ainda está em beta, e já ficou [fora do ar alguns momentos](http://code.google.com/status/appengine/detail/datastore/2009/10/22), em especial o serviço do DataStore (o BigTable). Há também o problema do _cold start_ e do plugin para o Eclipse, que é um pouco lento para iniciar o servidor de testes: falaremos com detalhes sobre ambos em um outro post. Outra desvantagem é a dependência ao BigTable: como é um serviço proprietário, migrar para qualquer outro _cloud_ vai necessitar de ajustes, mesmo que usando a JPA.

Respondendo o título do post: independente se o seu _cloud_ é Amazon EC2, Google App Engine ou GridGain, algo é certo: ele traz vantagens tanto para os desenvolvedores quanto para o host, mostrando que a estratégia do _cloud_ está cada vez mais solidificada.

Agradecimentos ao Pedro Matiello, Guilherme Silveira e Sérgio Lopes pela migração do site. Em breve, postaremos detalhes de como trabalhar na Google App Engine. Veja mais [artigos de cloud no blog](https://blog.caelum.com.br/tag/cloud).
