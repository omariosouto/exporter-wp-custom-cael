---
title: "JBoss In Bossa 2011 - Eu fui!"
date: "2011-10-11"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

No dia 8 de outubro aconteceu em Brasília o [Jboss In Bossa](http://www.jbossinbossa.com.br). Foi a primeira vez que o evento foi realizado na capital do país e contou com uma ótima participação da comunidade e organização, destaque para [João Paulo Viragine](http://twitter.com/#!/jpviragine) (JBoss), dentre outros.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/10/jbossinbossa_header-300x79.png "jbossinbossa_header")](https://blog.caelum.com.br/wp-content/uploads/2011/10/jbossinbossa_header.png)

O Keynote ficou sob a responsabilidade do argentino [Mauricio Salatino](http://twitter.com/salaboy). A apresentação foi focada no JBPM5 e como a tecnologia ajuda na integração de sistemas, principalmente no que se concerne a interação humanas, assunto esse que muitas vezes é esquecido por nós programadores.

O palestrante se preocupou em definir o conceito de BPM, primeiramente mostrando que ele não é um integrador de sistemas ou um framework para fazer máquina de estados e page flows. A tecnologia é um descritor de sequência de atividades, cujo foco está justamente em trabalhar interação humana e sistemas juntos.

Mauricio também falou das APIs BPMN 2.0 e WS-HT (ambas implementadas no JBPM 5). Esta especifica como as pessoas interagem fazendo diferentes tarefas, inclusive a troca de informações de entrada e saída e aquela define gráficos para descrever situações de negócio. Por fim, Mauricio discorreu sobre o projeto [Smart Tasks](http://planet.jboss.org/post/smart_tasks_first_alpha_release), uma ferramenta de colaboração em tempo real.

A próxima palestra foi do _professional speaker_ [Edgar Silva](http://twitter.com/#!/jedgarsilva). Digo isso pois todo mundo que trabalha com TI e faz palestra deveria ver uma apresentação dele, não só pelo conteúdo mas também pela técnica de apresentação. Sua palestra teve como principal tema a buzzword do momento: Cloud.

Ele explicou o conceito e modelos de cloud e sua principal vantagem, a [elasticidade](https://blog.caelum.com.br/cloud-computing-na-casa-branca-e-o-paas/) e que o futuro é [KVM](http://en.wikipedia.org/wiki/Kernel-based_Virtual_Machine) e clouds híbridos. Mencionou alguns gerenciadores de cloud como [Aeolus](http://www.aeolusproject.org/) e [OpenShift](http://openshift.redhat.com/). Por fim, ele fez uma demo interessante. Clonou uma app do GIT, feita já com especificação JavaEE 6 que usava Servlet Assíncrono, e colocou no OpenShift.

Bom, o que eu mais gostei da palestra do Edgar foi a ideia central que ele deixou para nós desenvolvedores: novos paradigmas, corra atrás, atualize-se. Antigamente nós colocávamos os dados na Session, agora já existe cache distribuído. XML? E JSON, já ouviu falar? [HTML5 e CSS 3](https://blog.caelum.com.br/css3-e-o-futuro-da-web/) já é realidade. SGBD? Bom, que tal [NoSQL](https://blog.caelum.com.br/bancos-de-dados-nao-relacionais-e-o-movimento-nosql/)?

\[gallery link="file" columns="5"\]

Duas palestras foram focadas no Seam 3. Uma feita por Ricardo Martinelli (Red Hat) e a outra feita pela [Hanneli Tavante](http://twitter.com/hannelita) (Caelum). O primeiro fez uma palestra mais explicativa e introdutória, abordando desde o conceito da JSR (299) - [CDI até a diferença entre ela e a JSR (330)](http://www.infoq.com/br/presentations/cdi-weld-seam-caelumdaybsb). Mostrou também 2 apps que ele fez com CDI e Seam 3 e estão no cloud da OpenShift. Já a segunda fez uma palestra mais focada no framework Seam e abordou um discussão relevante que está acontecendo na comunidade no momento: o [futuro do Seam](http://relation.to/Bloggers/SoWhatsHappeningWithSeam).

A palestra mais interessante tecnicamente para mim foi a do Vitor e Gustavo, e isso apesar de ser um desenvolvedor e achar que a palestra foi mais voltada para o pessoal de infra. O assunto principal foi como manter uma alta disponibilidade de sua aplicação, focando em clusterização.

Eles fizeram uma conta na Amazon e subiram 6 instâncias do Jboss Server 6, sendo que uma delas era para load balancer. Eles apresentaram duas ferramentas que ajudam no gerenciamento desse cluster, o [mod\_cluster](http://www.jboss.org/mod_cluster) e o [JGroups](http://www.jgroups.org/).

A aplicação consistia em um contador que ficava na session. Essa app foi deployada em uma das instâncias do cluster. Então ele derrubou essa instância e fez uma nova requisição e a outra instância passou a responder sem perda de dados.

O assunto é interessante e requer habilidade técnica e experiência na configuração do cluster. Tecnicamente o que eu achei mais interessante foi o fato de a amazon não permitir comunicação multicast entre servidores, e eles tiveram que configurar o JGroups para não fazer o default (que seria multicast), mudando para unicast.

Um outro tema bastante discutido atualmente na comunidade, cache distribuído, foi abordado na palestra do Pedro Igor. Achei interessante pois ele mostrou na prática como utilizar o [Infinispan](http://www.jboss.org/infinispan) e o [Hibernate OGM](http://www.infoq.com/br/news/2011/06/hibernate-omg).

Particularmente estou curioso pelo o que realmente vai ser o Infinispan, até o palestrante tentou explicar os vários conceitos que o framework tem, mas creio que ainda não haja um consenso. Já sabemos que a [JSR-107 vai fazer parte do Java EE 7](http://www.infoq.com/news/2011/08/jcache-jsr-lives). Pelo que eu entendo, o projeto aborda muito mais do que a JCache, mas por palpite ele terá uma implementação da especificação.

Finalizando, o evento teve como palestra final a da Flávia Rainone, uma das arquitetas responsáveis pelo JBoss AS 7, que pelo o que eu percebi, deixou o pessoal da RedHat bastante orgulhoso. Ainda teve outras palestras que não consegui ver sobre Drools, NoSQL, Portlets mas você pode conferir também [no  review da Hanneli Tavante](http://hannelita.wordpress.com/2011/10/10/jboss-in-bossa-2011-sucesso-total/).

Você foi ao evento? O que achou?
