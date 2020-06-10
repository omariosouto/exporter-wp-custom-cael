---
title: "JBoss AS 7 - inovação nos servidores Java EE"
date: "2011-10-13"
author: "hannelita"
authorEmail: "hannelita@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Desenvolver com Java para web permite a criação de aplicações robustas e escaláveis. Entretanto, uma das principais queixas dos desenvolvedores consiste na questão dos servidores. De modo geral, diferente de Ruby ou PHP, criar e manter aplicações para web com Java consiste em ter que reiniciar o servidor a cada mudança nas classes, uma vez que Java não é uma linguagem interpretada. Esse "tempo gasto" com o servidor originou algumas frases que devem lhe soar de modo familiar, tais como: "_Esse servidor é muito lento para startar!_" ou "_Dá tempo de tomar cinco cafés até que o deploy termine!_". Outro ponto de incômodo consiste na questão da complexidade de alguns servidores. Você já deve ter ouvido alguma pergunta do tipo "_Qual desses infinitos XMLs devo modificar para ativar o que eu preciso?_". Muitas vezes os servidores Java EE, por mais ricos e robustos que fossem, geravam alguma confusão e certo transtorno.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/10/Screen-shot-2011-10-13-at-4.44.32-PM-245x300.png "Screen shot 2011-10-13 at 4.44.32 PM")](https://blog.caelum.com.br/wp-content/uploads/2011/10/Screen-shot-2011-10-13-at-4.44.32-PM.png)

Eis que o [JBoss AS 7](http://www.jboss.org/as7) vem para derrubar algumas dessas questões que geravam um enorme incômodo nos desenvolvedores. Usamos o JBoss AS há muito tempo, inclusive no nosso [curso FJ-31](http://www.caelum.com.br/curso/fj-31-java-ee-web-services/), e o primeiro ponto que impressiona no AS 7 é seu tempo de start - em cerca dois segundos ou menos é possível iniciar uma instância do servidor (levantando alguma aplicação que esteja na pasta de deploys também). Não, você não leu errado, aproximadamente dois segundos. Para usuários de versões antigas do JBoss AS isso pode parecer um pouco absurdo, uma vez que o AS 5, por exemplo, tinha fama de levar muito tempo para iniciar após a realização de um deploy.

O Jboss AS 7 foi completamente reescrito, deixando de utilizar o JBoss Microcontainer para dar lugar ao [JBoss Modular Service Container](https://docs.jboss.org/author/display/MSC/Home) (MSC). Basicamente, em vez de adotar dependências de modo hierárquico na hora de iniciar o servidor, o MSC define dependências explícitas de modo similar a um grafo, sendo possível levantar diversos serviços de forma concorrente.

_Mudanças em relação a versões antigas do JBoss AS_

Falando em outras versões do JBoss AS, uma das mudanças que ocorreram consta no script de start. Não há mais o `run.bat` ou `run.sh`; este foi substituído pelo `standalone.bat` ou `standalone.sh`. Essa mudança se deu porque o AS 7 agora pode operar em dois modos: standalone, que é similar às versões anteriores do JBoss AS, onde um único processo é iniciado; ou operar em Managed Domain, onde múltiplas instâncias são iniciadas e regidas por uma instância central, tal qual mostra a figura abaixo:

\[caption id="attachment\_4579" align="aligncenter" width="300" caption="JBossAS7 domain"\][![](https://blog.caelum.com.br/wp-content/uploads/2011/10/Screen-shot-2011-10-07-at-8.02.44-AM-300x186.png "JBossAS7 - domain")](https://blog.caelum.com.br/wp-content/uploads/2011/10/Screen-shot-2011-10-07-at-8.02.44-AM.png)\[/caption\]

Muito simplificadamente, a imagem acima ilustra o funcionamento do JBoss AS 7 em modo domain. Basicamente, um processo central (Domain Controller) rege múltiplas instâncias do AS 7 contidas em hosts diversos. Operar em modo domain é bastante interessante. Você pode obter mais informações na [documentação oficial do JBoss AS 7](https://docs.jboss.org/author/display/AS7/Admin+Guide#AdminGuide-Operatingmodes).

_JBoss AS 7 em produção_

A boa notícia é que a maioria das aplicações Java EE que rodavam em versões antigas do JBoss AS e em outros servidores Java EE ou até mesmo servlet contaners, tais como Jetty e Tomcat, provavelmente funcionarão sem nenhuma configuração adicional ao serem movidas para o JBoss AS 7. Entretanto, é possível que seja necessário um ajuste ou outro devido ao Class Loading do AS 7, que é consideravelmente diferente de outros servidores, podendo carregar dependências equivocadas. Na maioria dos casos, declarar explicitamente as dependências no `MANIFEST.MF` resolve a os problemas. Por exemplo, para utilizar versões antigas ou que não sejam padrão do slf4j, basta adicionar ao `MANIFEST.MF` a seguinte linha:

\[code\]Dependencies: org.slf4j.jcl-over-slf4j\[/code\]

Apesar de novo, já há projetos fazendo uso do JBoss AS 7 devido a suas inúmeras vantagens. Por exemplo, o [Openshift](http://openshift.redhat.com/), projeto de Cloud da Red Hat, utiliza o AS 7. Você inclusive pode realizar deploy de suas aplicações Java gratuitamente lá. O [Torquebox](http://torquebox.org/), projeto que possibilita a criação de aplicações com JRuby on Rails, roda no AS 7 a partir da versão 2.

O JBoss AS 7 é bastante promissor para o desenvolvimento Java para web. O vídeo a seguir mostra um pouco dele na prática.

<iframe src="http://player.vimeo.com/video/30497673?title=0&amp;byline=0&amp;portrait=0" width="400" height="225" frameborder="0" webkitallowfullscreen allowfullscreen=""></iframe>

Você pode baixar o JBoss AS 7 [aqui](http://www.jboss.org/jbossas/downloads/). Caso queira informações extras sobre a estrutura do AS 7, leia [esse post](http://relation.to/21328.lace) publicado no In Relation To.
