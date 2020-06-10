---
title: "Testando continuamente suas specs JS"
date: "2017-10-09"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

No post [anterior](https://blog.caelum.com.br/nao-faca-testes-faca-specs/) falamos sobre o Jasmine. Agora vamos colocar a mão na massa e criar nossas especificações. O cenário é relativamente simples e fácil de especificar. Temos uma biblioteca de manipulação de CEP, o famoso Código de Endereçamento Postal, a partir daí iremos criar as especificações.

A função de nossa biblioteca é validar se o CEP digitado é um CEP válido. É um cenário bem simples e servirá bem para nossa apresentação.

Nosso primeiro passo é descrever melhor a especificação. Intuitivamente sabemos como é um CEP válido. É um número que se parece com este: 72.115-510. Bom, vamos detalhar melhor (em linguagem natural mesmo) a biblioteca e as funções que ela deve ter:

#### cepUtil é uma biblioteca destinada ao tratamento de CEP

- validarCep é uma função que deve validar se o valor de entrada é um CEP válido, segue a especificação:
    - Deve validar quando o CEP for composto por dois números, seguido de um ponto, três números, um hífen e mais três números. Ex.: 70.673-410;
    - Deve invalidar se a máscara não for informada. Ex.: 70673410;
    - Deve invalidar se houver caracteres alfabéticos. Ex.: 70.673-41X;
    - Deve invalidar se a quantidade de números estiver incorreta. Ex.: 70.673-4100;

Mas espere um pouco! Ainda não "montamos" nosso ambiente para poder executar os testes, vamos aos passos.

## Rodando os testes com jasmine

Bom, pra começo de conversa você deve ter o [node](https://nodejs.org/en/) e o [npm](https://www.npmjs.com/) devidamente instalados em seu [sistema operacional](https://blog.caelum.com.br/java-e-javascript-qual-e-a-sua-virtual-machine/).

O próximo passo é criar a pasta de nosso projeto: **testes-javascript**. Acessando essa pasta a partir da linha de comandos, vamos inicializar nosso projeto:

- npm init -y

Legal, com isso, perceba que na pasta foi criado o arquivo "package.json", se você é do mundo Java, esse é o equivalente ao "pom.xml".

Agora vamos adicionar algumas dependências ao nosso projeto para permitir que possamos rodar os testes, a primeira é o "karma".

No mundo Java basicamente você tem o JUnit que resolve todos quase todos os seus problemas relacionados a testes, contudo, no mundo JavaScript, as tecnologias são um pouco mais pulverizadas. Primeiro instalaremos o "karma" que é o nosso "test runner".

O objetivo do Karma é fornecer um ambiente para que nossos testes possam ser executados, por exemplo, iniciar um navegador, servir os arquivos de testes, preparar os relatórios de execução, etc. Isso irá ficar um pouco mais claro à medida que evoluímos nosso exercício, ok?

Para incluir o karma em seu projeto, faça:

- npm install karma --save-dev

Veja que o arquivo "package.json" foi atualizado com uma nova dependência, como não informamos uma versão, será instalada a última versão disponível da dependência. Esse "--save-dev" diz que a dependência é uma dependência de tempo de desenvolvimento e não precisa ser instalada quando o projeto estiver em "produção".

Pra facilitar a nossa vida, vamos utilizar o "karma-cli" que nos auxiliará na execução do karma via linha de comandos:

- sudo npm install --global karma-cli obs.: sudo será necessário se estiver no Linux ou OSX

Peraí, por que da necessidade desse "sudo" e desse "--global"?

O "--global" diz que a dependência será instalada de maneira global, ou seja, não é uma dependência do projeto e será instalada diretamente no S.O., por isso da necessidade de permissão de administrador (sudo).

karma-cli permite a execução do comando "karma" na pasta de nosso projeto, como se fosse um comando do sistema operacional e, na realidade, quem vai ser acionado é o karma que foi instalado dentro do seu projeto. Confuso né?

Um pouco, mas essa funcionalidade existe em diversas bibliotecas para permitir que possamos ter diferentes versões do karma em diferentes projetos e, quando dermos o comando "karma" na raiz do projeto, a versão correta do karma será executada (gulp e grunt por exemplo tem a mesma funcionalidade).

Ufa, e agora? Agora iniciamos nossos arquivos de configuração:

- karma init

O Karma vai iniciar o modo de configuração "interativo", vamos com as respostas "padrão", só vamos ajustar os diretórios de source e teste, conforme o print abaixo:

Obs.: Você deve ter o Chrome instalado em seu sistema operacional, se não tiver, escolha um outro navegador de sua preferência para rodar os testes.

[![](https://blog.caelum.com.br/wp-content/uploads/2017/08/Screenshot-from-2017-08-24-19-59-30.png)](https://blog.caelum.com.br/wp-content/uploads/2017/08/Screenshot-from-2017-08-24-19-59-30.png)

Veja que foi criado o arquivo karma.conf.js, agora falta instalar a nossa biblioteca se asserções, que no caso será o Jasmine. Lembra que eu disse que no mundo JavaScript as tecnologias são mais bagunçadas pulverizadas?

- npm install jasmine-core --save-dev

Pronto! Já podemos rodar os testes com o comando abaixo:

- karma start     **ou**     ./node\_modules/karma/bin/karma start

Será aberto o Chrome e algumas mensagens de erro serão exibidas no console, claro, ainda nem temos código fonte de nossa biblioteca nem dos testes! Para encerrar os testes, volte ao console e pressione "CTRL+C".

Ótimo, nosso ambiente já está funcionando, agora vamos criar a biblioteca cepUtil. NÃO! Estamos seguindo a filosofia do BDD e TDD, então devemos primeiro criar nossas especificações.

## Jasmine

Nosso próximo passo é transferir as especificações que criamos anteriormente para uma "linguagem" que o computador compreenda. Nesse caso, a linguagem é o JavaScript e utilizaremos a biblioteca Jasmine, [vista no post anterior](https://blog.caelum.com.br/nao-faca-testes-faca-specs/).

[testes-javascript/spec/cepUtilSpec.js](https://github.com/tiagolpadua/testes-javascript/blob/post1/spec/cepUtilSpec.js)

\[code language="javascript"\] describe('cepUtil é uma biblioteca destinada ao tratamento de CEP', function () { describe('validarCep é uma função que deve validar se o valor de entrada é um CEPs válido', function () { it('Deve validar quando o CEP for composto por dois números, seguido de um ponto, três números, um hífem e mais três números', function () { expect(cepUtil.validar('70.673-410')).toBeTruthy(); }); it('Deve invalidar se a máscara não for informada', function () { expect(cepUtil.validar('70673410')).toBeFalsy(); }); it('Deve invalidar se houverem caracteres alfabéticos', function () { expect(cepUtil.validar('70.673-41X')).toBeFalsy(); }); it('Deve invalidar se a quantidade de números estiver incorreta', function () { expect(cepUtil.validar('70.673-4100')).toBeFalsy(); }); }); }); \[/code\]

Você não vai precisar de uma documentação informando quando o CEP deve ser válido e nem mesmo de um analista de domínio. Melhor ainda, o [próximo programador que analisar seu código](https://cdn-images-1.medium.com/max/1600/1*E_sCZhF9iMggfn-U1uGWyg.jpeg), vai saber exatamente como que um CEP deve validado.

## Só agora o código fonte

Como eu disse no post anterior, tendo testes bem escritos, o código fonte é o que menos interessa. Usei a técnica de [IIFE](http://benalman.com/news/2010/11/immediately-invoked-function-expression/) para usarmos as boas práticas (acredite, elas também existem no mundo JavaScript) ;-).

[testes-javascript/www/js/cepUtil.js](https://github.com/tiagolpadua/testes-javascript/blob/post1/www/js/cepUtil.js)

\[code language="javascript"\] var cepUtil = (function () { 'use strict';

//código está zuado? Ok, mas tem testes! Seja um bom garoto e refatore! function validar(cep) { var objER = /^\[0-9\]{2}.\[0-9\]{3}-\[0-9\]{3}$/; if (!cep || !objER.test(cep)) { return false; } return true; }

return { validar: validar }; })(); \[/code\]

O código parece um pouco complicado de entender, fizemos control c + v pattern de algum stackoverflow da vida, e sendo bem sincero, pouco me interessa neste momento a qualidade dele, afinal, tendo testes, alguém algum dia mais capacitado refatora! =)

Agora vamos rodar de novo os testes pra ver se está tudo dando certo?

[![](https://blog.caelum.com.br/wp-content/uploads/2017/08/Screenshot-from-2017-08-24-20-34-30.png)](https://blog.caelum.com.br/wp-content/uploads/2017/08/Screenshot-from-2017-08-24-20-34-30.png)

Regra básica da computação:

- Verde é bom;
- Vermelho é ruim;

No relatório foram executados 4 testes, tudo verde e com sucesso, ótimo!

Recomendo que você brinque um pouco com a função "valida", por exemplo, introduza um "bug" na linha 7, trocando o return false por return true, de modo que qualquer valor passado será considerado um CEP válido (não se esqueça de corrigir depois a função, Ok? Be free!)

O console abaixo mostra como seria no caso de erro.

[![](https://blog.caelum.com.br/wp-content/uploads/2017/08/Screenshot-from-2017-08-24-20-40-51.png)](https://blog.caelum.com.br/wp-content/uploads/2017/08/Screenshot-from-2017-08-24-20-40-51.png)

Várias "asserções" falharam e o output em vermelho é bem maior, dizendo claramente qual foi a especificação não atendida, o valor esperado e o obtido.

## Por que escolhemos o Karma?

No mundo de testes do JavaScript há diversas [outras alternativas além do Karma](https://stackoverflow.com/questions/26032124/karma-vs-testing-framework-jasmine-mocha-qunit), que podem ser utilizadas de acordo com as necessidades do projeto.

Alguns pontos bem positivos que o Karma fornece são:

- Extensibilidade: Existem muitos plugins disponíveis que podem ser utilizados em conjunto com o [Karma](https://karma-runner.github.io/1.0/index.html), por exemplo, plugins de cobertura de testes e geração de relatórios no formato JUnit;
- Possibilidade de escolha do navegador: Neste post optamos por utilizar o Chrome, mas poderíamos ter utilizado o Firefox por exemplo, ou até mesmo o PhantomJS que é um navegador "headless" (ainda veremos sobre ele nos próximos posts);
- Execução contínua dos testes: De acordo com a configuração que fizemos no karma.conf.js podemos deixar a execução de testes sempre ativa, sendo executada cada vez que algum arquivo for alterado;

## Finalizando

Este post foi mais focado no hands on. A ideia é possibilitar que você faça as especificações daquelas funções mais básicas do seu código.

Se você já sabia disso, não se fruste! (ainda!), nos próximos vamos adicionar novas funcionalidades como pesquisa de endereço via Rest usando Jquery, preenchimento dos dados em uma tela HTML e vamos testar tudo isso.

Vamos falar de mocks, testes de integração e cobertura de testes.

Todo o código deste post pode ser encontrado neste [link](https://github.com/tiagolpadua/testes-javascript/tree/post1): Lá você também encontrará testes de outras funções como colocar e retirar máscara.

Não deixe de comentar como está sua experiência testando aplicações JS!
