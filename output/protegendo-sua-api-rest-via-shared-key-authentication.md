---
title: "Protegendo sua API Rest via Shared Key Authentication"
date: "2015-08-12"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Atualmente todos nós usamos a internet diariamente e com muita frequência. Seja para fins profissionais, acadêmicos ou pessoais. E na web existem diversos sites como Dropbox, GitHub, PayPal, Slack, SlideShare e LinkedIn, que nos fornecem _serviços_ para resolver problemas comuns do dia a dia.

A maioria desses sites fornece também uma _API Rest_, para que desenvolvedores possam interagir com os serviços e recursos disponibilizados, e até mesmo desenvolver novos serviços baseados nestes.

Mas ao criar um serviço web e disponibilizá-lo por meio de uma API Rest, devemos ter uma preocupação especial em relação a _segurança_. Afinal, os clientes do nosso serviço esperam que seus recursos estejam protegidos e não sejam acessados e/ou modificados por terceiros não autorizados.

Como fazer então para proteger uma API Rest?

Uma primeira abordagem seria aplicar o conceito de _autenticação_, baseado em _login/senha_, por exemplo. Assim, a cada chamada feita à API Rest, deve-se passar estes dados para que o _provedor_ do serviço verifique se quem está chamando a API realmente está cadastrado como _cliente_.

