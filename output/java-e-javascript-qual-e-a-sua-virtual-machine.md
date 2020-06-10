---
title: "Java e JavaScript: qual é a sua Virtual Machine?"
date: "2012-10-02"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Sempre falamos da onipresença da JVM. Ela está em uma quantidade enorme de computadores, em especial servidores, mas tem perdido a batalha no desktop e no mobile. Há uma outra plataforma/linguagem onipresente: o JavaScript, e este sim domina o mobile e o desktop, além de começar a ganhar terreno no servidor, como no Node.js.

Como disse Douglas Crockford há 2 anos no QConSP, o JavaScript foi da linguagem mais odiada para uma das mais amadas em um curtíssimo espaço de tempo.

### Você quer rodar JavaScript na JVM?

Entre 2003 e 2005, todos começaram a se animar com as novas possibilidades de rodar outras linguagens na JVM, fossem elas novas como a [Beanshell](http://www.beanshell.org/) e o [Groovy](http://groovy.codehaus.org/), fossem elas velhas como o JavaScript.

No caso especial do JavaScript, várias implementações surgiram. A [Rhino](https://developer.mozilla.org/en-US/docs/Rhino) foi a que mais ganhou espaço. Ela inclusive foi inserida no Java SE 6 como sendo a implementação padrão da scripting engine, definida pela [JSR 223](http://www.jcp.org/en/jsr/detail?id=223). Atualmente temos a VM [dyn.js](http://dynjs.org/), uma iniciativa brasileira. Há ainda a promessa da [Nashorn](http://www.infoworld.com/d/application-development/oracle-prepping-its-nashorn-javascript-engine-175159), uma iniciativa da Oracle.

### Você quer rodar Java numa VM JavaScript?

A [Doppio é uma JVM](http://int3.github.com/doppio/about.html) escrita puramente em JavaScript/CoffeScript. Ela implementa todos os bytecodes do Java e já consegue compilar classes do Java 1.4 com seu próprio compilador, além de aceitar `.class` de qualquer versão.

Sim! Você pode carregar arquivos .class e executá-los via (quase) qualquer navegador atual, sem necessidade de suporte a applets, sem a necessidade de uma JVM clássica.

Como é um projeto novo, ainda há muito a ser feito, em especial em relação a velocidade. Por enquanto não há um JIT, mas os resultados já são bastante impressionantes rodando na V8.

### Virtual Machine dentro de Virtual Machine

Podemos rodar Java em uma JVM escrita em JavaScript. Podemos rodar JavaScript numa JVM escrita em Java. Então podemos fazer o teste: rodar o Rhino no Doppio ou vice-versa.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/09/Inception-Top-Wallpaper-Sohan-Surag1-300x187.jpeg "Inception-Top-Wallpaper-Sohan-Surag1")](https://blog.caelum.com.br/wp-content/uploads/2012/09/Inception-Top-Wallpaper-Sohan-Surag1.jpeg)

Abra o [console de demonstração da Doppio](http://int3.github.com/doppio/) e verá que a rhino é uma das opções oferecidas. Digite rhino no console. Após algum tempo (o classloading do bootstrap do rhino já demora em uma JVM moderna, imagine na Doppio) experimente alguns comandos JavaScript!

\[code lang="javascript"\] Enter 'help' for full a list of commands. Ctrl-D is EOF. doppio > rhino js> var i = 10; js> print(++i); 11 js> \[/code\]

Essa brincadeira não é nada nova. Rodar Linux de dentro do Windows, de dentro do Linux, etc... já é possível via virtual machines há muito tempo. Até rodar bytecode Java dentro de uma implementação .NET, [uma JVM escrita em .NET](http://www.ikvm.net/), não é mais novidade. O interessante é ver isto acontece com duas plataformas que não imaginávamos. As VMs de JavaScript tem se tornado tão poderosas que a Mozilla está tentando até mesmo interpretar Flash, via o [projeto Shumway](https://github.com/mozilla/shumway).

Daria isso espaço a uma forma interessante de rodar Java no browser? De qualquer maneira, mostra o quão forte está o investimento no JavaScript e em suas VMs.

No nosso [curso de Arquitetura Java](http://www.caelum.com.br/curso/fj-91-arquitetura-design-projetos-java/) entramos a fundo nos detalhes de algumas Java Virtual Machines e você pode encontrar mais [no livro](http://www.arquiteturajava.com.br).
