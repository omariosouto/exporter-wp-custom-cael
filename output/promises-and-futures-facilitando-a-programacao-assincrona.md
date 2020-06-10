---
title: "Promises and Futures, facilitando a programação assíncrona"
date: "2013-08-15"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![scala-weblog-post](https://blog.caelum.com.br/wp-content/uploads/2013/08/scala-weblog-post-300x250.png)](https://blog.caelum.com.br/wp-content/uploads/2013/08/scala-weblog-post.png)

Sempre existem funcionalidades nos nossos sistemas que não necessitam do aguardo do usuário durante o seu processamento. Por exemplo: enviar um email ou gerar um pdf. Aqui na Caelum, temos uma aplicação interna que possibilita a geração dos pdf's das apostilas online. Essa aplicação roda numa instancia micro da Amazon, com recursos um tanto quanto limitados. O fluxo é bem simples: O usuário clica no link de geração e tem que aguardar até o momento em que a apostila fique pronta. Um exemplo de código segue abaixo:

\[code language="java"\] class FluxoDeGeracaoApostilaServlet extends HttpServlet{ public void service(HttpServletRequest req,HttpServletResponse res){ Apostila apostila = Apostila.gera("FJ-31"); Mailer.send("alberto.souza@caelum.com.br",apostila.getLinkDownlaod()); res.getWriter().println("gerando..."); } } \[/code\]

O problema dessa abordagem, já clássico, é a maneira sincrona que resolvemos o problema. O usuário fica travado esperando a geração do pdf, além do disparo do email.  E se demorar demais e expirar o tempo de duração máximo de um request? Um outro problema é que pode acontecer algum problema na geração da apostila e o cliente, depois de ter esperado travado na tela por um tempo, recebe uma exception. Bem frustrante. A solução padrão seria deixar esse código rodar numa _Thread_ separada. Abaixo segue:

\[code language="java"\] class FluxoDeGeracaoApostilaServlet extends HttpServlet{ public void service(HttpServletRequest req,HttpServletResponse res){ new Thread(new GeraApostila()).start(); res.getWriter().println("gerando..."); } }

class GeraApostila implements Runnable{ @Override public void run() { Apostila apostial = Apostila.gera("FJ-31"); Mailer.send("alberto.souza@caelum.com.br",apostila.getLinkDownlaod()); } } \[/code\]

Aqui eu já fui um pouco mais além e deixei o código de notificação via email dentro do próprio método _run._  E esse é um outro problema que temos de atacar agora. A nossa _Thread_ deveria apenas gerar o pdf, mandar email não é responsabilidade dela. Para esse tipo de processamento, onde devemos notificar alguém no final, o uso de _Thread_ já começa a nos complicar. Podemos evoluir e tentar usar a API de _Futures_ do próprio Java, que fica no pacote _java.util.concurrent._ Com ela, podemos rodar algum código de maneira assíncrona, pegar o retorno do processamento e fazer o que precisamos fazer. Abaixo segue o exemplo:

\[code language="java"\] class FluxoDeGeracaoApostilaServlet extends HttpServlet{ public void service(HttpServletRequest req,HttpServletResponse res){ ExecutorService executor = Executors.newFixedThreadPool(10); Future<Apostila> esperandoAGeracao = executor.submit(new Callable<Apostila>() { public Apostila call() throws Exception { return Apostila.gera("FJ-31");; } }); Apostila apostila = esperandoAGeracao.get(); Mailer.send("alberto.souza@caelum.com.br",apostila.getLinkDownlaod()); res.getWriter().println("gerando..."); } } \[/code\]

Um detalhe a se notar, é que o método _get()_ vai travar a execução esperando o processamento assíncrono acabar para podermos continuar. O pior é que precisamos disso, pois o email só pode ser enviado após a geração da apostila.

Aí o problema fica mais complexo, encadear tarefas assíncronas, mesmo com a API de Futures do java fica complicado. Sem contar o tratamento de exception e a sintaxe pouco amigável para este tipo de construção. Para esse tipo de situação, a linguagem _Scala_, na versão 2.10, trouxe a API de [Futures e Promises](http://docs.scala-lang.org/sips/pending/futures-promises.html) cujo objetivo é facilitar realização de trabalhos assíncronos. O código abaixo exige uma certa familiaridade com a linguagem Scala mas, o mais importante é a simplicidade atingida. Para mais indicações de como começar na linguagem, dê uma olhada [nesse link](http://typesafe.com/blog/enterprise-scala-adoption-roadmap-55683). 

\[code language="scala"\] val gerandoApostilas:Future\[List\[Apostila\]\] = future { val arquivos = List("FJ-31","FJ-26","FJ-91","FJ-25","FJ-21","FJ-16","FJ-11") val apostilas = arquivos.map(Apostila.gera) apostilas } \[/code\]

O retorno desse código é similar ao do código Java, porém o código para atingir o objetivo é muito mais simples. Agora caso queiramos encadear o retorno para outro processamento assíncrono, podemos fazer uso do método _map._ Cujo objetivo é o de pegar o retorno de uma _Future_ e gerar outra. Abaixo segue um fluxo mostrando o upload das apostilas para a Amazon.

\[code language="scala"\] val uploadandoParaAmazon:Future\[Seq\[String\]\] = gerandoApostilas.map(apostilas => Amazon.uploadPDF(apostilas)) \[/code\]

Agora podemos, quando acabar o upload, fazer o envio dos emails. O código final ficaria como abaixo:

\[code language="scala"\] val gerandoApostilas:Future\[List\[Apostila\]\] = future { val arquivos = List("FJ-31","FJ-26","FJ-91","FJ-25","FJ-21","FJ-16","FJ-11") val apostilas = arquivos.map(Apostila.gera) apostilas }

val uploadandoParaAmazon:Future\[Seq\[String\]\] = gerandoApostilas.map(apostilas => Amazon.uploadPDF(apostilas))

val emailando = uploadandoParaAmazon.map(urls => Mailer.send(emailDeQuemPediu,urls))

println("ja avisei para os usuarios que tá gerando") \[/code\]

Um último ponto que temos de tratar é que além de realizar o upload os pfds das apostilas, podemos enviar os outros formatos também. O único detalhe é que queremos vincular o envio do email ao término do envio do pdf. Para que, do meio de uma _Future_, possamos liberar outra para começar a executar, podemos usar a API de _Promise._ Como o próprio nome fala, você gera uma espécie de promessa que quando for cumprida vai liberar outra ponta para executar. Abaixo segue o exemplo:

\[code language="scala"\] val envioDePDFParaAmazonConcluido = promise\[Seq\[URL\]\]

val sobeParaAmazon = geraApostilas.map { apostilas => val urls = Amazon.uploadPDF(apostilas) //aqui eu notifico a promise envioDePDFParaAmazonConcluido.success(urls) //mobi e epub Amazon.uploadOutrosFormatos(apostilas) urls } \[/code\]

Agora a outra Future do envio de email pode fazer uso dessa _Promise_  para começar o seu processamento.

\[code language="scala"\] val enviaEmail = future { //poderia enviar um email avisando que já começou a subir os pdfs envioDePDFParaAmazonConcluido.future.onSuccess { case urls => Mailer.send("alberto.souza@caelum.com.br",urls) } } \[/code\]

Como Scala é uma linguagem muito poderosa, podemos usar ela em diversas partes do nosso sistema. Esse caso de processamento assíncrono simples é um ótimo caso. E caso queira, você pode criar uma classe Java apenas para servir de interface de uso com sua API em Scala. E aí, o que achou? Existe algum caso que você visualiza o uso dessa API para lhe ajudar?

Imagem da capa vem de [http://astroneo.wordpress.com/](http://astroneo.wordpress.com/)
