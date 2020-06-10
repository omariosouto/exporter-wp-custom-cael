---
title: "Revisão de código é mais do que revisar código?"
date: "2015-01-06"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Apesar de não ser uma das práticas mais populares em times ágeis (ela não é nem citada no [questionário de 2014 da VersionOne](http://stateofagile.versionone.com), por exemplo), a revisão de código vem ganhando seu espaço nas equipes de desenvolvimento de software. Afinal, as ditas vantagens da prática são várias: a diminuição na quantidade de defeitos e aumento da qualidade do código produzido, facilitando sua manutenção e evolução.

O próprio nome _"revisão de código"_ deixa claro o objetivo da prática. A ideia é que o código escrito por um desenvolvedor, antes de ser promovido ao ambiente de produção, seja revisado por outro membro da equipe. Essas revisões podem ser feitas de diversas maneiras, como, por exemplo, programação pareada, ou mesmo navegando pelos artefatos modificados. Comumente, o revisor anota todos os problemas encontrados e devolve ao autor original daquele código. O autor então avalia os comentários recebidos e eventualmente os propaga para o código-fonte.

Na Caelum, em particular, as equipes fazem uso do próprio Github, o serviço de hospedagem de código, para fazer suas revisões. Após a finalização de uma história, o desenvolvedor anota a lista de _commits_ e artefatos modificados. Um outro membro do time, em posse dessa lista, inspeciona todo o código modificado; todo e qualquer problema encontrado pelo revisor é salvo em comentários no próprio Github. O desenvolvedor original, ao receber os e-mails enviados automaticamente pela ferramenta, discute e tira dúvidas com seu revisor, e eventualmente altera o código para satisfazer a sugestão.

Interessantemente, imaginamos que um código que tenha passado revisão, tenha uma qualidade melhor, seja menos complexo, mais simples e fácil de ser entendido e mantido. Mas será que isso acontece sempre e de maneira natural? Resolvemos fazer um pequeno estudo dentro das equipes da Caelum para entender quais eram os benefícios que a revisão de código nos trazia.

O primeiro passo foi tentar medir o aumento da qualidade de código. Decidimos automatizar isso olhando para nossos repositórios no Github. Dado que todas as nossas revisões acontecem no Github, decidimos pegar todas as classes que foram revisadas, e a versão dela antes e depois da revisão. Por exemplo, a classe _Aluno_. Pegamos a quantidade de linhas de código antes e depois da revisão. A ideia é que esse número diminua. Fizemos isso com várias métricas de código diferentes, como [complexidade ciclomática](https://blog.caelum.com.br/medindo-a-complexidade-do-seu-codigo/), linhas de código, [falta de coesão dos métodos](https://blog.caelum.com.br/como-medir-a-coesao-lcom/) e [acoplamento eferente](https://blog.caelum.com.br/orientacao-a-objetos-uma-outra-perspectiva-sobre-o-acoplamento/).

E, para nossa surpresa, percebemos que os números não mudaram tanto assim após uma revisão. **Ou seja, a qualidade do código, sob o ponto de vista dessas métricas, continuou a mesma na maioria absoluta das vezes.** No desenho abaixo, você pode ver que a complexidade do código não mudou na maioria das vezes. Interessante, não?!

[![cweb-CCResult](https://blog.caelum.com.br/wp-content/uploads/2014/11/cweb-CCResult-300x300.png)](https://blog.caelum.com.br/wp-content/uploads/2014/11/cweb-CCResult.png)

Na sequência, optamos por mandar questionários para nossos desenvolvedores, perguntando a opinião deles sobre esses resultados, que eram bem contraditórios.

E a resposta deles foi bastante interessante! Os resultados mostraram que nossas equipes se beneficiam da inerente disseminação de conhecimento que acontece ao ler o código produzido por outro desenvolvedor. A percepção de redução de defeitos também acontece, já que o revisor comumente encontra problemas no código produzido pelo desenvolvedor original. Os desenvolvedores também têm a percepção de melhoria da qualidade interna, embora isso não tenha se refletido nas métricas de código coletadas.

**Ou seja, a revisão de código não serve só para melhorar a qualidade do código, mas também para disseminar conhecimento entre os membros da equipe, e ajudar a encontrar bugs que o primeiro desenvolvedor deixou passar.**

Se você quiser ler melhor sobre nosso estudo, pode ler [o artigo que publicamos no WBMA 2014](http://www.aniche.com.br/wp-content/uploads/2013/04/wbma-final3.pdf), o workshop acadêmico que acontece junto com a Agile Brazil. Em [nossos cursos de agilidade](https://www.caelum.com.br/curso-praticas-ageis/), discutimos o uso de diversas práticas como revisão de código e programação pareada.

E você, faz revisão de código? Em sua opinião, no que ela ajuda sua equipe?