E para que um cliente não tenha acesso aos recursos de outro cliente, o provedor pode aplicar o conceito de _autorização_, restringindo o acesso aos recursos apenas ao seu _proprietário_ ou a algum _terceiro_ que tenha sido autorizado por ele. Nesse caso o protocolo [OAuth](http://oauth.net) se encaixa muito bem na maioria dos casos.

Mas apenas autenticação/autorização não basta, ainda mais no ambiente web onde estamos sujeitos a diversos tipos de ataques, sendo um deles o [Man In The Middle](http://en.wikipedia.org/wiki/Man-in-the-middle_attack "Man In The Middle"), onde um hacker intercepta a comunicação entre o cliente e o servidor, e consegue ter acesso às informações sendo trafegadas pela rede, podendo até mesmo modificá-las. Temos que garantir também a _Confidencialidade_ e _Integridade_ das informações.

Para proteger as informações sendo trafegadas pela rede podemos utilizar técnicas como _criptografia_ e _assinatura digital_, e isto é fácil de conseguir se utilizarmos o protocolo _HTTPS_ juntamente com um certificado digital.

Até aqui, para quem já desenvolve aplicações web, não tem nada de muito novo. Essas mesmas técnicas e ferramentas geralmente também são utilizadas ao desenvolvermos aplicações web. Mas se engana quem acha que devemos parar por aqui. Existem ainda diversos outros tipos de ataques, sendo outros bem comuns: _SQL Injection_, _Parameter Injection_, _Cross-site Scripting(XSS)_ e _Cross-site Request Forgery(CSRF)_.

E mesmo utilizando o HTTPS ainda podemos ter problemas, pois ele não evita o ataque Man In The Middle. Mas como toda requisição é criptografada e assinada digitalmente, o hacker não poderá modificá-la e nem conseguirá entendê-la.

Porém, às vezes podemos ter algum tipo de restrição técnica no projeto que nos impede de utilizar o HTTPS, e com isso devemos ter uma preocupação maior, já que nesse cenário, mesmo utilizando criptografia e assinatura digital, um hacker pode enviar uma réplica de alguma requisição ao servidor.

Esse é mais um tipo de ataque, conhecido como _Replay Attack_, onde um hacker, ao interceptar as requisições feitas pelo cliente ao servidor, as reenvia uma ou mais vezes, sendo que o servidor as aceita, pois acredita que as mesmas foram originadas pelo cliente legítimo.

Como nos proteger desse tipo de ataque?

Para nos proteger, devemos fazer com que cada requisição seja única. Sendo assim, quando um hacker enviar uma réplica de uma requisição, o servidor deve rejeitá-la, pois detecta que uma requisição idêntica a esta já foi enviada anteriormente.

Uma técnica utilizada para isso é conhecida como _Shared Key Authentication_. Essa técnica é utilizada por provedores de cloud computing como [Amazon](http://docs.aws.amazon.com/AmazonS3/latest/dev/S3_Authentication2.html) e [Azure](https://msdn.microsoft.com/en-us/library/azure/dd179428.aspx).

A ideia consiste na elaboração de um _algoritmo_ que será responsável por gerar uma identificação única para cada requisição, devendo utilizar para isso informações da própria requisição como URI, Método HTTP e Data. Todos os clientes do serviço devem utilizar esse mesmo algoritmo para a geração da identificação de cada requisição a ser feita ao servidor, e enviá-la como parâmetro para que o servidor faça a validação.

Traduzindo isso para código, seria algo como:

\[java\] public class GeradorIdentificacaoRequisicao {

private static final String DELIMITADOR = "\\n"; private final Criptografia criptografia;

public GeradorIdentificacaoRequisicao(Criptografia criptografia) { this.criptografia = criptografia; }

public String geraIdDaRequisicao(HttpRequest request) { String uri = request.getURI(); HttpMethod metodo = request.getMethod(); LocalDateTime data = LocalDateTime.now();

String id = uri + DELIMITADOR + metodo + DELIMITADOR + data; return criptografia.criptografa(id); }

} \[/java\]

Repare que no código anterior concatenamos as informações da requisição, utilizando o _/n_ como delimitador, para gerar o id dela. Além disso, o id da requisição que foi gerado não é devolvido em texto puro, mas sim criptografado utilizando algum algoritmo de criptografia qualquer, como _SHA2_. Esse id deve ser enviado como parâmetro da requisição e o servidor utilizará o mesmo algoritmo para gerá-lo novamente, e então fará a comparação para identificar se a requisição é válida.

Devemos ainda melhorar o algoritmo, pois no momento ele pode gerar id iguais para clientes distintos. Como todos os clientes utilizarão o mesmo algoritmo, se dois ou mais deles fizerem uma requisição para a mesma URI, na mesma hora e utilizando o mesmo método HTTP, os ids gerados para ambas as requisições serão idênticos.

É aqui que entra a ideia do _Shared Key_. Nosso algoritmo precisa ser alterado para levar mais uma informação em consideração ao gerar o id da requisição: a chave compartilhada do cliente. Essa chave é na verdade uma senha gerada pelo servidor para o cliente. Cada cliente tem a sua chave distinta, e ela deve ser compartilhada apenas entre o servidor e o cliente. Adaptando nosso código anterior:

\[java highlight="10,15"\] public class GeradorIdentificacaoRequisicao {

private final Criptografia criptografia; private static final String DELIMITADOR = "\\n";

public GeradorIdentificacaoRequisicao(Criptografia criptografia) { this.criptografia = criptografia; }

public String geraIdDaRequisicao(HttpRequest request, String sharedKey) { String uri = request.getURI(); HttpMethod metodo = request.getMethod(); LocalDateTime data = LocalDateTime.now();

String id = uri + DELIMITADOR + metodo + DELIMITADOR + data + DELIMITADOR + sharedKey; return criptografia.criptografa(id); }

} \[/java\]

Agora duas requisições iguais, mas originadas de clientes distintos, produzirão ids distintos. Nesse caso o cliente precisa também enviar na requisição o seu login como parâmetro, para que o servidor possa recuperar sua chave compartilhada ao receber a requisição, e com isso consiga utilizar o algoritmo para gerar o id da requisição e assim validá-lo.

Existem outros detalhes para se melhorar ainda mais a segurança ao utilizar essa técnica, mas a ideia desse post é apenas mostrar o seu funcionamento de forma geral.

Já utilizou essa técnica em algum projeto? Conte-nos como foi a experiência.
