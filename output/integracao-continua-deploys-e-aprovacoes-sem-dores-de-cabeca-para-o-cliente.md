---
title: "Integração contínua: deploys e aprovações sem dor de cabeça para o cliente"
date: "2010-01-18"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em 2008 comentamos sobre [a importância de integração contínua](https://blog.caelum.com.br/integracao-continua/) no processo de receber feedback rápido sobre suas mudanças em um sistema e depois sobre os [problemas que surgem](https://blog.caelum.com.br/integracao-continua-builds-rapidos-com-grids-e-paralelismo/) quando um sistema possui baterias de teste muito grandes e complexas.

Um das grandes vantagens da agilidade consiste em poder efetuar mudanças sem medo e receber as respostas rapidamente em relação aos bugs que introduzimos no sistema, [evitando mais erros](http://www.jacoozi.com/blog/?p=11) e, para atingir esse objetivo, um servidor de [integração contínua](http://martinfowler.com/articles/continuousIntegration.html) deve integrar o nosso código com aquele existente e rodar a bateria de testes automatizados a cada commit, criando um relatório do que foi feito que pode ter quebrado a aplicação.

Existem diversos níveis de adoção de um servidor de integração contínua em um projeto, indo do mais básico que envolve a simples compilação de um projeto (quando a linguagem é compilada) e a execução de testes unitários, até cenários mais complexos. Uma das principais dores de cabeça que um cliente enfrenta, mesmo em empresas que adotam [metodologias ágeis](http://agilemanifesto.org/) como [XP](http://www.extremeprogramming.org/) e [Scrum](http://www.caelum.com.br/curso/pm-83-gerenciamento-agil-projetos-scrum/), está ligada ao produto ser colocado em produção e não funcionar: a famosa frase "mas no meu computador funcionou".

Um cenário clássico envolve o desenvolvimento de testes unitários, que são rodados na máquina do desenvolvedor e no servidor de integração contínua. O desenvolvedor chama então o cliente para testar em sua máquina, que aprova a funcionalidade e depois de duas semanas é feito o deploy da aplicação quando, para a surpresa do cliente, ela não funciona como esperado.

Acontece que a máquina de desenvolvimento em geral não possui a mesma configuração - software de banco de dados, servidor, proxies, firewall ou até mesmo os dados contidos no banco - que o sistema em produção, e mudanças do gênero podem significar resultados completamente diferentes para o cliente que, no fim da iteração, não obtém o valor que esperava.

A solução está em criar um sistema de homologação e diversas empresas adotam essa prática. A dificuldade encontrada nessa etapa é a da não automatização do processo de réplica da estrutura e dados de produção para homologação. Dados antigos ou inadequados no ambiente de aprovação facilitam um aceite errôneo por parte do cliente no momento do teste. Na Caelum e em nossos clientes que possuem sistemas que efetuam deploy diversas vezes em um ano, para garantir a competibilidade da empresa, é comum automatizar o processo de deploy ao ponto de [maximizar as semelhanças entre produção e homologação](http://martinfowler.com/articles/continuousIntegration.html#TestInACloneOfTheProductionEnvironment), e minimizar as idas e vindas de uma funcionalidade.

Passo a passo junto com o cliente, cada fase é incluída no [pipeline de integração contínua](http://www.thoughtworks-studios.com/cruise-release-management). Primeiro o projeto é compilado, depois os testes unitários são executados. Essas duas fases do pipeline são padrão para linguagens compiladas como Java. Em algumas empresas devido ao código legado ter um acoplamento muito alto, essa primeira fase já envolve mudanças na maneira de trabalhar e na qualidade do código gerado pelos desenvolvedores.

As fases seguintes variam bastante de acordo com projeto, mas para melhorar o deploy que minimize o número de tentativas de aprovação é importante adicionar três fases: testes [end-to-end automatizados](http://www.slideshare.net/spriebsch/end-to-end-web-testing-with-selenium-presentation), deploy para homologação e para produção em um clique.

Na primeira fase, os testes executam as funcionalidades como o cliente faria e cobrem a maior quantidade de funções plausíveis de automatização. Na segunda fase, os desenvolvedores podem optar por deployar para um sistema de homologação, onde o cliente aprovará com os dados copiados de produção (alterações ligadas a sigilo de dados devem ser aplicadas).

Por fim, quando o sistema está aprovado em homologação, com um clique o desenvolvedor efetua o deploy para produção. Nesse instante, os sistemas são restartados, de preferência com [o uso de load balancers](https://blog.caelum.com.br/melhorando-o-guj-jetty-nio-e-load-balancing/) que permitem o restart sem a queda da aplicação, e o cliente está pronto para usar as funcionalidades novas.

Toda essa automatização tem um custo inicial de desenvolvimento que é compensado com a minimização de erros humanos no processo de deploy e na lentidão do mesmo. Muitas vezes o processo de deploy para qualquer um dos dois ambientes não é feito tão frequentemente devido ao processo ser demasiadamente complexo para ser executado manualmente. A chance de funcionar em produção é maximizada uma vez que a mesma foi testada em homologação com uma cópia mais fidedigna do sistema de produção possível: menos reclamações e tempo perdido pelo cliente.

Note que nenhuma ferramenta específica é necessária para adotar essa prática, basta mudar o método de trabalho.
