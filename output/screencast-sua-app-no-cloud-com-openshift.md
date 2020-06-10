---
title: "Screencast: sua App no Cloud com OpenShift"
date: "2011-12-01"
author: "hannelita"
authorEmail: "hannelita@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O cloud vem se espalhando pelo mercado de TI e em especial entre os desenvolvedores de software. Heroku, Cloud Foundry, Microsoft Azure, [Google App Engine](https://blog.caelum.com.br/escrevendo-e-migrando-aplicacoes-para-o-google-app-engine/), entre outros serviços de cloud no modelo PaaS, ([Platform as a Service](https://blog.caelum.com.br/cloud-computing-na-casa-branca-e-o-paas/)), popularizaram-se muito nos últimos tempos.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/11/openshift.png "openshift")](https://blog.caelum.com.br/wp-content/uploads/2011/11/openshift.png)

Para disponibilizar serviços de cloud, é necessário que haja uma infraestrutura de servidores bastante robusta e uma ambientação sofisticada. Aproveitando a experiência no ramo, a Red Hat lançou, em maio de 2011, durante o Red Hat Summit, seu serviço de PaaS: o [OpenShift](https://openshift.redhat.com/app/).

O OpenShift possui duas versões - OpenShift Express, opção simples e rápida para hospedar aplicações Ruby, Python, Perl ou Java do zero; e OpenShift Flex, para ser integrado ao Amazon EC2 em aplicações PHP e Java a fim de propiciar monitoramento avançado e auto-scale.

Havia uma boa disponibilidade de PaaS para linguagens como Ruby e Python, e, em contrapartida, o mercado contava com poucas opções de PaaS para Java. A fim de atacar esse ponto, a equipe do OpenShift lançou suporte para aplicações Java, tanto para aas versões Express quanto para a Flex. Adotando como servidor o JBoss AS 7 (mais informações [neste post](https://blog.caelum.com.br/jboss-as-7-inovacao-nos-servidores-java-ee/)), o OpenShift implementou uma maneira muito simples e rápida para deployar aplicações Java para o cloud, aceitando quase qualquer framework disponível.

**Primeiros passos com o OpenShift**

Para usar o OpenShift Express, inicialmente é preciso [cadastrar uma conta](http://openshift.redhat.com). Feito isso, devemos instalar o conjunto de ferramentas para trabalharmos com o OpenShift. Para Linux ou Mac OS, é necessário que Ruby 1.8 esteja instalado na máquina, e, como root, devemos executar:

\[code\] gem install json\_pure gem install rhc \[/code\]

Fizemos um pequeno screencast para dar uma pequena introdução ao OpenShift Express. Você pode seguir os passos descritos no vídeo abaixo após instalar as gems especificadas acima:

<iframe src="http://player.vimeo.com/video/32917829?title=0&amp;byline=0&amp;portrait=0" width="541" height="338" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

Agora é necessário criar um domínio para hospedar nossas aplicações. Basta digitarmos no terminal:

\[code\]rhc-create-domain -n meu\_dominio -l seu\_login\[/code\]

Os parâmetros -n e -l especificam, respectivamente, o nome do domínio que você deseja e seu login no OpenShift. Será solicitada sua senha; digite-a e pronto, seu domínio foi criado.

É importante frisar que o OpenShift utiliza git para controle de versão, por isso é necessário tê-lo instalado em sua máquina antes de criar aplicações.

Em seguida, vamos criar uma aplicação. Além de fornecermos o nome, é necessário também fornecer o tipo da aplicação que iremos deployar - Ruby, Java, Python, Perl ou PHP. Para tanto, executamos o seguinte comando:

\[code\]rhc-create-app -a minha\_app -t jbossas-7.0 -l seu\_login\[/code\]

Os parâmetros -a e -t representam, respectivamente, o nome de sua aplicação e o tipo (linguagem) escolhido. Novamente, -l representa seu login no OpenShift. O tipo da aplicação, indicado por -t, pode receber os seguintes valores:

rack-1.1 - Aplicações Ruby wsgi-3.2 - Aplicações Python jbossas-7.0 - Aplicações Java perl-5.10 - Aplicações Perl php-5.3 - Aplicações PHP

Experimente acessar http://minha\_app-meu\_dominio.rhcloud.com/. Uma página de index padrão deve aparecer se tudo correu bem.

O OpenShift suporta alguns bancos bastante populares como MySQL e SQLite. Podemos adicionar suporte ao MySQL em nossa aplicação de exemplo digitando o seguinte comando:

\[code\]rhc-ctl-app -a minha\_app -e add-mysql-5.1\[/code\]

O parâmetro -a refere-se ao nome de nossa aplicação, e -e refere-se à ação que desejamos efetuar; no caso, adicionar suporte para MySQL. O OpenShift então irá lhe devolver em seu console alguns dados de sua base, tais como nome, host e senha. Anote e guarde esses dados!

Podemos ainda acessar nosso banco remoto com o auxílio do PHPMyAdmin. Basta digitar o comando:

\[code\]rhc-ctl-app -a minha\_app -e add-phpmyadmin-3.4\[/code\]

Digitamos então o usuário e senha do banco remoto que nos foi fornecido anteriormente e pronto! Temos suporte do PHPMyAdmin.

Todas as etapas até a criação de sua aplicação podem ser realizadas por meio da interface gráfica disponibilizada pelo site do OpenShift. Apenas frisando que criar a aplicação via linha de comando já gera automaticamente um projeto trackeado para a branch remota de sua aplicação no OpenShift. Criar através do assistente gráfico vai requerer que você faça isso manualmente.

Gostou? Atualmente um dos projetos da Caelum, o [Calopsita](http://calopsita.caelum.com.br/), está no OpenShift. Você também pode obter mais informações acessando a [documentação oficial](https://docs.redhat.com/docs/en-US/OpenShift_Express/1.0/html/User_Guide/index.html) e também nos [diversos materiais](https://www.redhat.com/openshift/forums) elaborados pela Red Hat e pela comunidade. Será um forte competidor para as outras plataforma?
