---
title: "JustJava 2007, Arquitetura e Caelum"
date: "2007-10-08"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Neste último JustJava palestrei juntamente com o [Phillip Calçado](http://blog.fragmental.com.br/) a respeito das novidades em relação a arquitetura Java. Aproveitei para extrair conhecimento e idéias do Phillip, que sempre anda muito ligado com as novidades.

[![DSC00334](http://farm3.static.flickr.com/2418/1492388332_23b8d038f8_m.jpg)](http://www.flickr.com/photos/silveira/1492388332/ "Photo Sharing") [![DSC00359](http://farm3.static.flickr.com/2065/1492391390_200ff4a90a_m.jpg)](http://www.flickr.com/photos/silveira/1492391390/ "Photo Sharing")

Como a apresentação é bem sucinta, vale falar um pouco sobre ela. O intuito foi mostrar as arquiteturas e designs enlatados e que durante muito tempo reinaram por aí, seja na forma de Core J2EE patterns mal aplicados ou no mal uso de clusters. O início da palestra mostra diversos pontos do nosso cotidiano de décadas passadas: destaque para os design patterns ValueObject (na realidade TransferObject), BusinessDelegate e ServiceLocator. Esses design patterns faziam muito sentido quando no J2EE os entity beans não eram serializáveis, e sim acessados remotamente, além de que não existia injeção de dependência.

Apesar do Java EE 5.0 trazer essas novidades, muita gente acaba aplicando esses design patterns sem **nenhuma** necessidade, o que acaba criando o padrão carinhosamente chamado de **BOLOVO** pelo Phillip: classes como UsuarioBusinessObject (**BO**), UsuarioLayerObject (**LO**), UsuarioValueObject (**VO**), UsuarioXYZ, etc.

Toda vez que uma classe de domínio é criada em um sistema como esses, suas irmãs também aparecem: BOs, VOs, LOs, XYZs. Utilizar TOs apenas para transportar objetos entre camadas (e não tiers) não faz sentido algum, polui o código e diminui flexibilidade e manutenção. Separar funcionalidade e dados entre BOs e VOs é outro grande problema: onde está a orientação a objetos? Entity [beans apenas com getters e setters é um mau sinal](https://blog.caelum.com.br/2006/09/14/nao-aprender-oo-getters-e-setters/).

Passamos por Model Driven Design, dando alguns exemplos com código de Domain Driven Design e de Domain Specific Languages. Por último atacamos SOA, comparando o modelo WSDL/SOAP com o Plain Old XML (POX), além de outras alternativas. Cada forma de webservices, seja SOAP, POX ou JSON, tem seu caso de uso. O que mostramos na palestra foi que não deve-se optar diretamente por SOAP sem antes pensar bem nas necessidades para aquele serviço. O mesmo para os design patterns, práticas e idéias aqui discutidos ou até mesmo criticados: cada um tem seu lugar, não utilize-os apenas porque estão em um livro ou já foram muito usados em prévias arquiteturas.

No final falamos que não há uma arquitetura enlatada que possa resolver todos nossos problemas, e que devemos tomar muito cuidado para não construir um monstro para resolver um pequeno sistema. Cada projeto deve ter sua arquitetura muito bem estudada, com todas as suas particularidades.

Na sexta feira ainda tivemos uma palestra de Lucene apresentada pelo Guilherme Moreira. Aproveitando a ocasião, este domingo ocorreu uma confraternização e reunião da Caelum para discutir idéias, projetos e metas.

[![DSC00377](http://farm3.static.flickr.com/2097/1508103467_bbe71cdf86_m.jpg)](http://www.flickr.com/photos/silveira/1508103467/ "Photo Sharing") [![DSC00389](http://farm3.static.flickr.com/2292/1508110085_abfe352ec2_m.jpg)](http://www.flickr.com/photos/silveira/1508110085/ "Photo Sharing") [![Time Caelum Out/2007 faltando 3 pessoas](http://farm3.static.flickr.com/2293/1508985408_f2e0caf18d_m.jpg)](http://www.flickr.com/photos/silveira/1508985408/ "Photo Sharing") [![DSC00392](http://farm3.static.flickr.com/2105/1508969214_741b8fdf29_m.jpg)](http://www.flickr.com/photos/silveira/1508969214/ "Photo Sharing")

Tivemos novos participantes, os mais novos integrantes e colaboradores da Caelum: [Jonas Abreu, Cecília Fernandes](http://vidageek.net/), [Alexandre Magno](http://amagno.blogspot.com/), [Danilo Sato](http://www.dtsato.com/blog/default/) e Ricardo Nakamura! O time está crescendo. Nas fotos faltam apenas o Carlos Felício, Suellen Campana e o Rafael Consentino.
