---
title: "Desenvolvimento: o dia que o meu projeto parou"
date: "2010-04-20"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Existem diversos tipos de débitos e o que todos eles tem em comum é que tornam a manutenção de um sistema muito custosa e delicada.

Por mais de dois anos, a Caelum tem feito um [esforço sobre cortar diversos tipos de débitos técnicos](https://blog.caelum.com.br/o-processo-de-deploy-continuo/), incluindo levar práticas ao extremo, como [testes end-to-end em grid](https://blog.caelum.com.br/integracao-continua-builds-rapidos-com-grids-e-paralelismo/).

Uma forma de enxergar que devemos melhorar o processo de desenvolvimento é verificar que perdemos dinheiro ao tomar decisões [ou muito cedo](http://agilenomundoreal.wordpress.com/2010/03/30/um-produto-por-semana/), [ou tarde demais](http://agilenomundoreal.wordpress.com/2010/03/04/como-criar-e-priorizar-um-backlog/).

O **descuido** gera débitos que cortam a produtividade de sua equipe. Também o **excesso de cuidado**, [gera muitas trocas de decisões, visando o preciosismo técnico](http://www.wired.com/magazine/2009/12/fail_duke_nukem/), pode levar a um atraso muito grande.

Além disso, uma empresa que espera 4 meses para colocar algo em produção pode ser uma empresa 4 meses atrás de seus concorrentes. Entregar valor tardio é ficar para trás.

Mas o que causa empresas ágeis a não entregar tão frequentemente?

Muito produtos criados em ambientes ágeis só são colocados em produção depois de muito tempo. Durante todo esse tempo, os únicos a testarem e aprovarem as funcionalidades são a equipe de QA e o Product Owner. Ao colocar este produto em produção depois de um ciclo tão grande, o cliente final vai encontrar [os problemas que costumavam aparecer em metodologias tradicionais](http://en.wikipedia.org/wiki/IBM_Rational_Unified_Process).

No lado gerencial, o Product Owner apresenta dificuldades em priorizar seu backlog de maneira a entregar valor, acabando por entregar funcionalidades. Além disso, é dificil aceitarmos algo "incompleto para o uso mínimo" e, na visão de quem é dono de um produto, o "mínimo necessário" acaba sendo maior do que o real e nunca é atingido para ser colocado em produção. É aí que seu projeto para: ele ficara sem entregas durante muito tempo.

Existe uma prática bem simples para um PO executar e perceber se possui essas dificuldades. Olhe seu penúltimo sprint e veja as funcionalidades que já estão sendo utilizadas por seu cliente. Agora jogue na fórmula:

> GPD = (salario da equipe \* pontos\_desnecessários) / pontos\_do\_sprint

GPD é o **G**asto que você teve com **P**riorização **D**esnecessária durante o penúltimo sprint. Ser ágil é entregar valor, e [seus pontos estão sendo utilizados em funcionalidades completas sem tanto valor](http://www.dtsato.com/blog/2009/07/04/velocity-gone-wrong-2-making-up-points/). Se existiam outras tarefas que entregavam valor para os usuários, a priorização poderia ter sido melhor.

Outra possível melhora na parte gerencial está na execução de testes somente por QAs e POs: o feedback não vem do cliente final. Devemos entregar cedo e frequentemente para receber feedback real, e não apenas do PO. Mas como alcançar isso se o produto possui um conjunto mínimo de funcionalidades, que é razoavelmente grande, para entrar em produção?

Assim como diversos ramos de software, adote o conceito de usuários beta. Ele possuem acesso a suas funcionalidades mais cedo, em servidores suficientemente estáveis que acessam dados de produção, rodando a última versão de seu produto, mas com o risco de algo dar errado. O conceito de beta potencializa a propaganda do seu produto, [podendo criar uma legião](http://www.uberreview.com/2008/01/25-signs-that-you-might-be-an-apple-fanboy.htm) de fanboys.

Na parte técnica, desenvolvedores tem percebido cada vez mais a importância de todos tipos de testes automatizados, controle de versão, integração contínua e outras práticas com base em XP, mas outra causa ainda mais comum de problemas em diversas empresas é [o truck factor](http://www.agileadvice.com/archives/2005/05/truck_factor.html). Se durante a reunião de planejamento alguém responde que determinada tarefa é dele - pois ele "conhece mais aquela parte do sistema" - ou se alguém é o único que vota os pontos de parte do sistema; ou ainda se é comum esperarem por alguém para executar qualquer tarefa, está na hora de compartilhar mais o conhecimento e acabar com esse impeditivo antes que o _truck_ acabe com ele. Parear e ensinar mais o que conhecemos permite que, ao invés de produzirmos por nós mesmos, diversas pessoas produzam através do que compartilhamos. Essa é a mágica do ensino, levado ao pareamento. Não parear por causa daquela parte ser responsabilidade de uma única pessoa é um paradoxo! É justo esse tipo de caso que mais ganha vantagens do pareamento.

O outro fator para desenvolvedores e sysadmins se perguntarem é quanto tempo leva para fazer deploy? Alguns dias? Se sim, a metodologia ágil que foi alcançada perdeu grande parte do seu valor pois o feedback rápido virou feedback semanal ou ainda mensal.

Apesar de processos de build automatizados junto com integração contínua serem importantes, [deploy contínuo é cada vez mais fundamental](https://blog.caelum.com.br/integracao-continua-deploys-e-aprovacoes-sem-dores-de-cabeca-para-o-cliente/).

Essas e outras práticas, gerenciais ou técnicas, servem para encontrar o que está implicando na não-entrega de valor ao cliente. Depois de detectado esse gargalo, devemos agir para melhorar nosso processo de desenvolvimento.
