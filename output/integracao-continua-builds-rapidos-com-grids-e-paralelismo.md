---
title: "Integração Continua - Builds rápidos com Grids e paralelismo"
date: "2009-02-09"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Como já comentamos em um [post anterior](https://blog.caelum.com.br/integracao-continua/), aqui na Caelum fazemos a [Integração Contínua](http://martinfowler.com/articles/continuousIntegration.html) das nossas aplicações, com a ajuda de algumas ferramentas, entre elas o [Selenium](http://seleniumhq.org/), para os testes de integração das aplicações.

Um dos princípios de Integração Contínua é que, no final do processo de build, tenhamos um produto pronto pra ir pra produção. Para garantir que a aplicação está nesta situação, precisamos criar diversos tipos de testes automatizados, como os unitários e os de integração.

Testes de integração são aqueles que testam funcionalidades completas, ou seja, testam a integração de vários módulos do seu sistema para garantir que a funcionalidade desejada está completa. Em aplicações web, esse tipo de teste costuma envolver abertura de browsers e simulação da interação do usuário com o sistema.

O problema desses testes fazerem parte do build da aplicação, é que eles tendem a demorar demais à medida que a aplicação cresce, ferindo outro princípio da Integração Contínua: seu [build deve ser o mais rápido possível](http://martinfowler.com/articles/continuousIntegration.html#KeepTheBuildFast). Não adianta nada ter um servidor de Integração Contínua, se seu build demora demais, por exemplo mais de 30 minutos. Imagine que você só descobre que a última mudança que enviou para o controle de versão (commit) quebrou alguns pontos de seu programa somente meia hora depois de iniciar uma nova tarefa. Sua mente, como desenvolvedor, já está muito longe do que executou até meia hora atrás e focada em um problema totalmente distinto.

[Dez minutos](http://jamesshore.com/Agile-Book/ten_minute_build.html) é um bom limitante superior pro tempo do seu build, fazendo com que o feedback seja rápido, assim um build quebrado tende a ser corrigido imediatamente, e a aplicação a estar sempre no estado pronto pra deploy o tempo todo.

Mas como fazer um build de menos de dez minutos, quando só os meus testes de integração demoram mais de meia hora? Uma alternativa é tirá-los do processo de build, e rodá-lo só no fim do dia, ou a cada hora. O problema é que perdemos o feedback rápido dos testes de integração, caindo em uma situação ainda pior que a citada acima, com o feedback de 30 minutos.

Outra alternativa, bem mais interessante, é rodar seus testes de integração em paralelo. Aqui na Caelum usamos algumas ferramentas para conseguir rodar os testes dessa maneira. A primeira delas é o [Parallel Junit](https://parallel-junit.dev.java.net/), capaz de rodar em paralelo um conjunto qualquer de testes compatíveis com JUnit. Para aqueles que usam o TestNG, existe um suporte natural à essa funcionalidade, que permite somente passar parâmetros na sua tag do ant ou maven.

De qualquer maneira, não adianta rodar somente uma instância do Selenium e tentar rodar os testes em paralelo. Então usamos o [Selenium Grid](http://selenium-grid.seleniumhq.org/), que permite subir mais de uma instância do Selenium Server na mesma máquina, ou melhor ainda, em várias máquinas, deixando isso transparente para os testes que usam o Selenium. De uma maneira ainda mais emocionate, podemos levantar diversos browsers distintos em máquinas com sistemas operacionais diferentes, possibilitando rodar os testes em paralelo em ambientes como Windows e Linux.

No Selenium Grid temos duas partes importantes: O **Selenium Hub**, que vai responder aos comandos do selenium emitidos pela sua aplicação, e delegá-los para algum dos **Remote Controls**, que executarão os mesmos no selenium de verdade. Em outras palavras, o Hub funciona como um proxy para cada comando que o teste deseja executar no browser, delegando essa requisição para algum Remote Control ocioso.

Um grande problema do Selenium Grid é a sua usabilidade. Para configurá-lo, precisamos subir o Selenium Hub em uma máquina, depois ir em cada máquina que rodará os Remote Controls, e subir um por um na mão, usando uma task pronta do Ant. Chato? Mas ainda não é o maior problema...

Se a máquina que tem o Hub cair, precisamos ir em todas as máquinas que tem os Remotes, e registrar tudo novamente. Se uma máquina que tem os Remotes cair sem executar um shutdown limpo, não desregistrando seus remotes, precisamos reiniciar tudo de novo, pois se um teste tentar usar o Remote "fantasma", vai dar erro. Por fim, se um teste abrir um Selenium, e esquecer de fechar, o Remote Control associado a esse Selenium ficará travado para sempre, e então você terá que reiniciar todos os programas novamente.

Por causa disso, eu e o Guilherme Silveira resolvemos hackear o Selenium Grid e resolver esses problemas. Criamos então um [fork do projeto original no github](http://lucascs.github.com/selenium-grid), e um novo projeto, o [Selenium Box Agent](http://lucascs.github.com/selenium-box-agent).

A idéia desse novo projeto é a seguinte: Em cada máquina onde serão rodados os Remote Controls, deixamos uma instância do Box Agent rodando. Na máquina onde rodará o Hub, rodamos o projeto do Grid modificado. E então é só ir na página de console do Hub (geralmente em http://localhost:4444/console, substituindo localhost pelo ip do seu servidor) e registrar os Box Agents, pedindo para iniciar os Remotes na mesma página, sem necessidade de linha de comando, tasks do ant, nem qualquer outro trabalho manual e repetitivo. Apenas formulários e links na página do console.

\[caption id="attachment\_568" align="alignnone" width="300" caption="Página Principal do Hub modificado"\]![Página Principal do Hub modificado](https://blog.caelum.com.br/wp-content/uploads/2009/02/hub1-300x187.png)\[/caption\] \[caption id="attachment\_569" align="alignnone" width="300" caption="Box adicionado"\]![box adicionado](https://blog.caelum.com.br/wp-content/uploads/2009/02/hub2-300x187.png)\[/caption\] \[caption id="attachment\_570" align="alignnone" width="300" caption="Remote Control Adicionado"\]![Remote Control Adicionado](https://blog.caelum.com.br/wp-content/uploads/2009/02/hub3-300x187.png)\[/caption\]

Se a máquina do Hub cair, quando ela voltar levantará automaticamente os Remotes de todos os Boxes registrados. Se uma máquina de Box cair, o Hub desregistrará automáticamente todos os seus Remote Controls, e quando ela voltar o Hub registra todos eles automaticamente também. Se um teste esquecer de devolver um Selenium, você pode desregistrar o RemoteControl associado, e pedir pra registrá-lo novamente, direto da página de console.

Um dos nossos builds, que demorava cerca de 50 minutos quando os testes eram rodados serialmente, demora por volta de 8 minutos quando rodado em paralelo, usando 7 threads simultâneas de testes. Se a aplicação crescer mais, é só registrar mais Remote Controls e aumentar o número de Testes simultâneos. E pronto, conseguimos o build de dez minutos, sem sacrificar os testes de integração.

O resultado? Uma equipe mais propensa a corrigir seus próprios erros a medida que eles são cometidos, sem ter que esperar o feedback de um cliente, possivelmente furioso, sobre algo que foi quebrado na última entrega.

Aqui na Caelum tentamos sempre encontrar alguns projetos com os quais podemos contribuir, seja com comentários, documentação ou código e ter sido capazes de criar uma extensão para o Selenium Hub é mais uma maneira que encontramos para compartilhar algo criado por nós com aqueles que procuram e precisam de uma solução similar.
