---
title: "Prática: melhore a qualidade do código para evitar uma enchente de bugs"
date: "2010-10-14"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

**Sintoma**: o número de bugs novos que são abertos é maior do que o número de bugs fechados a cada iteração. O backlog é dominado por bugs e cada vez menos funções novas são adicionadas ao projeto.

**Ação**: Por muito tempo acreditou-se que aumentando o número de desenvolvedores aumentaria a produtividade de uma equipe em um projeto, crença hoje combatida pelos métodos ágeis.

O primeiro fator para combater a sensação de fracasso na luta contra os bugs é criar uma medida que **incentive** a equipe a melhorar a qualidade do código.

Uma maneira fácil de criar essa métrica é medir o número de bugs novos abertos e de bugs fechados no último ciclo. A cada ciclo novo meça novamente esses valores. A medida pode ser tirada automaticamente através da criação de plugins para determinadas plataformas online ou de maneira bem simples durante o review de seu projeto - manualmente.

Para aqueles que usam o pivotal tracker, [o projeto Buggie](http://github.com/caelum/buggie) integra com o processo de build e pode gerar os dados a cada novo build executado no servidor de integração, mantendo o gráfico sempre atualizado. A tabela a seguir mostra o gráfico de um projeto durante as seis meses, gerado com o Buggie:

\[caption id="attachment\_296" align="alignnone" width="380" caption="Features sendo entregues e corrigidas continuamente até o momento da grande entrega de muitas novas funcionalidades: um grande impacto no número de bugs, mas em número absoluto é o mesmo que anteriormente."\][![Aguardando algo acontecer para testar](https://blog.caelum.com.br/wp-content/uploads/2010/10/aguardo-300x235.png "aguardo")](https://blog.caelum.com.br/wp-content/uploads/2010/10/aguardo.png)\[/caption\]

Fica visualmente claro que os bugs vinham em um ritmo natural e corrigidos também da mesma maneira. Mas de repente uma avalanche de bugs aparece - e são corrigidos proporcionalmente. O feedback é instantâneo, e o time percebe que algo precisa mudar nessa parte do [processo de desenvolvimento para melhorar a qualidade de seu código](http://www.caelum.com.br/curso/pm-87-agile-praticas-ageis/).

Para melhorar a qualidade do código devemos adotar práticas como o pareamento, mais comunicação com o cliente e evitar adivinhar como as funcionalidades deveriam funcionar. A análise do gráfico de bugs junto com o histórico da equipe nos diz o que acontece e o que podemos fazer.

**Desafio para o leitor**

1\. Cite motivos pelos quais o time acima possui um acúmulo de bugs próximo a iteração 18?

2\. Quais motivos levam outro time a ter um gráfico diferente, como o a seguir?

\[caption id="attachment\_297" align="alignnone" width="380" caption="Executando a entrega contínua de um projeto, mesmo que sem o processo de deploy totalmente automatizado em um clique."\][![Fazendo a entrega contínua](https://blog.caelum.com.br/wp-content/uploads/2010/10/continuo-300x230.png "continuo")](https://blog.caelum.com.br/wp-content/uploads/2010/10/continuo.png)\[/caption\]

3\. E o seu projeto controlado pelo Pivotal? [Gere seu gráfico com o Buggie](http://github.com/caelum/buggie) e [envie via twitter](http://twitter.com/guilhermecaelum) ou [email](mailto:guilherme.silveira@caelum.com.br). Se preferir basta compartilhar o acesso ao pivotal.

Veja também: [pontos de bug contam na velocidade de meu time?](http://agilenomundoreal.com.br/2010/03/15/pontos-de-bugs-contam-na-velocidade/)
