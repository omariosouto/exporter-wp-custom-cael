---
title: "Integração contínua de projeto Java com Jenkins"
date: "2012-04-24"
author: "francisco.sokol"
authorEmail: "francisco.sokol@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Há bastante tempo [aplicamos e escrevemos sobre integração contínua](https://blog.caelum.com.br/integracao-continua/), uma das práticas mais importantes do desenvolvimento ágil. Através dela, é possível agilizar tarefas demoradas como a compilação de um projeto e a execução dos seus testes automatizados. **Com um servidor de integração contínua bem configurado, essas tarefas são executadas a cada mudança no repositório de código e, em caso de erros de compilação ou falhas nos testes automatizados, todos os desenvolvedores são alertados rapidamente**. Dessa forma, se o servidor de integração não aponta problemas no projeto, a equipe tem a segurança de que as mudanças no código estão de acordo com a bateria de testes. É também um passo na direção do [deploy contínuo](https://blog.caelum.com.br/o-processo-de-deploy-continuo/).

Nesse post, iremos ensinar como instalar e configurar um projeto simples com testes de unidade no [Jenkins](http://jenkins-ci.org/), uma versão renovada (e forkada) do Hudson, o servidor de integração contínua que utilizamos aqui na Caelum e um dos mais populares.

O Jenkins é distribuído como um war e portanto pode ser deployado em um servidor de aplicação como o Tomcat ou o Jetty. Nesse post, iremos utilizar o Tomcat e vamos supor que ele já esteja instalado no seu computador. Chamaremos a home do Tomcat de <tomcat>. Você também deve ter o ant instalado no seu computador pois o utilizaremos para compilar o código e executar os testes. Baixe a [versão mais recente do Jenkins](http://mirrors.jenkins-ci.org/war/latest/) e coloque no diretório `<tomcat>/webpps/`. Inicie o servidor de aplicação executando o script `<tomcat>/bin/startup.sh`. Depois disso, você poderá acessar a interface web do jenkins pela URL `http://localhost:8080/jenkins/` no seu navegador.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/screen.png)](https://blog.caelum.com.br/wp-content/uploads/2012/04/screen.png)

Para demonstrar a configuração de um projeto novo, iremos utilizar um [projeto simples](https://github.com/csokol/jenkins-exemplo), criado para esse tutorial. Ele possui apenas duas classes bem simples e uma classe de teste com o JUnit. Além disso, o projeto possui um `build.xml` configurado para compilar o  projeto, executar os testes e gerar um jar. Você pode também ir aplicando todos os passos em seu próprio projeto. Se você ainda não desenvolve com integração contínua, vai poder mudar bastante a forma com que sua equipe desenvolve e receber um feedback contínuo do que está acontecendo.

Como o projeto se encontra em um repositório Git, será necessário instalar um plugin do Git no Jenkins (inicialmente, o Jenkins só suporta CVS e SVN). Para isso, vá até o plugin manager do Jenkins (_Manage Jenkins > Manage Plugins_) na aba _Avaiable_, habilite o _Git Plugin_ e clique em _Download now and install after restart_ (pode ser que não exista nenhum plugin disponível na aba _Avaiable_, nesse caso, vá até a aba _Advanced_ e clique em _Check Now_ para atualizar o repositório de plugins). Depois do término da instalação do plugin, você deverá reiniciar o Jenkins.

Com o plugin instalado, podemos criar uma nova task no Jenkins para compilar e executar os testes do nosso projeto. Clique em _New Job_, dê um nome a ele, selecione a opção _Build a free-style software project_ e clique em Ok. Você será redirecionado para a página de configurações do projeto.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/config.png)](https://blog.caelum.com.br/wp-content/uploads/2012/04/config.png)

Na seção Source Code Management, selecione Git e coloque a url pública do repositório (`git://github.com/csokol/jenkins-exemplo.git`). Em _Build Triggers_, selecione `Poll SCM` e no campo `Schedule` coloque "`*/5 * * * *`". Essa configuração determina que o Jenkins irá verificar se houve alterações no repositório desde o último build a cada cinco minutos. Se existir alguma, irá atualizar o seu repositório e executar o job.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/1-cut1.png)](https://blog.caelum.com.br/wp-content/uploads/2012/04/1-cut1.png)

Nesse projeto temos duas targets principais do ant que queremos executar no Jenkins, uma para executar os testes e outra para gerar um jar do projeto, pronto para ser distribuído. Para configurar a execução dessas targets, selecione a opção _Invoke Ant_ na seção _Build_ e no campo _Targets_ coloque `test jar`, essas são as targets que executa os testes e gera o jar, respectivamente.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/2-cut2.png)](https://blog.caelum.com.br/wp-content/uploads/2012/04/2-cut2.png)

Agora, precisamos configurar as ações que o Jenkins irá executar depois de executar o ant. Caso exista alguma falha nos testes ou na criação do jar, o Jenkins deve notificar a equipe, para isso, na seção _Post-build Actions_ selecione _E-mail Notification_ e adicione os emails que devem ser notificados em caso de falha no build. Além disso, gostaríamos também de guardar o jar gereado pelo ant, para isso, selecione _Archive the artifacts_ e coloque `target/*.jar` no campo _Files to archive_. Assim, toda vez que o job for executado, o Jenkins irá disponibilizar o jar para download em sua interface. Clique em _Save_ para salvar as configurações.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/3-cut.png)](https://blog.caelum.com.br/wp-content/uploads/2012/04/3-cut.png)

Pronto! Agora o projeto está configurado e cada alteração no repositório irá disparar o Job no Jenkins, executando os testes de unidade:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/4-cut.png)](https://blog.caelum.com.br/wp-content/uploads/2012/04/4-cut.png)

Como nesse momento o Job nunca foi executado, o primeiro irá iniciar automaticamente e você poderá vizualizá-lo no painel do lado esquerdo em _Build history_. Você também pode inicar o processo manualmente, clicando em _Build Now_. Com o build finalizado, você pode visualizar sua página de status, onde poderá baixar o jar criado e também examinar o resultado dos testes em _Console Output_.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/04/5-cut.png)](https://blog.caelum.com.br/wp-content/uploads/2012/04/5-cut.png)

O Jenkins oferece muitos outros recursos além dos descritos aqui, ele é capaz de executar diversas tarefas, como colocar deployar um war em um servidor em produção, gerar relatórios de cobertura de testes, calcular métricas sobre o código do projeto, entre outras. Você poderá conhecer essas funcionalidades explorando os plugins disponíveis ou mesmo desenvolvendo o seu.
