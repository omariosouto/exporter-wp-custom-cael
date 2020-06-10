---
title: "O panorama do gerenciamento de bibliotecas no Android e iOS"
date: "2013-12-16"
author: "erich.egert"
authorEmail: "erich.egert@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muitas bibliotecas opensource acabam ganhando uma versão oficial, o que facilita nosso trabalho. Tanto em aplicativos Android quanto iOS tornou-se comum a adoção de menus laterais para determinar os principais itens da navegação da aplicação, e muitas bibliotecas surgiram:

[![side_menu](https://blog.caelum.com.br/wp-content/uploads/2013/07/side_menu.jpeg)](https://blog.caelum.com.br/wp-content/uploads/2013/07/side_menu.jpeg)

Na plataforma Android, atenta a essa tendência, o Google criou o [Navigation Drawer](http://developer.android.com/design/patterns/navigation-drawer.html) dando suporte nativo ao Widget do Side Menu. Mas nem todas as bibliotecas, componentes de UI e ferramentas acabam tendo uma alternativa dentro das bibliotecas padrão. Como fazer?

Recentemente esse não foi o único momento em que o Android saiu na frente. Uma reclamação recorrente dos desenvolvedores da plataforma que utilizavam o plugin do Eclipse era o gerenciamento de dependências: uma aplicação geralmente usava várias Project Libraries, fazendo com que uma aplicação Android resultasse em vários projetos abertos no Eclipse, levando o desenvolvedor até a criar Working Sets na IDE por aplicação. Quando novas versões das bibliotecas eram lançadas, o desenvolvedor tinha um trabalho manual considerável. Esse trabalho não é cotidiano dos desenvolvedores Android no exterior que geralmente usam a IDE de desenvolvimento da JetBrains, o IntelliJ Idea, que possui plugin para desenvolvimento Android e possui uma integração muito boa com o Maven, só que é uma IDE paga cujo uso é bem menor no Brasil.

Ainda em versão beta, o Google lançou o Android Studio, nova IDE de desenvolvimento Android que deverá colocar fim ao suporte ao plugin para Eclipse. A nova IDE está sendo desenvolvida pela JetBrains e será gratuita e terá a mesma integração com o Maven que o IntelliJ Idea. Agora para gerenciar dependências basta fazermos uso do conhecido arquivo pom.xml e usarmos a capacidade do Maven de baixar as dependências, buildar o projeto e gerar o APK.

A plataforma da Apple, conhecida pela produtividade, não podia ficar para trás. Apesar de não possuir ainda um mecanismo oficial de gerenciamento de dependências para os Frameworks utilizados nas aplicações, a Apple teve ajuda da comunidade para atingir o objetivo de tornar fácil a utilização de bibliotecas de terceiros. Como todo Mac já vem com Ruby instalado e existe uma ferramenta chamada Bundler escrita na linguagem para fazer o gerenciamento das bibliotecas Ruby, as chamadas "Gems" pareceu natural para a comunidade aproveitar essa infraestrutura para criar um mecanismo de gerenciamento para as aplicações iOS: nascia o [CocaPods](http://cocoapods.org).

Para instalá-lo, basta instalar a Gem (sim, é uma biblioteca Ruby!) cocoapods e depois fazer o setup:

\[code\] gem install cocoapods pod setup \[/code\]

Para cadastrar uma dependência na aplicação basta criar o arquivo Podfile na raiz da aplicação. O formato do arquivo será bem familiar ao Gemfile que é usado para cadastrar as dependências no Ruby. Abaixo um exemplo no qual estou cadastrando a biblioteca JASidePanels, uma das mais utilizadas para fazer o Side Menu no iOS:

\[code\] platform :ios, '6.0' pod 'JASidePanels', '~> 1.2.1' \[/code\]

Uma vez cadastrada, para baixarmos e instalarmos a dependência em nossa aplicação iOS basta executarmos o comando:

\[code\] pod install \[/code\]

Agora é só seguir as instruções do desenvolvedor na página em que hospeda o código no GitHub: [https://github.com/gotosleep/JASidePanels](https://github.com/gotosleep/JASidePanels).

O sucesso foi tanto que o uso do cocoapods tornou-se praticamente obrigatório nas aplicações comerciais para iOS. Para facilitar ainda mais a comunidade ainda criou um site que funciona como um "catálogo" das bibliotecas disponíveis para a plataforma, o [http://www.cocoacontrols.com/controls](http://www.cocoacontrols.com/controls).

Boa parte das bibliotecas mostradas no site é disponibilizada na forma de "Pods", as demais precisam ser baixadas e incluídas manualmente no projeto e nem todas tem licença que permite o livre uso.

A ideia é tão interessante que a comunidade Android adotou a mesma abordagem! Se você é desenvolvedor Android e também quiser um catálogo visual dos componentes disponíveis para a plataforma, confira o site [www.androidviews.net](http://www.androidviews.net).

Pra quem já escreveu aplicação Java ME eu posso dizer: Nunca foi tão fácil desenvolver para mobile! E você, tem truques para gerenciar todos os artefatos necessários para seus projetos?
