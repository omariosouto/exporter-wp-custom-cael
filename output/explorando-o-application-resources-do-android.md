---
title: "Explorando o Application Resources do Android"
date: "2012-02-09"
author: "andre.silva"
authorEmail: "andre.silva@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Desenvolver para Android não é uma tarefa fácil. Hoje existem cerca de 700 dispositivos que rodam Android. Além de rodarem [diversas versões do android](http://developer.android.com/resources/dashboard/platform-versions.html), eles possuem hardwares, telas e tamanhos diferentes. Uma grande dor de cabeça para o desenvolvedor Android, a conhecida fragmentação da plataforma.

Para facilitar o desenvolvimento e  aliviar a dor de cabeça do desenvolvedor, a plataforma Android desenvolveu uma ferramenta fantástica: [o Application Resources](http://developer.android.com/guide/topics/resources/index.html).

O Application Resources é muito útil no desenvolvimento de aplicativos que serão usados por diversos dispositivos, principalmente se for no caso para smartphones e tablets.

![](http://developer.android.com/images/resources/resource_devices_diagram2.png)

Fonte: http://developer.android.com/images/resources/resource\_devices\_diagram2.png

Para se utilizar do Application Resources, basta colocar [qualifiers](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources) nas pastas de [Resources (`res/`)](http://developer.android.com/guide/topics/resources/available-resources.html). Quando criamos um projeto Android ele já tem a pasta layout. Se, por exemplo, formos criar layouts diferentes (mais adequados) para dispositivos de telas pequenas, criaremos a pasta layout`-small`. Nesse caso, `small` é seu qualifier.

Quando sua aplicação é executada, ela carregará os resources de acordo com os _qualifiers_ do seu dispositivo. Se você não utilizar nenhum qualifier, a aplicação utilizará os resources defaults (sem qualifiers. Ex: `layout`, `values`, etc.). Aplicações podem possuir diferentes diretórios para diferentes qualifiers, como a figura mostra:

![](https://blog.caelum.com.br/wp-content/uploads/2012/01/application-resources-example.png)

Se você vai desenvolver sua aplicação para uma grande quantidade de dispositivos, é praticamente obrigatório o uso do Application Resources. Ele vai evitar que você faça arquivos de layouts desnecessários, ou seja, ao invés de ter um arquivo XML dentro da pasta layout para cada tipo de tela, você terá o arquivo com o mesmo nome em uma pasta layout e outro com o **conteúdo diferente** em `layout-small`. Com isso você pode aproveitar as capacidades (processamento, qualidade e tamanho de telas) dos dispositivos.

Uma curta aplicação de exemplo [está disponível no github](https://github.com/andrelrs/ApplicationResourcesExample). Nela exibimos um texto diferente para cada tipo de dispositivo: dispositivo com telas pequenas, telas médias, telas médias em landscape, telas grandes e telas grandes com versão 14. O Application Resources não serve só para internacionalização, no exemplo a ideia é exibir qual o tipo de tela que o dispositivo tem **alterando apenas o conteúdo** do arquivo `strings.xml` de acordo com os qualifiers. Seguindo esta ideia, também podemos utilizar o Application resources principalmente no `styles.xml`, facilitando trabalhar com os diferentes tipos e orientações de telas.

Uma aplicação que tem muitos detalhes diferentes em dispositivos diferentes pode aparecer com diversos qualifiers grandes, como `values-**pt-large-v14-land**`.

Mas tome cuidado, não saia colocando qualifiers em tudo. Antes disso, procure [usar o que realmente você vai precisar](http://developer.android.com/guide/topics/resources/providing-resources.html#BestMatch). Esperamos te encontrar aqui no [curso de desenvolvimento para Android](http://www.caelum.com.br/curso/fj-57-desenvolvimento-google-android/)!
