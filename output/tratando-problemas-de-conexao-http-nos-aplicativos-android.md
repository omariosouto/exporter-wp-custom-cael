---
title: "Tratando problemas de conexão http nos aplicativos Android"
date: "2012-06-25"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Diversas aplicações android utilizam uma conexão com a internet para conseguir recuperar dados essenciais para o correto funcionamento do aplicativo, alguns exemplos são o Facebook e o Twitter . Um detalhe que nem sempre é lembrado é o de prover um feedback útil para o usuário quando a conexão está com problemas. Por exemplo, ela pode estar lenta ou simplesmente pode ter caído. O que fazer nessas situações?

[![](https://blog.caelum.com.br/wp-content/uploads/2012/06/connetion_android.png "connetion_android")](https://blog.caelum.com.br/wp-content/uploads/2012/06/connetion_android.png)

O primeiro caso é o mais comum e, provavelmente, o que menos recebe atenção. O usuário está conectado mas, como a conexão está muito lenta, o uso da aplicação fica quase impossibilitado. Muitas aplicações fazem uso do `[ProgressDialog](http://developer.android.com/reference/android/app/ProgressDialog.html)` ou, [seguindo as mais novas orientações do Google](http://developer.android.com/design/index.html),  do `[ProgressBar](http://developer.android.com/reference/android/widget/ProgressBar.html)`. O problema é que, como a conexão está lenta, esses dois componentes dão a impressão que estão carregando indefinidamente. Lembre-se que mesmo usando uma [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html), o que é recomendado para carregar qualquer dado que seja externo à aplicação, o usuário ainda está sujeito a espera indefinida do processamento da `Task`.

Para tentar fornecer algum feedback para o usuário sobre a situação, pode-se tentar configurar um tempo de expiração da conexão, caso nada seja trafegado. Caso você esteja usando o [Apache HttpComponents](http://hc.apache.org/), a biblioteca padrão para realizar requisições no Android, você pode definir algumas propriedades como `http.socket.timeout` e `http.connection.timeout` para tentar evitar o problema das conexões muito lentas:

\[sourcecode lang="java"\] httpclient.getParams().setIntParameter("http.socket.timeout", 10000); httpclient.getParams().setIntParameter("http.connection.timeout", 4000); \[/sourcecode\]

É bom conhecer as [diversas propriedades e configurações oferecidas pelo HttpComponents](http://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html). Agora você deve ficar preparado para dar o feedback apropriado para seu usuário. Não tem segredo, ao invés de tratar a exception mais genérica, basta tratar `SocketTimeoutException` ou  `ConnectTimeoutException` e devolver uma mensagem mais explicativa para seu usuário, por exemplo: "_Sua conexão está lenta, quer tentar de novo?_".

Uma outra abordagem, ainda mais defensiva, é a de tentar estabelecer um número de tentativas de conexão antes de avisar ao usuário que a conexão está lenta ou que caiu. Para não termos que implementar isso na mão,  [podemos utilizar a biblioteca LoopJ](http://loopj.com/android-async-http/). Ela já realiza todos requests numa _Thread_ separada, fornecendo métodos de callback para casos de sucesso ou falha e inclusive já vem com a estratégia de tentativas pronta! Essa estratégia é muito importante num ambiente onde a internet não é estável e a qualquer momento o sinal pode ser perdido ou ficar muito fraco.

Para a situação em que o usuário não está conectado ou caso tenha ocorrido algum problema no seu servidor, uma `UnknownHostException` é lançada e você pode trata-la informando ao usuário que o sistema não está respondendo naquele momento.

O grande ponto é que por mais que uma exception completamente inesperada tenha sido lançada, simplesmente exibir uma mensagem informando que deu  "_Erro no servidor_" não ajuda muito o usuário. Lembre-se, vários deles não são da área de computação  e não tem nenhuma ideia do que certas mensagens querem dizer. Um outro detalhe importante é que nem sempre o request é essencial para a aplicação, por exemplo o download de um thumbnail. Nessa situação, em caso de falha, pode-se simplesmente ser exibido uma imagem padrão para que isso não interfira na experiência do usuário, além de deixar o thumbnail quebrado linkado para uma nova tentativa de baixá-lo, por exemplo.

Que truques você utiliza para deixar sua aplicação android mais responsiva em relação ao status de sua conexão? Conheça também outras boas práticas no [curso de Android](http://www.caelum.com.br/curso/fj57) da Caelum.
