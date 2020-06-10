---
title: "Princípios ágeis revisitados: técnicas e práticas"
date: "2015-10-27"
author: "ceci"
authorEmail: "cecilia.fernandes@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

No terceiro post da série sobre os princípios ágeis, vamos complementar as primeiras partes, voltadas para [entrega de valor](https://blog.caelum.com.br/manifesto-agil-parte-1/) e [comunicação](https://blog.caelum.com.br/manifesto-agil-parte-2/), com os quatro princípios finais, que falam de desenvolvimento em si e do importante tópico da melhoria contínua.

Vamos direto ao ponto!

#### Atenção contínua a excelência técnica e bom design engrandecem a agilidade.

Quando falamos de métodos ágeis, é muito comum que pessoas liguem tais discursos exclusivamente com processos e _soft skills_. Contudo, se pretendemos trabalhar _"em um ritmo constante, indefinidamente"_ é importante pensar também no material de trabalho de um time de desenvolvimento: no código, no ambiente de desenvolvimento, etc.

Todo desenvolvedor passa, eventualmente, por códigos de dificílima manutenção. Isso acontece por diversos fatores: ausência de testes, baixa prioridade na refatoração de códigos já existentes, falta de padronização... e as muitas e muitas gambiarras que se acumulam com o tempo.

A agilidade lembra que o time todo responsável pelo que produz -- e também é responsável por como se sentirão os próximos desenvolvedores (talvez você mesmo, no futuro?) que tiverem que lidar com esse código! Como Joe Yoder bem colocou, todo código tende a virar uma Grande Bola de Lama, então cuidados com ele têm que ser contínuos!

#### Simplicidade -- a arte de maximizar a quantidade de trabalho não realizado -- é essencial.

Embora simplicidade na comunicação e nos processos sejam **bastante importantes**, é neles que frequentemente pensamos, novamente! Decidi, então, focar em como esse princípio se aplica na engenharia.

Pense na pior classe do seu sistema. Você tem uma noção clara de qual é ela? Por que você a considera muito ruim? Muito provavelmente, a resposta tem forte relação com a complexidade dela -- seja em linhas de código, em uma forte centralização de responsabilidades, em lógicas complicadas todas em um mesmo arquivo, etc.

Já passamos do tempo de valorizar aquele desenvolvedor "tão bom que ninguém entende o código dele". Ou então aquele sistema que faz tudo o que você pode imaginar e mais um pouco. Quanto mais simples o código, melhor. Quanto menos funcionalidades pudermos ter, melhor também.

#### As melhores arquiteturas, requisitos e design emergem de times auto-organizados.

Quando pensamos em um time mais tradicional, um pequeno subgrupo de pessoas é responsável (e responsabilizável) pelas decisões arquiteturais, do que vai ser feito e, em casos mais extremos, até do design de classes - isto é, de como algo será feito.

Por mais que o responsável seja a pessoa mais experiente do time, o fato de um indivíduo tomar uma decisão que deverá ser acatada pelos outros é arriscado de várias formas! Preferimos, em agilidade, que o time todo seja envolvido nessas decisões. Embora o processo de decisão possa se tornar mais complexo assim, vemos as vantagens:

- Membros do time se sentem mais responsáveis pelo produto desenvolvido;
- Eles se sentem, também, com mais autonomia e mais liberdade;
- Quando identifica-se um problema, o foco é na busca por soluções mais do que em apontar culpados;
- Iniciantes têm a oportunidade, desde mais cedo, de entender o processo de raciocínio das decisões e, assim, evoluem mais rápido;
- Mais questionamentos são levantados e o entendimento geral do produto é maior.

Dá trabalho? Sim. Mas essa descentralização chega até mesmo a melhorar a moral, aumentar a velocidade de aprendizado e reduzir o turnover no time!

#### Em intervalos regulares, o time reflete sobre como se tornar mais efetivo e, então, sintoniza e ajusta comportamentos de acordo.

Esse é, simplesmente, o mais importante entre todos os princípios ágeis, na minha opinião. Ele nos lembra do importante foco na **melhoria contínua**, que é base da agilidade!

Não importa o quão bem seu time funcione ou o quão fantástico seu produto seja, não existe um "melhor impossível" quando lidamos com pessoas e projetos evolutivos. Com o passar do tempo, valorizamos coisas diferentes e, sem inspecionarmos a nós mesmos, poderíamos até nos afastar de nossos princípios mais básicos. E, se esse fenômeno acontece dentro de cada indivíduo, certamente acontece ainda mais em times.

É importante tirar tempo para parar e pensar no que está bem, no que deveríamos melhorar, no que poderíamos melhorar... e decidir ações a respeito desses itens, que nos levem a um lugar melhor -- seja lá o que for melhor para seu time!

### Práticas relacionadas

E, como os outros princípios, estes também são aplicados na forma de diversas práticas ágeis:

Métricas de código

É muito difícil dizer se um dado código é bom, mas é consideravelmente fácil dizer que um código é ruim! Vários projetos se propõem a tirar métricas do seu código e, se for usar alguma delas, a dica é começar com regras bem relaxadas e ir deixando-as mais estritas conforme melhora os pontos mais tensos do seu código.

Refactor Day

Separar um dia da iteração (ou da semana, ou do mês...) para focar os esforços do time apenas na melhoria do código pode trazer enormes benefícios a médio prazo: sabendo que outro refactor day acontecerá no futuro, a tendência é que as pessoas passem a perceber e se incomodar com códigos feios.

Anzeneering

Misturando a palavra japonesa _anzen_ (segurança) com _engineering_, surgiu o termo. A proposta é bem interessante: pense em como tornar seu ambiente mais seguro, tanto em aspectos pessoais, quanto na parte técnica. Várias das práticas já comentadas em posts passados figuram nesse grande guarda-chuva de conceitos e [vale dar uma olhada no post que cunhou o termo](https://www.industriallogic.com/blog/anzeneering/) para entendê-lo.

Propriedade Coletiva de Código

Vindo de XP, essa política diz que não existe uma pessoa que é responsável por um trecho de código específico: todos devem ter um senso de responsabilidade sobre todo o código, com autonomia para alterá-lo, extendê-lo ou palpitar sobre ele, seja lá quem o tenha feito.

Times multifuncionais

Em vez de trabalhar em times especializados que, até por isso, dependem fortemente uns dos outros para entregar valor, preferimos que os times sejam formados por pessoas de diferentes especialidades, tanto para aumentar sua autonomia, quanto para termos pontos de vista variados ao pensar na evolução do produto que estamos desenvolvendo!

Retrospectivas

Embora já tenha falado delas na parte II dos posts sobre princípios ágeis, vale relembrar: se você ainda não está fazendo retrospectiva, comece já. Se quiser variar a sua, recomendo dar uma olhada nas diversas opções que o Caroli e o TC sugerem no [blog Fun Retrospectives](http://www.funretrospectives.com/), que ainda está em inglês, mas deve ganhar traduções logo mais.

Feedback

E retrospectivas não são o único momento no qual podemos ajudar outros a melhorarem. Feedbacks dados continuamente podem ser poderosos para melhorias e até da motivação pessoal. Lembre-se que há quem reaja melhor a feedbacks positivos e aqueles que reagem melhor a feedback negativo -- observe seus colegas para obter melhores resultados com feedbacks bem colocados!

Conhecer os princípios ágeis é o básico para qualquer agilista praticante e vale relembrá-los de quando em quando. E, claro, cada princípio poderia ser explicado e pode ser aplicado das mais diversas formas. Que tal fazer essa discussão com seus colegas e atualizar os princípios que você considera ultrapassados? É um excelente exercício!
