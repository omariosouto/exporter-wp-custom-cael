---
title: "Projeto open source: Caelum git-reports"
date: "2009-07-03"
author: "pmatiello"
authorEmail: "pedro.matiello@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Completamos recentemente, aqui na Caelum, a migração de todos os nosso projetos para [git](http://git-scm.com/ "git"), o sistema de controle de versões distribuído criado inicialmente por Linus Torvalds para o desenvolvimento do kernel Linux. Esta migração, realizada pelo Cauê Guerra e pelo Jonas Abreu, foi motivada por algumas características do git que se mostraram úteis em alguns projetos internos e pessoais; em especial, a possibilidade de fazer commits locais (muito útil quando não há acesso à internet) e os branches locais baratos.

Em vez de mantermos nós mesmos nossos repositórios, optamos por hospedá-los no [GitHub](http://github.com/ "GitHub"). Esse serviço oferece uma interface web bastante prática e nos permite, convenientemente, centralizar nossos [projetos públicos](http://github.com/caelum/ "projetos públicos") e privados em um único serviço. Contudo, entre tantas facilidades, sentimos falta de uma forma de observar a atividade recente em todos os nossos repositórios de forma centralizada e de ter alguma medida da atividade de cada desenvolvedor em cada projeto - e vice-versa. E para atender a essa necessidade, eu e o Cauê Guerra escrevemos o [Caelum git-reports](http://github.com/caelum/git-reports/ "git-reports"), um pequeno projeto em Ruby.

O software gera, a partir de um conjunto de repositórios **git**, uma medida do impacto de cada desenvolvedor (discriminado por projeto), do impacto em cada repositório (discriminado por desenvolvedor) e um resumo de todos os commits recentes, apresentando o resultado em HTML. Adotamos a mesma definição de impacto usada pelo GitHub: a soma das linhas acrescentadas e removidas. De fato, uma medida bastante grosseira, mas que tem suas utilidades. O projeto é muito novo, e podemos facilmente adicionar novas funcionalidades, como critérios para verificação de atividade nos repositórios, além de gráficos.

Quem quiser saber mais sobre o **git** pode consultar o [tutorial para preguiçosos](http://www.spheredev.org/wiki/Git_for_the_lazy "tutorial para preguiçosos") como ponto de partida. Outros recursos úteis podem ser encontrados no [site oficial](http://git-scm.com/documentation "site oficial") e no [índice de guias](http://github.com/guides/home "índice de guias") do GitHub. Quem prefere o formato de vídeos também deve conferir o [GitCasts](http://www.gitcasts.com/ "GitCasts").
