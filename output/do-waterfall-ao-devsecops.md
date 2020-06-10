---
title: "Do Waterfall ao DevSecOps"
date: "2019-03-13"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Desenvolver um software do zero e colocá-lo em produção é um grande desafio para muitas organizações. Principalmente se for um software complexo e que atenda a diversas áreas de negócios distintas.

Há algumas décadas esse problema não existia, pois bastava apenas entender o que precisava ser desenvolvido, escrever o código e então colocar em produção.

Naquela época a tecnologia era bem limitada, os problemas eram mais simples - poucas pessoas participavam do processo e o ritmo de evolução das necessidades e problemas não era frenético como é hoje em dia. Era muito mais fácil de se organizar e sair do outro lado.

Mas conforme o mundo foi evoluindo, a tecnologia, os problemas e as necessidades das organizações também foram, exigindo assim que as equipes de desenvolvimento de software buscassem por maneiras mais organizadas e padronizadas de realizar o seu trabalho, que estava se tornando cada vez mais complexo.

## Waterfall

\[caption id="attachment\_11388" align="alignnone" width="1024"\][![Etapas do modelo Waterfall](http://blog.caelum.com.br/wp-content/uploads/2029/02/waterfall-1024x631.png)](http://blog.caelum.com.br/wp-content/uploads/2029/02/waterfall.png) Etapas do modelo Waterfall\[/caption\]

 

Em 1970, Winston Royce escreveu um artigo chamado **Gerenciando o Desenvolvimento de Grandes Sistemas de Softwares**, ou, originalmente, [Managing the Development of Large Software Systems](http://www-scf.usc.edu/~csci201/lectures/Lecture11/royce1970.pdf). Neste artigo, Royce descreve um processo para o gerenciamento de projetos de software de larga escala, que ficou conhecido mundialmente como Waterfall.

O autor cita que para desenvolver qualquer software, independente de seu tamanho e complexidade, existem duas atividades que sempre serão necessárias: **análise** e **codificação**.

Entretanto, com base em sua própria experiência profissional, ele afirma que no caso de softwares de larga escala que serão operados por outras pessoas, apenas essas duas atividades não são suficientes, sendo necessário adicionar ao processo mais algumas outras, para assim diminuir os riscos.

E com isso ele descreve um processo mais completo, que seria mais ideal ao lidar com projetos de software de larga escala, composto por sete etapas: **Requisitos de Sistema -> Requisitos de Software -> Analise -> Design de Código -> Codificação -> Testes -> Operação**.

Royce enfatiza que esse modelo é arriscado e sujeito a falhas, por conta de não haver feedback entre as etapas e também por conta dos testes ficarem para o final do ciclo, sendo que quaisquer ajustes necessários podem afetar todas as etapas anteriores, algo que certamente causaria um impacto no orçamento e no prazo.

O autor chega a citar que o ideal seria um modelo mais iterativo, possibilitando com isso alguns pontos de feedback para eventuais ajustes necessários, mas poucas equipes deram atenção a esse detalhe.

\[caption id="attachment\_11389" align="alignnone" width="1024"\][![Modelo Waterfall com ciclos de feedback](http://blog.caelum.com.br/wp-content/uploads/2029/02/waterfall-2-1024x619.png)](http://blog.caelum.com.br/wp-content/uploads/2029/02/waterfall-2.png) Modelo Waterfall com ciclos de feedback\[/caption\]

 

Muitas equipes no mundo inteiro adotaram o Waterfall como processo para desenvolvimento de software e após algum tempo alguns problemas foram surgindo.

Dentre os diversos problemas enfrentados pelas equipes de desenvolvimento de software que utilizavam o Waterfall, podemos destacar um: **ciclo de release muito longo**.

No modelo Waterfall a entrega é realizada apenas ao final do processo, quando o software estiver totalmente finalizado. O problema é que um software pode levar meses, ou até anos, para ser desenvolvido como um todo.

E durante todo esse tempo, infelizmente, o mundo não vai ficar "congelado", esperando o software ficar pronto. O mundo continua a girar, evoluindo e mudando mais rapidamente a cada dia, sendo que isso vai exigir que as organizações se adaptem na mesma velocidade, para não ficarem para trás.

Por conta disso, o modelo Waterfall acabou caindo em desuso pelas equipes de desenvolvimento de software, que passaram a buscar por outros modelos mais leves e que favoreciam ciclos mais curtos.

Para resolver esses problemas, diversos outros modelos alternativos ao Waterfall foram surgindo com o tempo, como o modelo Espiral, o RAD e o RUP, mas o que se destacou e vem sendo utilizado pela grande maioria das equipes é o modelo que ficou conhecido como **Agile**.

## Agile

\[caption id="attachment\_11390" align="alignnone" width="699"\][![Agile Manifesto](http://blog.caelum.com.br/wp-content/uploads/2029/02/agile-manifesto.png)](http://blog.caelum.com.br/wp-content/uploads/2029/02/agile-manifesto.png) Agile Manifesto\[/caption\]

 

Diversos processos leves foram sendo criados na década de 90, como o Scrum, XP, FDD e Crystal, sendo que seus criadores possuíam ideais e valores em comum, que acreditavam ser importantes no processo de desenvolvimento de software.

Em fevereiro de 2001, dezessete dessas pessoas se reuniram para discutir e chegar a um consenso sobre os valores e princípios que elas acreditavam serem mais adequados ao se trabalhar com desenvolvimento de software, bem diferente do que estava sendo feito no mercado, que focava muito em processos pesados e orientados a documentação.

Nasce assim o [Agile Manifesto](https://agilemanifesto.org/), considerado como um marco histórico pela indústria de desenvolvimento de software.

Agile na verdade é um conjunto de **valores** e **princípios**, que quando aplicados por uma equipe de desenvolvimento de software, e também pela organização como um todo, favorece o processo de desenvolvimento e entrega de software que gera **valor** para sua organização.

Dentre as metodologias e frameworks que seguem o manifesto, as principais e mais adotadas hoje em dia são: [XP](http://www.extremeprogramming.org/), [Scrum](https://www.scrum.org/) e [Kanban](https://leankanban.com/project/what-is-km/).

Ao utilizar tais metodologias, as equipes de desenvolvimento de software acabam reduzindo bastante os seus ciclos de release, para conseguir entregar valor desde cedo e de maneira contínua, para obter mais feedbacks e também para diminuir os riscos.

Além disso, é comum que a comunicação e a colaboração entre as pessoas melhore bastante, fazendo com que elas se organizem como um **time**, com transparência, colaboração e metas compartilhadas, não mais se dividindo em equipes isoladas.

Esses times costumam ser auto-organizados e cross-funcionais, compostos por pessoas com diferentes conhecimentos e habilidades, para que assim consigam executar o processo do inicio ao fim, de maneira quase totalmente independente.

Porém, um problema que é comum em quase todas as organizações é que existe uma equipe responsável pela parte de infra, também chamada de operações, que geralmente trabalha de maneira isolada do time de desenvolvimento, gerando com isso alguns conflitos.

\[caption id="attachment\_11418" align="alignnone" width="1024"\][![Imagem representando o atrito entre as equipes de Dev e de Ops](http://blog.caelum.com.br/wp-content/uploads/2029/02/kong-1024x574.png)](http://blog.caelum.com.br/wp-content/uploads/2029/02/kong.png) Dev vs. Ops\[/caption\]

 

O problema é que de um lado está o time de desenvolvimento, trabalhando de maneira ágil, buscando autonomia e necessitando de ciclos de release cada vez mais curtos, mas do outro lado está o time de operações, que possui outro ritmo de trabalho e processos que exigem mais controle, governança e estabilidade.

São duas áreas com ideias, necessidades e processos distintos, que às vezes podem entrar em conflito, gerando um atrito entre os times de desenvolvimento e operações.

\[caption id="attachment\_11432" align="alignnone" width="787"\][![Imagem representando o conflito entre os processos de desenvolvimento e operações](http://blog.caelum.com.br/wp-content/uploads/2029/02/agile-itl.jpg)](http://blog.caelum.com.br/wp-content/uploads/2029/02/agile-itl.jpg) Conflito entre os processos de desenvolvimento e operações\[/caption\]

 

Para evitar problemas, essas duas áreas devem trabalhar **juntas**, de maneira **colaborativa** e com objetivos comuns, sempre buscando por ferramentas, processos e práticas que possam simplificar o trabalho, gerando com isso melhores resultados para a organização como um todo.

Baseado nessas ideias, nasce assim um novo movimento chamado DevOps.

\[caption id="attachment\_11420" align="alignnone" width="1024"\][![Imagem representando a união entre Dev e Ops](http://blog.caelum.com.br/wp-content/uploads/2029/02/devops-1024x685.jpg)](http://blog.caelum.com.br/wp-content/uploads/2029/02/devops.jpg) DevOps\[/caption\]

## DevOps

DevOps é a união entre os times de desenvolvimento e operações, que combina suas filosofias, práticas, processos e ferramentas com o objetivo de melhorar a colaboração entre essas duas áreas, para com isso ajudar a organização a conseguir entregar software de maneira mais ágil.

Ao invés desses times trabalharem isoladamente, eles se unem de maneira que consigam trabalhar juntos por todo o ciclo de desenvolvimento do software, desde o desenvolvimento e testes, até o deploy e operação dele.

Ao trabalhar dessa maneira, o time de desenvolvimento passa a ter um melhor entendimento sobre os processos, dificuldades e problemas enfrentados pelo time de operações, e vice-versa. Um dos focos passa a ser a busca por maneiras de simplificar os processos, com o objetivo de aumentar a agilidade.

Tópicos como integração/entrega contínua, monitoramento, logs e escalabilidade acabam ganhando uma maior atenção, e a utilização de ferramentas e automatização acaba sendo essencial para isso.

\[caption id="attachment\_11421" align="alignnone" width="1024"\][![Exemplo de ferramentas utilizadas no modelo DevOps](http://blog.caelum.com.br/wp-content/uploads/2029/02/devops-tools-1024x543.png)](http://blog.caelum.com.br/wp-content/uploads/2029/02/devops-tools.png) Exemplo de ferramentas utilizadas por times DevOps\[/caption\]

 

Não há dúvidas de que o modelo DevOps auxilia muito os times de desenvolvimento e operações a serem mais ágeis, entregando software em ciclos mais curtos, evitando atritos e problemas, e promovendo asssim uma melhor colaboração.

Entretanto, os problemas ainda não acabaram, pois existe uma outra área que ficou de lado, sendo que ela também é muito importante, além de também possuir seus próprios valores, práticas e processos: a área de segurança.

É muito comum que a área de segurança seja formada por um time que trabalha de maneira isolada dos times de desenvolvimento e operações, algo que certamente pode gerar problemas e atritos.

Nesse tipo de cenário, o time de segurança acaba atuando como um "porteiro", onde ao final de cada ciclo de release vai verificar se a entrega pode ou não ser liberada, baseado nas verificações de segurança que serão realizadas.

Isso pode impactar na tão desejada agilidade que o time DevOps deseja alcançar, além de também fazer com que o time de segurança seja visto como uma barreira para a agilidade e como responsável por ter que ficar arrumando a "bagunça" deixada pelo time DevOps.

\[caption id="attachment\_11422" align="alignnone" width="703"\][![Imagem representando o time de segurança limpando a bagunça deixada pelo time de DevOps](http://blog.caelum.com.br/wp-content/uploads/2029/02/devops-sec.png)](http://blog.caelum.com.br/wp-content/uploads/2029/02/devops-sec.png) Sensação do time de segurança em relação ao time DevOps\[/caption\]

 

E foi essa a motivação para o surgimento do movimento chamado DevSecOps, cujo objetivo é integrar também o time de segurança aos times de desenvolvimento e operações.

## DevSecOps

Assim como o DevOps, o DevSecOps também é um modelo que tem como objetivo a integração entre os diferentes times envolvidos no processo de desenvolvimento de softwares.

No caso do DevSecOps, a ideia é integrar o time de segurança ao time DevOps, evitando assim que o tema de segurança seja tratado apenas ao final do processo e de maneira isolada.

O "coração" do DevSecOps é construir a ideia de que **segurança é responsabilidade de todos**.

Segurança não deve ser um tópico discutido e tratado apenas ao final do ciclo de cada release e apenas pelo time de segurança, mas também deveria ser discutido durante todo o ciclo de desenvolvimento, desde as discussões sobre as funcionalidades, até a fase de deploy e operação.

Com isso, os times de desenvolvimento e operações passam a ter um melhor entendimento sobre o assunto e sobre os processos, práticas e dificuldades enfrentadas pelo time de segurança.

Certamente isso vai melhorar a agilidade do time de desenvolvimento, que passará a tratar do tópico de segurança antes e durante o desenvolvimento, evitando assim implementar funcionalidades com diversas vulnerabilidades que precisariam ser corrigidas posteriormente.

Um time DevSecOps também deve buscar por soluções e ferramentas que facilitem e automatizem as tarefas relacionadas à segurança, para simplificar os processos dessa área e evitar que ela se torne um gargalo no ciclo de desenvolvimento ágil.

Novos tópicos surgirão e precisarão de atenção, dentre eles: desenvolvimento seguro, análise de código, pentesting, detecção de ataques e logs de segurança.

Um time que trabalha utilizando o modelo DevSecOps, integrando as áreas de desenvolvimento, operações e segurança, com suas práticas, processos e ferramentas, sempre buscando por melhorias e simplicidade, certamente consegue ser mais ágil e ajudar a sua organização a alcançar melhores resultados, tornando-a mais competitiva no mercado.

E você, trabalha em um time que segue o modelo DevSecOps? Deixe um comentário contando sua experiência e aprendizado nessa jornada. :)
