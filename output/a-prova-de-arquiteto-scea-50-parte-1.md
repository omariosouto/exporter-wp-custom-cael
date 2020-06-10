---
title: "A prova de arquiteto SCEA 5.0 parte 1"
date: "2007-10-15"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Agora que já são cinco da Caelum que fizeram a [prova beta da certificação Enterprise Architect](http://www.sun.com/training/certification/java/beta_architect.xml), focada no Java EE 5.0, creio que possamos fazer um resumo sobre nossas opiniões e impressões sobre a prova, e sobre as diferenças para quem já fez a versão anterior da prova.

Vá preparado: como toda prova beta, você sai esgotado. Leve água e algum chocolate. São 153 questões em 4:30. Uma maratona. A partir do início de 2008 a prova deve oficializar e ter uma duração próxima a 2 horas, e conter cerca de 50 questões. A prova está mais difícil que a anterior: há menos questões triviais e mais textos para ler.

A prova tem algumas surpresas: existem questões que abordam ajax, outras falam sobre unit test, webservices restful, POJOs e algumas até esbarram em AOP! Um grande avanço. Um pouco mais de sorte e quem sabe teríamos perguntas sobre DSLs, DDD, MDD e testes de aceitação. Mas Service Locators, Singletons e Transfer Objects continuam por ali, mesmo a plataforma já tendo injeção de dependências e entity beans serializáveis no Java EE 5.

Não existem mais questões sobre UML. Também não perguntam mais sobre código, interfaces, ciclo de vida e outros detalhes técnicos da API de EJB. Apesar da segurança Java EE ter ganho espaço, criptografia, digests e certificados têm menor peso em relação a prova anterior. SecurityManager e policies aparecem mais.

Design Patterns aparecem bastante, como já acontecia antes. A novidade é que os Core J2EE patterns estão mais presentes, não se restringindo apenas a DAOs. Esses dois livros são leitura obrigatória para a prova, até mesmo o capítulo inicial de Design Patterns tem grande valia: temos perguntas sobre frameworks, herança, encapsulamento e orientação a objetos.

Muitas questões envolviam conhecimento de **webservices**, em especial sobre o uso das APIs JAXB, JAXP, JAXR, JAX-WS, JAX-RPC. O uso das anotações nos stateless session beans e em pojos também é abordado. Conhecimento básicos sobre SOAP, WSDL, XML também é necessário. Escolher CORBA, Webservices SOAP/WSDL, RMI, Sockets puro ou webservices restful também é cobrado na prova, porém bem menos. Sendo uma prova de arquitetura, esperávamos que esse tipo de decisão tivesse um peso maior do que o conhecimento sobre siglas, procotolos e APIs.

EJB3, JPA e JSF são as tecnologias de destaque. Existe necessidade de usar EJB em um sistema que não necessita de remotabilidade nem transações? Qual a desvantagem do JSF passar os dados via POST? Quando usar JSF e quando optar por servlet + taglibs + EL? Quando usar JPA e quando escolher JDBC direto? Vale lembrar que EJB3, JPA e JSF são as tecnologias que a Sun mais aposta hoje em dia, então a não ser que a questão de fortes indícios do contrário, a provável resposta correta envolve essas tecnologias.

Temos então as questões sobre arquitetura: tiers, layers, performance, avaliability, reliability, etc. Essas questões são sem dúvida as mais confusas. Uma arquitetura X-tiers é mais reliable que uma Y-tier? Esse tipo de questão aparece muito e alguns casos necessitaria de mais algumas (muitas) informações para uma resposta. Um outro tipo de questão de arquitetura aparece e é bem mais interessante: tradeoffs entre tecnologias como Java Web Start, Applets, Ajax ou puro HTML.

A prova é interessante, porém muitas questões continuam quase que subjetivas: dependendo de uma visão mais ampla a resposta poderia ser totalmente diferente. Retirar UML e código já foi um grande avanço, juntamente com a atualização da versão e das tecnologias.

Se você está estudando, alguns links interessantes são o [post do Urubatan sobre a prova](http://www.urubatan.com.br/2007/10/04/scea-5-impressoes-sobre-a-prova-e-comentarios-sobre-o-conteudo/) e [esse tópico do fórum no Java Ranch](http://saloon.javaranch.com/cgi-bin/ubb/ultimatebb.cgi?ubb=get_topic&f=26&t=007174). E você, tem algum link interessante? Comentários sobre a prova?
