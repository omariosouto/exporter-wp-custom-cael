---
title: "CocoaPods - O gerenciador de dependências  no iOS"
date: "2014-05-19"
author: "fabio.pimentel"
authorEmail: "fabio.pimentel@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Usar bibliotecas de terceiros certamente é uma das facilidades no desenvolvimento de software. Gerenciar isso em um projeto nem sempre é uma tarefa trivial já que temos versões diferentes de uma mesma biblioteca, outras vezes dependências transitivas que se conflitam. Para resolver esses problemas e facilitar a comunicação do time de desenvolvedores as ferramentas de gerenciamento de dependências existem. São exemplos dessas ferramentas: Ivy nos projetos Java e Bundle nos de Ruby.

[![cocoapods-banner](https://blog.caelum.com.br/wp-content/uploads/2040/04/cocoapods-banner.png)](https://github.com/CocoaPods/CocoaPods)

No iOS uma ferramenta que vem se tornando padrão nos projetos, motivada também pela dificuldade de importar bibliotecas e disponibilizá-las no Xcode, é o [CocoaPods](http://cocoapods.org/) embora ela também sirva para aplicações para OSX.

## Instalação do CocoaPods

A sua distribuição se dá em forma de [Gem](https://rubygems.org/) (bibliotecas do mundo Ruby). Para instalar basta o famoso comando de instalação:

\[code language="bash"\] gem install cocoapods \[/code\]

Normalmente um arquivo com a definição das dependências é usado no diretório do projeto e tem o nome de **Podfile**. Nele a primeira definição é sobre para qual plataforma o projeto está sendo desenvolvido.

\[code language="ruby"\] platform :ios, '7.0' \[/code\]

A partir disso é declarar as bibliotecas a serem usadas e definir a versão com a declaração **pod** que o projeto usa:

\[code language="ruby"\] platform :ios, '7.0'

pod 'AFNetworking', '~> 2.2.0' \[/code\]

Os operadores que podem ser especificados junto com uma dependência são: **\>, >=, <, <=** e **~>.** No Podfile anterior, o operador **~>** diz que qualquer versão que mantenha o MAJOR e o MINOR pode ser usado, ou seja, 2.2.0 e 2.2.1 (já que só muda o PATCH) seriam aceitos mas 2.3.0 ou qualquer acima dela não. Para mais detalhes sobre a semântica do versionamento acesse [http://semver.org/](http://semver.org).

Depois da definição das dependências um comando é usado para a download e instalação:

\[code\] pod install \[/code\]

E sua saída é:

![saida_pod_install.png](https://lh3.googleusercontent.com/iATC2xVs28HEQKWb5NpA2ymgJNH5CjesQU31oay8JO6q0EPVq566Euowb_-IH9xI8GGI0-cnim5Ozuemk4A7zWtsew1jA7H3hMZIoo8Dgj5Nqu07M6_9siZ36RnAwUN0aw)

Note que agora devemos usar o ._xcworkpace_ como projeto no Xcode e não mais o ._xcodeproj_. Sendo assim ao abrirmos o projeto na IDE, nosso _Project Navigator_ estará dessa forma:

[![Project Navigator](https://blog.caelum.com.br/wp-content/uploads/2040/04/pod-navigator-173x300.png)](https://blog.caelum.com.br/wp-content/uploads/2040/04/pod-navigator.png)

Além disso um arquivo chamado **Podfile.lock** foi gerado no diretório da aplicação. De acordo com ele a versão de fato instalada do AFNetworking foi a 2.2.1. Observe-o:

[![Arquivo Podfile.lock](https://blog.caelum.com.br/wp-content/uploads/2040/04/pod-podfile-lock-300x284.png)](https://blog.caelum.com.br/wp-content/uploads/2040/04/pod-podfile-lock.png)

Existindo um _Podfile.lock_ no projeto a execução do comando _pod install_ irá baixar e instalar apenas as versões das bibliotecas especificadas no .lock . Portanto é fundamental que esse arquivo seja adicionado ao repositório do projeto para que todos os desenvolvedores tenham o mesmo _.lock_ e por consequência usem sempre as mesmas versões. Caso uma alteração seja necessário no Podfile basta usarmos o comando _pod update_ que atualizará o Podfile.lock .

Por ser tão prático e simples de usar o CocoaPods tem sido figurinha carimbada nos projetos iOS.

## Criando nossos próprios Pods

Além da facilidade de uso, a praticidade de disponibilizar bibliotecas através desse gerenciador é mais um dos motivos de seu sucesso. Para criar o seu próprio Pod, um arquivo contendo a especificação da biblioteca é necessário. O **Pod Spec** pode ser gerado usando o comando _pod spec create_ que não faz nada além de gerar um _.podspec_ auxiliar. As configurações mínimas são as seguintes:

\[code language="ruby"\] Pod::Spec.new do |s| s.name         = "FPLibrary" s.version      = "1.0.0" s.summary      = "Just another library" s.homepage     = "http://www.fplibrary.com.br" s.license      = 'Apache License, Version 2.0' s.author       = { "Fábio Pimentel" => "fabio.pimentel@caelum.com.br" } s.platform     = :ios, '7.0' s.source       = { :git => "https://github.com/fabiopimentel/fplibrary.git", :tag => "1.0.0" } s.source\_files  = "Classes/\*.{h,m}" end \[/code\]

Esse arquivo deve estar disponível em um diretório a ser adicionado no repositório das especificações do CocoaPods. O repositório é chamado de **Spec Repo** e acessado via [github.com/CocoaPods/Specs](https://github.com/CocoaPods/Specs). Dessa forma teremos  uma estrutura similar a essa do Spec do AFNetworking de versão 2.2.1:

[![Screen Shot 2014-04-16 at 4.35.17 AM.png](https://lh3.googleusercontent.com/f_xagtpK1N6E1URg-4bshfUj4yIJstogqVUbvj7D5We1BHwRyorHLipjof6uODHeFSEsWt7-DH7nmQIjNOlqsG_juttaOjcUo3g_5YHUa04eFX5vKS04TGurvDuMqg89fQ)](https://github.com/CocoaPods/Specs/blob/master/AFNetworking/2.2.1/AFNetworking.podspec)

Portanto para cada  nova versão existe a necessidade de mais um subdiretório com o número e dentro dele um específico _.podspec_. Como a seguinte estrutura:

├── Specs(Spec Repo)
    └── \[SPEC\_NAME\]
        └── \[VERSION\]
            └── \[SPEC\_NAME\].podspec

Para submeter nossas alterações um _pull request_ deve ser feito, mas não antes de validar a spec  com um  _pod spec lint_ na estrutura do Spec Repo clonada.

## Adicionando um repositório privado

É bastante comum a prática de reaproveitar componentes e as vezes até mesmo bibliotecas internas em nossos projetos. Para este cenário temos a opção de criar um repositório privado. Esse repositório deve seguir a mesma convenção de diretórios do repositório principal que já vimos acima. E pode ser adicionado como fonte de Specs fazendo:

pod repo add nome-do-repositorio url-do-repositorio

Com toda essa simplicidade fica difícil não fazer tanto sucesso não é mesmo? Participe da [Mobile Conf 2014](http://www.mobileconf.com.br) e venha ver esse e mais assuntos ligados ao dia-a-dia do desenvolvedor iOS na minha palestra. Se você já usa o Cocoa Pods, ou considera usá-lo nos próximos projetos, comente e compartilhe conosco suas experiências.
