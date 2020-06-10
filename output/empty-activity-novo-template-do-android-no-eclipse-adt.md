---
title: "Empty Activity, novo template do Android no Eclipse ADT"
date: "2014-07-25"
author: "suelengc"
authorEmail: "suelen.carvalho@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A primeira coisa que normalmente fazemos ao iniciar o desenvolvimento em uma nova tecnologia é tentar criar um projeto do tipo _Hello World_ para gerar o código mais simples e começar a partir dele.

No Android, esta tarefa era bem simples de se fazer. Basicamente, após baixar e abrir o Eclipse [ADT](http://developer.android.com/tools/sdk/eclipse-adt.html) (Android Development Tools), bastava criar um novo _**Android Application Project**_ usando o template _Blank Activity_ e seguir as instruções do _wizard_, ou seja, _next, next_ e _finish._

Porém, na versão 2.6.0 do Eclipse ADT, o Google alterou o template _Blank Activity e_ ao invés de gerar um código simples, passou a gerar um código mais complexo, já fazendo uso de API's mais avançadas e difíceis de entender. Isso fez com que alguns desenvolvedores Android ficassem um pouco confusos.

Esta alteração sem aviso prévio gerou [muita insatisfação por parte dos desenvolvedores](https://code.google.com/p/android/issues/detail?id=67513) pois não havia mais como criar o _Hello World_ básico.

Ao perceber esta insatisfação, o Google lançou a versão 2.6.3 do ADT que traz um template novo chamado _Empty Activity_ que visa atuar como era o antigo _Blank Activity_, gerando um código simples sem o uso de API's avançadas.

Ou seja, caso seu template _Blank Activity_ esteja gerando um código complexo e você queira algo simples, use o template _Empty Activity_. Caso você não esteja visualizando este novo template, basta [atualizar o Eclipse ADT](http://developer.android.com/sdk/installing/installing-adt.html).
