---
title: "Integração Contínua e o processo Agile"
date: "2008-11-04"
author: "caueguerra"
authorEmail: "caueguerra@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

> "Integração Contínua é uma pratica de desenvolvimento de software onde os membros de um time integram seu trabalho frequentemente, geralmente cada pessoa integra pelo menos diariamente - podendo haver multiplas integrações por dia. Cada integração é verificada por um build automatizado (incluindo testes) para detectar erros de integração o mais rápido possível. Muitos times acham que essa abordagem leva a uma significante redução nos problemas de integração e permite que um time desenvolva software coeso mais rapidamente." [Martin Fowler](http://martinfowler.com/articles/continuousIntegration.html "Martin Fowler")

Integração Contínua tornou-se muito importante na comunidade de desenvolvimento de software e isso provavelmente ocorreu devido ao grande impacto causado pelas metodologias ágeis. Em equipes que adotaram tais metodologias (eXtreme Programming, Scrum, entre outras), integração contínua é um dos pilares da agilidade, garantindo que todo o sistema funcione a cada build de forma coesa, mesmo que sua equipe seja grande e diversas partes do código estejam sendo alteradas ao mesmo tempo.

Mas porque fazer Integração Contínua ? Quais os benefícios que isso pode trazer ?

Basicamente, a grande vantagem da integração contínua está no **feedback instantâneo**. Isso funciona da seguinte forma: a cada commit no repositório, o build é feito automáticamente, com todos os testes sendo executados de forma automática e falhas sendo detectadas. Se algum commit não compilar ou quebrar qualquer um dos testes, a equipe toma conhecimento instantâneamente (através de email, por exemplo, indicando as falhas e o commit causador das mesmas). A equipe pode então corrigir o problema o mais rápido possível, o que é fundamental para não introduzir erros ao criar novas funcionalidades, refatorar, etc. Integração contínua é mais uma forma de trazer **segurança em relação a mudanças**: você pode fazer modificações sem medo, pois será avisado caso algo saia do esperado.

Mas porque eu não rodo pessoalmente os testes na minha máquina e só então faço o commit? Simples: seu projeto pode ser tão grande, que os testes (em especial os de aceite) demoram um tempo considerável para serem executados e você não vai querer esperar todo esse tempo a cada commit pra poder continuar a trabalhar. Nesse caso, o recomendado é rodar os testes que envolvem as partes que você modificou e só então commitar, deixando para o **servidor de integração contínua** o trabalho de realizar todos os testes do sistema e garantir que tudo esteja funcionando. Além disso, não estamos falando apenas de testes, estamos falando de builds completos: a cada commit temos uma versão que teoricamente está pronta para entrar em produção, e isso pode envolver a realização de tarefas que não faríamos se estivessemos só testando, como por exemplo gerar um arquivo `.war`. O projeto pode ainda ser implantado automaticamente num servidor de desenvolvimento/homologação, e então com isso a cada commit temos o projeto rodando na web instantaneamente refletindo nossas mudanças!

Um outro exemplo interessante é o processo de geração das apostilas dos nossos cursos: a cada commit feito no repositório, o servidor faz o check-out, executa o [**Tubaina**](https://github.com/caelum/tubaina "Tubaína"), que transforma o fonte em `latex`, convertendo o `latex` gerado em pdf e por fim copiando esse pdf num diretório interno, pronto para impressão na gráfica. Dessa maneira, garantimos que disponibilizamos para nossos alunos o material mais atualizado disponível. Para tudo isso funcionar, no entanto, é preciso agarrar a idéia de commits pequenos. Fica mais fácil saber onde foi introduzido um erro quando o build quebrar se houveram pequenas mudanças, do que ter de verificar as ultimas 50 classes alteradas no ultimo commit. Outro ponto importante, é garantir ao menos um build limpo, com todos os testes passando, ao final de cada dia. Assim, teremos software pronto para entrar em produção tão cedo seja necessário.

Em outras palavras, podemos descrever Integração Contínua como integração **automática** com processo de build **automático** e que roda testes de forma **automática** e **automaticamente** detecta falhas em cada pedaço.

Abaixo as ferramentas que usamos aqui na Caelum:

- [**CruiseControl.rb**](http://cruisecontrolrb.thoughtworks.com/): desenvolvida pela [ThoughtWorks](http://www.thoughtworks.com/), é a aplicação de integração contínua. Capaz de constantemente verificar os repositórios em busca de novos commits, fazendo check-out e rodando tarefas pré-determinadas. O interessante dessa ferramenta é que ela trabalha com qualquer tipo de projeto: ruby, java, ou qualquer outro cujo build possa ser feito através da linha de comando. Além disso, tem sua interface extremamente simples e funcional :).

\[caption id="attachment\_384" align="aligncenter" width="300" caption="CruiseControl.rb"\][![CruiseControl.rb](https://blog.caelum.com.br/wp-content/uploads/2008/10/cruisecontrol-300x229.png "CruiseControl.rb")](https://blog.caelum.com.br/wp-content/uploads/2008/10/cruisecontrol.png) \[/caption\]

\[caption id="attachment\_385" align="aligncenter" width="300" caption="CruiseControl.rb build report"\][![CruiseControl.rb build report](https://blog.caelum.com.br/wp-content/uploads/2008/10/cruise-stella-300x196.png "CruiseControl.rb build report")](https://blog.caelum.com.br/wp-content/uploads/2008/10/cruise-stella.png)\[/caption\]

- **[CCMenu](http://ccmenu.sourceforge.net/ "CCMenu")**, ou **[CCTray](http://ccnet.sourceforge.net/CCNET/CCTray.html)**: permite acompanhar o build de cada projeto sem ter de entrar no site do CruiseControl.rb. Isso é feito através de um ícone alertando quais projetos estão com o build quebrado.
- [**Selenium**](http://seleniumhq.org/): também desenvolvida pela ThoughWorks, provê maneira de realizar testes de integração em projetos Web, simulando a navegação do usuário pelas páginas do site, realizando diversas ações como clicar o mouse, prencher campos, etc... Usado para validar se o resultado de determinadas ações do usuario correspondem às que esperamos.
- [**Xvfb**](http://www.xfree86.org/4.0.1/Xvfb.1.html): Servidor X11 que permite realizar todas as operações gráficas em memória, não exibindo nada na tela. Com essa ferramenta, é possível rodas os testes com Selenium sem ter as janelas do Firefox pulando na tela do nosso servidor a cada teste. Além disso, podemos rodar os testes mesmo em ambientes sem X instalado (como é o caso de servidores, por ex).

Ainda usamos plugins Maven e Ant para a geração de relatórios de cobertura e testes, que publicamos a cada novo build. Assim, conseguimos além de garantir que o código está todo integrado, garantir que está com uma cobertura adequada; afinal, de que adianta ter o sistema sem testes falhando, se ele possui menos testes que o necessário?

\[caption id="attachment\_402" align="aligncenter" width="300" caption="JUnit Report"\][![JUnit Report](https://blog.caelum.com.br/wp-content/uploads/2008/10/junit-300x52.png "JUnit Report")](https://blog.caelum.com.br/wp-content/uploads/2008/10/junit.png)\[/caption\]

\[caption id="attachment\_401" align="aligncenter" width="300" caption="Cobertura Report"\][![Cobertura Report](https://blog.caelum.com.br/wp-content/uploads/2008/10/cobertura-300x272.png "Cobertura Report")](https://blog.caelum.com.br/wp-content/uploads/2008/10/cobertura.png)\[/caption\]

Por fim, pela experiência que temos no uso dessas ferramentas no desenvolvimento de aplicações internas, open source e em nossos clientes, podemos afirmar que elas de fato nos trazem uma produtividade antes inalcançável. Se você pensa em começar a utilizar alguma ferramentas de integracão contínua, vale a pena dar uma olhada nessas que indicamos. No entanto, existem várias outras disponíveis, e é questão de escolher a que melhor se adapta às suas necessidades. Não deixe de comparilhar suas experiências...
