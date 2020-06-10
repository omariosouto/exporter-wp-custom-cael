---
title: "Morte à sessão! Entenda esse tal de stateless session com tokens"
date: "2017-04-03"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[Microservices](https://blog.caelum.com.br/arquitetura-de-microservicos-ou-monolitica/) pelo visto [já é uma realidade](http://hipsters.tech/microservicos-hipsters-17/), há inúmeros [artigos](https://blog.caelum.com.br/conheca-o-kumuluzee-seu-novo-framework-para-microservices/) falando sobre [servidores](https://blog.caelum.com.br/introducao-aos-microservices-com-spark-retorno-de-dados-dos-correios/), [implementações](https://blog.caelum.com.br/micro-profile-javaee-com-wildfly-swarm/), arquitetura. Daqui a pouco começam [a surgir artigos](http://basho.com/posts/technical/microservices-please-dont/) falando mal ou que o [conceito](https://www.ciklum.com/blog/the-good-the-bad-and-the-ugly-of-microservice-architecture/) está [morto](https://martinfowler.com/articles/is-tdd-dead/). Enfim, você provavelmente já sabe o ciclo de vida de uma BuzzWord. Voltando, eu não consigo desassociar MicroServices de um outro conceito que já foi uma buzzword há um tempo atrás. REST.

Demorei anos para entender o conceito, algo que o Guilherme Silveira já falava lá em [2009 como hipermída (HATEOAS)](https://blog.caelum.com.br/hipermidia-e-contratos-dinamicos-menor-acoplamento/), [2010 como diminuir acoplamento,](https://blog.caelum.com.br/diminuindo-acoplamento-de-sistemas-com-rest-e-video/) só fui compreender há pouco tempo. E um desses conceitos sempre foi para mim obscuro demais: RESTFul API, mais especificamente: **ser stateless**. Portanto, é esse conceito que eu vou tentar ajudar você a entender também.

Essa dúvida [não é só minha](http://stackoverflow.com/questions/34130036/how-to-understand-restful-api-is-stateless): "_como fugir da famosa [Session](http://stackoverflow.com/questions/3105296/if-rest-applications-are-supposed-to-be-stateless-how-do-you-manage-sessions) em um contexto RESTFul API?_". Aposto que foi um dos primeiros conceitos que você aprendeu ao lidar com programação web, que existe a possibilidade de ter um espaço alocado no servidor para cada cliente e nele você pode guardar qualquer informação relevante para o atendimento deste. Então, surge esse conceito de RESTFul API e diz que sessão na verdade não é uma boa ideia?!?!?!?! Por que isso?

## vamos puxar da raiz...

Segundo [Roy Fielding](https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm), ser stateless é a capacidade que um servidor tem de processar requisições de um cliente sem precisar usufruir de nenhum dado já previamente guardado nele. Logo, o envio de dados ao servidor deve conter **TODA** informação necessária para ser compreendida e processada. Em suma, o estado quem mantém é o cliente e não o servidor.

Agora, novamente, por que isso?

Há [algumas respostas](https://brockallen.com/2012/04/07/think-twice-about-using-session-state/) para isso, mas uma delas poderia ser quanto à escalabilidade.

## imaginando o cenário...

[![](https://blog.caelum.com.br/wp-content/uploads/2017/03/Screen-Shot-2017-03-28-at-10.28.27-PM-90x300.png)](https://blog.caelum.com.br/wp-content/uploads/2017/03/Screen-Shot-2017-03-28-at-10.28.27-PM.png)

Se o servidor cai ou aumenta o número de clientes, qual seria a solução? Ok, colocar mais um servidor e um [load balancer](https://httpd.apache.org/docs/2.4/mod/mod_proxy_balancer.html) na frente.

[![](https://blog.caelum.com.br/wp-content/uploads/2017/03/Screen-Shot-2017-03-28-at-10.28.40-PM-189x300.png)](https://blog.caelum.com.br/wp-content/uploads/2017/03/Screen-Shot-2017-03-28-at-10.28.40-PM.png)

Agora temos um problema. O servidor 1 contém os dados do cliente, se ele cai você precisa redirecionar as requisições para o servidor 2 (ok, load balancer já faz isso). Além disso, você precisa também pegar todos os dados que estavam no servidor 1, [passivá-los e transferí-los](http://stackoverflow.com/questions/10494431/sticky-and-non-sticky-sessions) para o servidor 2. **ANTES DELE CAIR!!!**

Agora imagina que o seu load balancer possui um algoritmo que alterna as requisições entre servidores ao invés de fazer o [stick session](http://stackoverflow.com/questions/1040025/difference-between-session-affinity-and-sticky-session). A primeira é o servidor 1 que responde, a segunda é o servidor 2. E agora? A sessão do seu usuário tem que ficar trafegando entre servidores. Essa sincronia começa a ficar complicada.  Logo, talvez a melhor solução, seja simplesmente **evitar o problema**. Aqui o peso de manter uma sessão no servidor começa a ser cobrado. O que você, eu e muuuuuuuuiiiiiiiittaaa gente acaba fazendo é simplesmente migrar o usuário para o servidor 2 sem os dados prévios. Ou seja, o usuário **não "percebe"** que o serviço não ficou disponível.

[![](https://blog.caelum.com.br/wp-content/uploads/2017/03/Screen-Shot-2017-03-28-at-10.28.46-PM-171x300.png)](https://blog.caelum.com.br/wp-content/uploads/2017/03/Screen-Shot-2017-03-28-at-10.28.46-PM.png)

Perdemos aqui um item primordial, [Escalabilidade horizontal](http://ntakashi.net/entendendo-o-que-e-escalabilidade/). À medida que o número de clientes aumenta, são adicionados novos servidores para permitir que eles atendam à demanda crescente. E, de fato, é um pouco mais complicado de se implementar quando estamos em um arquitetura stateful.

Portanto, compreendemos o porquê de um sistema RESTFul ser totalmente stateless. As informações devem ficar no cliente e não no servidor. Por conseguinte, podemos aumentar o número de servidores e cada um será capaz de responder por novas requisições sem se preocupar em buscar dados anteriores, afinal, o cliente já vai fornecer as credenciais de quem ele é e outras informações adicionais.

## frameworks podem e vão ajudar...

A resposta você provavelmente já sabe. **Token**! Já, inclusive, falamos sobre [OAuth em um contexto MicroServices Rest](https://blog.caelum.com.br/autenticacao-rest-oauth2-em-java-com-apache-oltu/). Uma outra solução seria o [Json Web Token](https://jwt.io/), que é um [padrão aberto](https://tools.ietf.org/html/rfc7519) para representação das informações que duas partes [(cliente e servidor)](https://dzone.com/articles/jwtjson-web-tokens-are-better-than-session-cookies?edition=290898&utm_source=Spotlight&utm_medium=email&utm_campaign=web%20dev%202017-04-13) acordaram previamente. De fácil instalação, manipulação (fluent interface API), implementação em múltiplas linguagens (.NET, Java, Phyton) e inúmeros [artigos](http://jonatan.nilsson.is/stateless-tokens-with-jwt/) [falando](https://medium.com/vandium-software/5-easy-steps-to-understanding-json-web-tokens-jwt-1164c0adfcec) sobre o tema.

O conceito básico é: você como usuário manda sua autenticação para o servidor. Ele valida e identifica seus papéis e gera um token com essas informações para o cliente guardar. [(Cookie X HTML 5 web storage).](https://stormpath.com/blog/where-to-store-your-jwts-cookies-vs-html5-web-storage) Quando requisições são feitas ao servidor, o token é enviado e as informações são recuperadas.

Para isso o token deve ser assinado com uma **chave**, que deve ficar no servidor. Se você precisar subir mais servidores, eles devem ter a mesma chave.

\[java\]

//salvando a String em um arquivo, ele poderá ser compartilhado entre servidores. String chaveAsString = Base64.getEncoder().encodeToString(MacProvider.generateKey().getEncoded());

//recuperando a chave do arquivo SecretKey chave; String nextLine = new Scanner(file).nextLine(); byte\[\] chaveEmBytes = Base64.getDecoder().decode(nextLine); chave = new SecretKeySpec(chaveEmBytes, SignatureAlgorithm.HS512.getJcaName());

\[/java\]

## Nosso famoso token...

Ao gerar o token você escolhe o conteúdo, algoritmo, tempo de expiração, dentre outros. O conteúdo precisa ser uma String, eu costumo parsear para Json.

\[java\] public String gerar(Object objeto) { String objetoAsJson = new Gson().toJson(objeto); return Jwts.builder().setSubject(objetoAsJson).signWith(SignatureAlgorithm.HS512, chave).compact(); }

public <T> getConteudo(String token, Class<T> clazz) { try { String conteudo = Jwts.parser().setSigningKey(chave).parseClaimsJws(token).getBody().getSubject(); return ((T) new Gson().fromJson(conteudo, clazz)); }catch (io.jsonwebtoken.SignatureException | io.jsonwebtoken.ExpiredJwtException e) { throw new TokenInvalidoException(e); } } \[/java\]

## E o Logoff?

O token ficará válido pelo tempo definido no **setExpiration**. Todavia, o usuário pode [invalidar o token](http://stackoverflow.com/questions/28759590/best-practices-to-invalidate-jwt-while-changing-passwords-and-logout-in-node-js) antes do prazo (logout). [Como fazer isso](http://stackoverflow.com/questions/21978658/invalidating-json-web-tokens)? A forma mais fácil seria apenas remover o token do cliente. Se ele está guardado em um Cookie, basta remové-lo.

Mas isso não resolve o problema de alguém ainda ter guardado o token em outro lugar e usá-lo para obter informações do servidor. O que nos leva a uma segunda solução: **black list** de tokens. Nesse contexto, teria que existir uma lista guardada em memória(Redis, MemCached, Bolt DB), compartilhada entre os servidores,  que seria **consultada antes de cada requisição.**

Houve essa discussão recentemente na nossa lista interna e com certeza esse é um dos pontos mais chatos, invalidar o token.

Por fim, uma outra decisão arquitetural que precisará ser discutida pela equipe é se todos os servidores vão saber descriptografar o token ou se existirá um servidor central responsável por essa abordagem. Prós e contras em ambas decisões como _evitar espalhar código, ponto único de falha, requisições duplicadas (uma requisição sempre para o servidor de autenticação)_. E se o servidor de autenticação cair?????? E se a forma de lidar com o token precisar ser alterada??? Eu gostei bastante do **JWT** e achei mais fácil de lidar do que o OAuth. E você, já implementou sua API RESTFul totalmente stateless?
