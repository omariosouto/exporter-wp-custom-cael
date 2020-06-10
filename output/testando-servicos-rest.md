---
title: "Testando serviços REST"
date: "2014-07-08"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma pergunta frequente em muitos projetos é [qual tipo de testes devo fazer](https://blog.caelum.com.br/unidade-integracao-ou-sistema-qual-teste-fazer)? A resposta, como quase tudo em nossa área, não é única, e cada caso deve ser analisado separadamente, sendo um específico o de aplicações WEB e serviços REST.

**Testes de sistema** em aplicações WEB tradicionais são usados para garantir todo o fluxo de uso de uma aplicação: desde o usuário abrindo a página inicial até os cliques na interface apresentada no browser. Mas no caso de um serviço REST, o cliente é uma outra aplicação, o código de programação de um cliente.

Por exemplo, imagine o serviço REST que traz as informações de um carrinho fazendo uma requisição GET para http://www.meuservidor.com/carrinhos/2378634:

\[code\] GET /carrinhos/2378634 HTTP/1.1 Host: www.meuservidor.com \[/code\]

Trazendo o resultado:

\[code\] <carrinho> <produtos> <produto> <id>6237</id> <quantidade>1</quantidade> <nome>Videogame 4</nome> <preco>4000</preco> </produto> <produto> <id>3467</id> <quantidade>2</quantidade> <nome>Jogo de esporte</nome> <preco>120</preco> </produto> </produtos> <total>4120</total> <entrega> <rua>Rua Vergueiro 3185, 8 andar</rua> <cidade>São Paulo</cidade> </entrega> </carrinho> \[/code\]

Como escrever um teste para isso? Primeiro desejamos efetuar a requisição GET de verdade, isto é, criar um cliente web, como um navegador, que faça o GET e traga o xml que é o resultado esperado. Em Java temos a API HttpClient do Apache que permite fazer tais requisições, mas desde a última versão do JAX-RS temos uma API cliente ainda mais avançada, que vamos utilizar. Começamos criando um cliente HTTP:

\[code\] Client client = ClientBuilder.newClient(); \[/code\]

Precisamos de um cliente pois ele funciona como um navegador: diversas requisições efetuadas pelo mesmo cliente compartilham os mesmos cookies etc, portanto não pretendemos criar um cliente por requisição. O nosso servidor alvo é 'www.meuservidor.com':

\[code\] WebTarget target = client.target("http://www.meuservidor.com"); \[/code\]

E pretendemos acessar a URI '/carrinhos/':

\[code\] String resultado = target.path("/carrinhos/2378634").request().get(String.class); \[/code\]

Agora podemos conferir que o XML retornado contem aquilo que desejamos:

\[code\] Assert.assertEquals("Rua Vergueiro 3185, 8 andar", resultado); \[/code\]

Claro que o código completo acessa a URI desejada, faz a requisição e confere o resultado, mas ela não é necessariamente perfeita: ela só conferiu que parte da String está contida. Talvez o seu teste de sistema queira conferir que o resultado retornado era o esperado. O JAX-RS utiliza por padrão a serialização do JAXB, padrão oficial de serialização do Java. Portanto podemos pedir para ele deserializar o conteúdo utilizando o JAXB para um Carrinho, e então conferir sua rua:

\[code\] Carrinho carrinho = (Carrinho) target.path("/carrinhos/1").request().get(Carrinho.class); Assert.assertEquals("Rua Vergueiro 3185, 8 andar",carrinho.getRua()); \[/code\]

Com isso temos um teste completo que acessa uma URI via GET, retorna o dado deserializado e garante o valor que estamos esperando. Podemos utilizar o resto da API cliente do JAX-RS para fazer diversos outros tipos de requisição, interagindo com o servidor, alterando o estado do mesmo e confirmando os resultados.

Mas para executar todos esses testes você precisa ter seu servidor ligado... e aí entra um problema antigo: posso levantar meu Tomcat, meu Jetty, meu servidor de aplicação ou qualquer outra aplicação, como desenvolvedor, mas os testes podem se misturar com o que estou fazendo no navegador e quebrarem. Por exemplo, se eu começo a interagir com o servidor enquanto os testes rodam, posso quebrar o teste.

O que fazer? Levantar um servidor só para o teste e só enquanto o teste é executado. No caso do Jersey ([implementação padrão do JAX-RS](https://blog.caelum.com.br/arquitetura-rest-com-java-jax-rs/)), podemos fazer isso com o [Grizzly](https://grizzly.java.net/) antes de todo teste:

\[code\] ResourceConfig config = new ResourceConfig().packages("br.com.alura.loja"); URI uri = URI.create("http://localhost:7575/"); HttpServer server = GrizzlyHttpServerFactory.createHttpServer(uri, config); \[/code\]

E derrubá-lo após cada teste:

\[code\] server.stop(); \[/code\]

Pronto! A cada teste nosso servidor é levantado em uma porta específica, que não atrapalha meu desenvolvimento, o mesmo é executado, e o servidor é derrubado. Já existem diversas bibliotecas que facilitam esse trabalho de levantar servidores para rodar testes end-to-end, desde o antigo [Cargo](http://cargo.codehaus.org/), até a API proprietária de cada servidor, como neste caso o Grizzly.

Você pode conhecer mais sobre testes end-to-end com sistemas REST, inclusive como navegar entre recursos no [curso online do Alura](http://www.alura.com.br/cursos-online-agile/rest-assured).
