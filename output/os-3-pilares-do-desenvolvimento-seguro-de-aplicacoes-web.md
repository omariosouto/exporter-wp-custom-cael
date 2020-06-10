---
title: "Os 3 pilares do desenvolvimento seguro de aplicações Web"
date: "2019-03-20"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O processo de desenvolvimento seguro de aplicações web não se resume a apenas utilizar o protocolo HTTPS, em conjunto com uma funcionalidade de login. Isso não é o suficiente para garantir que a aplicação esteja livre de vulnerabilidades.

Hoje em dia existem diversas ferramentas prontas, nas mais diversas linguagens de programação, que auxiliam pessoas desenvolvedoras a construírem um software funcional do zero em um curto período de tempo.

Entretanto, um código robusto e que funciona não necessariamente é seguro. A boa notícia é que tais ferramentas se preocupam cada vez mais com segurança e já implementam diversos mecanismos para a proteção contra as principais vulnerabilidades.

Os ataques mais críticos a uma aplicação são os que consistem em ganhar acesso irrestrito a ela e também a obter acesso aos dados sensíveis que ela gerencia.

Existem diversas práticas e técnicas para tornar uma aplicação livre de possíveis vulnerabilidades, sendo que no geral elas seguem a estes três pilares:

- Gerenciamento do controle de acesso;
- Validação das informações recebidas e
- Gerenciamento dos ataques sofridos.

## Gerenciamento de controle de acesso

Esse pilar foca em evitar que alguém consiga ter acesso indevido aos dados e funcionalidades que a aplicação gerencia.

As principais técnicas relacionadas a esse pilar são: **autenticação** e **autorização**.

Autenticação visa garantir que de fato o usuário é quem ele diz ser, sendo que geralmente é implementado com o usuário fornecendo um login/senha e a aplicação validando se tais informações estão corretas. Em aplicações que exigem maior segurança, outros mecanismos podem ser utilizados, tais como certificados digitais e smartcards.

Autorização consiste na aplicação conseguir determinar se um dado usuário tem permissão de acesso a um dado ou funcionalidade que ele está requisitando. Em algumas aplicações esse controle de acesso pode ser complexo, envolvendo vários perfis e restrições, sendo bastante comum encontrar brechas de segurança na implementação desse mecanismo.

## Validação das informações recebidas

Esse pilar foca em validar e tratar as informações fornecidas pelos usuários da aplicação.

Desenvolvedores devem sempre considerar que as informações fornecidas pelos usuários são inseguras, por serem potencialmente maliciosas.

As principais técnicas relacionadas a esse pilar são: **validação semântica** e **sanitização**.

Validação semântica consiste em validar as informações do ponto de vista lógico, pois nem todos os ataques enviarão dados maliciosos. Por exemplo, validar se uma informação que deveria ser numérica realmente é um número ou validar se um campo oculto/desabilitado foi modificado de maneira não permitida.

Sanitização consiste em limpar e/ou filtrar as informações fornecidas pelos usuários, removendo assim possíveis trechos de códigos maliciosos, como códigos SQL e JavaScript. Consiste também em fazer um “escape” das informações antes de exibi-las na aplicação, evitando assim que o browser execute códigos maliciosos.

## **Gerenciamento dos ataques sofridos**

Esse pilar foca em como lidar com possíveis ataques que uma aplicação possa sofrer.

Desenvolvedores devem considerar que toda aplicação pode ser alvo de hackers maliciosos a qualquer momento. Portanto, é parte fundamental da área de segurança ter mecanismos para lidar com ataques sofridos em suas aplicações.

Tais mecanismos incluem **tratamento de erros**, **geração de logs** e **alertas de segurança**.

Erros inesperados sempre podem acontecer em aplicações em produção, sendo importante então ter um mecanismo que faça um tratamento deles.

Evite exibir para os usuários telas padrões de erros, pois elas podem conter informações que serão valiosas para os hackers. Lembre-se também de sempre gerar logs dos erros inesperados, adicionando a eles o máximo de informações possíveis, para que se possa identificar de maneira mais fácil e ágil o problema ocorrido e como corrigi-lo.

Sempre gere logs de auditoria em aplicações, pois eles podem ser fontes vitais de informações quanto à ataques sofridos.

Logs efetivos são aqueles que permitem se descobrir o que de fato aconteceu, quais vulnerabilidades foram exploradas, se o hacker conseguiu ter acesso a dados ou funcionalidades restritas e até mesmo informações sobre o hacker em si.

Utilize logs nos pontos chave da aplicação, em especial nos seguintes eventos:

- Todos os eventos de autenticação (sucesso, falha, solicitação de alteração de senha, etc.);
- Transações relacionadas a movimentações financeiras;
- Tentativas de acesso que foram bloqueadas pela aplicação; e
- Dados maliciosos que foram enviados para a aplicação.

Em casos onde segurança é um fator crítico, é importante criar um mecanismo que alerte os administradores da aplicação em tempo real sobre ataques sofridos.

Mecanismos de alerta devem ser disparados para situações de anomalias, tais como:

- Uso de recursos de maneira anormal (CPU, memoria, requests excessivos, etc.);
- Regras de negócio da aplicação sendo utilizadas de maneira diferente do normal;
- Requisições contendo dados maliciosos; e
- Requisições onde se sabe que dados ocultos ao usuário foram manipulados.

Certamente seguir a esses três pilares fará com que sua equipe desenvolva softwares cada vez mais seguros, com menos vulnerabilidades e riscos para sua organização.

Você se preocupa com segurança e segue esses pilares ao desenvolver suas aplicações? Deixe um comentário nos contando sua experiência sobre o assunto :)
