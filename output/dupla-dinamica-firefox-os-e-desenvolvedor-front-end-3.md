---
title: "Dupla dinâmica: Firefox OS e desenvolvedor front-end"
date: "2014-04-10"
author: "flavio.almeida"
authorEmail: "flavio.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em algum momento em sua carreira de **desenvolvedor front-end** você já deve ter pensando em criar aplicações nativas para **plataformas mobile**. Talvez pelo retorno financeiro que elas possam oferecer (caso seu aplicativo seja um sucesso), ou quem sabe apenas para entender melhor este universo. Independente de qual seja sua motivação, tempo e investimento são necessários e **muitas vezes o tempo é o vilão**.

O problema é que **muitas dessas plataformas não falam a sua língua**: HTML, CSS e JavaScript. Uma opção seria começar pelo Windows 8 Phone no qual podemos programar utilizando essas três tecnologias, mas **não seríamos capazes de aproveitar o mesmo workflow e ferramentas** que já dominamos para criar aplicações web. Teríamos que aprender um novo ambiente.

No universo mobile há um novo player que também fala a mesma língua dos desenvolvedores front-end, inclusive se adequando com um mínimo de esforço ao workflow já existente: o **FirefoxOS**.

### Firefox OS

O [**Firefox OS**](http://www.mozilla.org/pt-BR/firefox/os/) (ou [Boot to Gecko - B2G](https://github.com/mozilla-b2g/B2G)) é um sistema operacional para smartphones e tablets desenvolvido pela [Mozilla](http://www.mozilla.org). Ele possui 3 grandes camadas em sua arquitetura:

[Gonk](https://developer.mozilla.org/en-US/Firefox_OS/Platform/Gonk)

Kernel do Linux e camada de abstração de hardware. Por usar a versão open source do kernel Android ([AOSP](https://source.android.com/)), ferramentas como [ADB](http://developer.android.com/tools/help/adb.html) funcionam.

[Gecko](https://developer.mozilla.org/pt-BR/docs/Mozilla/Gecko)

Mesma engine utilizada pelo navegador Firefox e que implementa os padrões HTML, CSS e JavaScript.

[Gaia](https://developer.mozilla.org/pt-BR/docs/Mozilla/Firefox_OS/Platform/Gaia)

Gaia é a parte responsável pela interface gráfica do Firefox OS seguindo os padrões estabelecidos pelo Gecko. É tudo aquilo exibido na tela após o Firefox OS ter iniciado.

### Expertise front-end diretamente no smartphone

**Uma aplicativo em Firefox OS é agnóstico de estilo**. Ele não herda o _look and feel_ do sistema operacional. Isso é um **prato cheio para o desenvolvedor front-end**, já que estilizar páginas é uma de suas tarefas mais corriqueiras.

Outro ponto importante é que praticamente todas as boas práticas que ele já domina sobre **responsive design**, **mobile first**, **otimizações da web**, inclusive **posicionamento e efeitos com CSS** são **totalmente reaproveitadas no aplicativo** para esta plataforma.

**O JavaScript funciona da mesma forma, porém com API específicas de controle de hadware** como câmera, gps, storage até API's que interagem com recursos do sistema operacional como as API's Contatos e Discagem. **Suas bibliotecas favoritas e consagradas pela comunidade no desenvolvimento web também podem ser utilizadas**.

Por fim, e não menos importante, **você pode encaixar o desenvolvimento de aplicativos para Firefox OS dentro do seu workflow front-end** com nenhum ou pouquíssimos ajustes.

Independente se temos ou não o smartphone, já podemos criar aplicações através do simulador do Firefox OS e realmente **verificar se nossas skills de desenvolvedor front-end são reaproveitadas**.

### Firefox OS Simulador: instale e comece a desenvolver já!

Você já pode iniciar seu desenvolvimento para Firefox OS sem sequer ter um aparelho. O único pré-requisito é que você tenha instalado de preferência a versão mais atualizada do **Firefox**. É através dele que instalaremos [Firefox OS Simulator](https://ftp.mozilla.org/pub/mozilla.org/labs/fxos-simulator/). Escolha a versão mais atual e não se preocupe. **O simulador é bem rápido** mesmo se a sua máquina não for uma das melhores.

### O application Manager

Com o simulador instalado abra o Firefox e digite na barra de endereços:

**about:app-manager**

O navegador exibirá o painel do **application manager** que permite iniciar o simulador e também adicionar e remover aplicações:

[![fo-painel](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-painel-300x243.png)](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-painel.png)

Clicando no botão **iniciar Simulador** o application manager exibirá um botão para cada versão do Firefox OS que você tenha instalado em seu simulador. Basta clicar no botão da versão desejada que em menos de 10 segundos o simulador será carregado e exibido numa janela independente:

[![fo-simulador](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-simulador.png)](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-simulador.png)

Baixe [este aplicativo exemplo](https://github.com/flaviohenriquealmeida/exemplo-firefoxos/archive/master.zip) descompactando-o em sua máquina. Visualize sua estrutura:

[![Estrutura do Projeto](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-estrutura.png)](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-estrutura.png)

O projeto foi criado usando Mac OS, [Sublime Text Editor](http://www.sublimetext.com) e as imagens foram otimizadas pelo [**Grunt**](http://gruntjs.com/) usando a task [Imagemin](https://github.com/gruntjs/grunt-contrib-imagemin). Um servidor de [livereload](https://github.com/gruntjs/grunt-contrib-watch) também foi utilizado para que as alterações do documento fossem vistas instantaneamente no navegador. Já falamos sobre o Grunt [aqui](https://blog.caelum.com.br/automacao-de-build-de-front-end-com-grunt-js/) no Blog da Caelum. O [**Boostrap**](http://getbootstrap.com/) também foi utilizado para dar um visual atrativo e **mobile first!**. Por fim, nosso JavaScript que captura o botão da nossa página adicionando o evento "click":

\[code language="javascript"\] (function() { var botao = document.querySelector('button'); botao.onclick = function() { alert('Era o que você esperava?'); }; })(); \[/code\]

**Estrutura e bibliotecas familiares não**? Agora experimente abrir a página em seu navegador e clique no botão:

 [![Projeto visualizado no navegador](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-pagina-300x214.png)](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-pagina.png) 

### Enviando o aplicativo do Application Manager para o Simulador

Agora vamos adicionar o aplicativo no simulador. Volte para o Firefox. No canto esquerdo há um ícone chamado **Aplicativos**. Verifique se ele está selecionado. Em seguida, no canto inferior da tela, clique em **Adicionar aplicativo empacotado** e aponte para a **pasta do projeto**. Isso fará com que seu aplicativo apareça no manager apenas.

 [![Aplicaçã no Application Manager](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-app-no-manager-300x72.png)](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-app-no-manager.png) 

Agora, basta clicar no botão **Atualizar** para enviar seu aplicativo para o simulador. Isso fará com que um novo ícone apareça na janela do simulador com o nome **Ola**. Abra o aplicativo clicando no ícone:

[![Aplicativo rodando no simulador](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-rodando.png)](https://blog.caelum.com.br/wp-content/uploads/2050/04/fo-rodando.png)

Experimente clicar no botão para ver como o alerta é exibido no simulador.

### Entendo um pouco mais sobre o projeto: o arquivo manifest.webapp

Nosso aplicativo possui visual e comportamento idênticos ao que vimos no navegador, sem falar na estrutura do projeto que é semelhante ao de muitos projetos web. Então basta adicionar a pasta do nosso aplicativo e pronto? Não, seu aplicativo precisa atender à requisitos mínimos, principalmente os do arquivo **manifest.webapp**

O primeiro requisito para seu aplicativo subir no simulador desta maneira é ter o arquivo **manifest.webapp** na raiz do seu projeto. Este arquivo nada mais é do que um JSON:

\[code language="javascript"\] { "version": "1.0", "name": "Ola", "launch\_path": "/index.html", "icons": { "128": "/img/icon-128.png" } } \[/code\] Suas propriedades são:

**version**

A versão do aplicativo que ao ser trocada dispara sua atualização

**name**

O nome do aplicativo que será exibido com o ícone

**icons**

Define o ícone do aplicativo. No exemplo usamos um png de 128x128.

Existem outras propriedades específicas para determinados tipos de aplicações, mas essas são as mínimas que você precisa utilizar para concluir seu arquivo manifest.webapp.

### Conclusão

Vimos como seu background em desenvolvimento front-end é reaproveitado na criação de aplicativos para Firefox OS, inclusive testamos uma aplicação que utilizou o Bootstrap, uma biblioteca CSS famosa na comunidade.

Não utilizamos nenhum API específica do aparelho, algo que poderá ser abordado num post exclusivo para este assunto. Caso você não queira esperar, na minha palestra na **[MobileConf edição Rio](http://www.mobileconf.com.br) deste ano eu abordarei API nativas, permissionamento e quais bibliotecas front-end podem ajudá-lo no desenvolvimento para Firefox OS**

A Caelum também não fica de fora e pode ajudá-lo a ampliar sua expertise front-end com sua [formação front-end](http://www.caelum.com.br/cursos-web-front-end/).
