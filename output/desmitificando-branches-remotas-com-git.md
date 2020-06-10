---
title: "Desmistificando branches remotas com Git"
date: "2013-04-18"
author: "williammizuta"
authorEmail: "william.mizuta@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Você já usa [o básico do Git](https://blog.caelum.com.br/passo-a-passo-para-comecar-com-git-e-novo-curso-online/) e quer dar um passo além: trabalhar com os temidos branches.

[![Git-Logo](https://blog.caelum.com.br/wp-content/uploads/2013/04/Git-Logo-300x125.png)](https://blog.caelum.com.br/wp-content/uploads/2013/04/Git-Logo.png)

Uma das grandes vantagens do git ou de qualquer sistema de controle de versão distribuído é a possibilidade de trabalhar com branches localmente. Com isso, outras pessoas do projeto não precisam conhecer uma branch que foi criada apenas para facilitar o trabalho de um desenvolvedor. Mas quando desejamos distribuir o conteúdo de uma branch local para que outros desenvolvedores possam contribuir, precisamos que ela se torne remota.

Imaginem que uma das tarefas consiste em modificar o design de uma determinada página. Para isso, criaremos uma branch local chamada `design`, onde serão realizadas os commits com as alterações, e depois entramos nela:

\[code\] git branch design git checkout design \[/code\]

Agora você pode trabalhar normalmente, fazendo commits nesse branch, que ficará armazenado localmente. Dessa forma você não incomoda os outros desenvolvedores que estão trabalhando na branch master, nem precisará mandar todo o novo design pra lá num único commit!

**Criando branches remotas**

Contudo, você gostaria que uma outra pessoa também pudesse contribuir com as alterações nessa branch. No Git, para disponibilizar uma branch local remotamente basta rodar o seguinte comando:

\[code\] git push origin design \[/code\]

**Copiando uma branch remota localmente**

Agora, outras pessoas que quiserem contribuir numa determinada branch remota, precisam copiá-la localmente. Isto é feito com o seguinte comando:

\[code\] git checkout -t origin/design \[/code\]

Feito isso, você terá uma branch local chamanda design e pode trabalhar nela como qualquer outra branch local.

**Depois do design pronto: 'juntando' os branches**

Dentro do branch design, uma vez terminadas as modificações que queríamos fazer em paralelo, podemos jogar de volta as modificações para o branch master:

\[code\] git checkout master git merge design \[/code\]

Isso deixará o master atualizado com um novo commit, trazendo todas as modificações da branch design. Fica só faltando atualizar o master do remoto, com o simples `git push`.

Há também a opção de fazer um `rebase`, mas deve-se tomar cuidado, pois ele altera o histórico do master, tentando adicionar os commits do outro branch.

**Removendo branches remotas**

Uma vez a tarefa foi concluída e integrada na branch principal (master), a branch que criamos anteriormente não é mais necessária. Para não ficar poluindo o nosso repositório, uma boa prática é remover branches que não são mais necessárias. Para isso, basta rodar o seguinte comando:

\[code\] git push origin :design \[/code\]

Perceba que a única diferença com o comando de disponibilizar reomotamente uma branch local é um dois pontos (:).

Aprenda mais no nosso [curso de práticas ágeis](http://www.caelum.com.br/curso-praticas-ageis/) e no [curso online de Git](http://www.caelum.com.br/curso/online/git/)!
