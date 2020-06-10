---
title: "Eclipse 3.2 Milestone 5"
date: "2006-03-07"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Dia 17 de fevereiro saiu uma nova milestone do [Eclipse 3.2](http://www.eclipse.org/eclipse/development/eclipse_project_plan_3_2.html), dando mais um passo em direção da versão final, prefista para o final de Junho de 2006. Logo em seguida lançaram o milestone 3.2 M5a (download [aqui](http://download.eclipse.org/eclipse/downloads/drops/S-3.2M5a-200602231656/index.php)), para facilitar a retrocompatibilidade de plugins.

Sempre que uma nova milestone do Eclipse é lançada, o projeto monta uma página onde você pode ver as novidades desta versão. É a famosa [News and noteworthy](http://download.eclipse.org/eclipse/downloads/drops/S-3.2M5a-200602231656/eclipse-news-M5.html). Uma grande quantidade de novos quick fixes e code assists é adicionada, além de correção de bugs, novas views e funcionalidades.

Uma pequena mudança que me deixou muito contente é que agora o compilador incremental do Eclipse não se perde inteiramente quando um erro de sintaxe mais grave aparece.

Repare a diferença, antes do M5 (na foto em questão o M4):

\[img:eclipse\_32M4\_compilacao.PNG,full,vazio\]

O mesmo código no M5 (agora o quick fix percebe nosso erro de digitação):

\[img:eclipse\_32M5\_compilacao.PNG,full,vazio\]

Esta nova funcionalidade da uma ajuda incrível: em diversos momentos quando estamos codificando rapidamente, não queremos que erros de compilação evitem com que possamos prosseguir na linha de pensamento. É entediante ter de voltar no for e preenche-lo, ou comentá-lo, para poder prosseguir e continuar com os hints do eclipse.

Existe uma grande resistência para as pessoas usarem algo considerado beta. Um milestone do Eclipse é mais que um beta, é um artefato que estava previsto de ser lançado em uma determinada data, com determinadas funcionalidades: é uma iteração do projeto. Se você nunca usou um milestone do Eclipse recomendo altamente que você faça uma tentativa. Ficará espantado com a quantidade de novas features do 3.2, e a grande maioria de seus plugins do 3.1 estarão rodando sem maiores problemas.
