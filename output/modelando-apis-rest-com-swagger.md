---
title: "Modelando APIs REST com Swagger"
date: "2016-02-23"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Atualmente é bem comum que empresas utilizem _APIs REST_ para a integração de aplicações, seja para consumir serviços de terceiros ou prover novos serviços.

Ao consumir uma API existente, precisamos conhecer as funcionalidades disponíveis e detalhes de como invocá-las: recursos, URIs, métodos, Content-Types e outras informações.

Ao prover uma nova API REST, além da implementação, há outras duas preocupações comuns: como **modelar** e **documentar** a API?

## Ferramentas para modelagem e documentação de APIs REST

Em um Web Service do estilo _SOAP_ temos o _WSDL_, que funciona como uma documentação (para máquinas) do serviço, facilitando a geração automatizada dos clientes que vão consumi-lo. Além disso, podemos modelar nosso serviço escrevendo o WSDL, em uma abordagem conhecida como _Contract-First_. Não é nada legível nem fácil de escrever, mas funciona. Só que no mundo dos Web Services REST não temos o WSDL. E agora?

Algumas ferramentas para nos auxiliar nessa questão foram criadas, e dentre elas temos: WSDL 2.0, WADL, API Blueprint, RAML e Swagger.

Neste post vamos abordar o **Swagger**, que é uma das principais ferramentas utilizadas para modelagem, documentação e geração de código para APIs do estilo REST.

## Mas o que exatamente é o Swagger?

O Swagger é um projeto composto por algumas ferramentas que auxiliam o desenvolvedor de APIs REST em algumas tarefas como:

- Modelagem da API
- Geração de documentação (legível) da API
- Geração de códigos do Cliente e do Servidor, com suporte a várias linguagens de programação

Para isso, o Swagger especifica a **OpenAPI**, uma linguagem para descrição de contratos de APIs REST. A [OpenAPI](https://github.com/OAI/OpenAPI-Specification) define um formato JSON com campos padronizados (através de um [JSON Schema](http://json-schema.org/)) para que você descreva recursos, modelo de dados, URIs, Content-Types, métodos HTTP aceitos e códigos de resposta. Também pode ser utilizado o formato YAML, que é um pouco mais legível e será usado nesse post.

Além da OpenAPI, o Swagger provê um ecossistema de ferramentas. As principais são:

- [Swagger Editor](http://swagger.io/swagger-editor/) - para a criação do contrato
- [Swagger UI](http://swagger.io/swagger-ui/) - para a publicação da documentação
- [Swagger Codegen](http://swagger.io/swagger-codegen/) - para geração de "esqueletos" de servidores em mais de 10 tecnologias e de clientes em mais de 25 tecnologias diferentes

Nesse post, vamos focar na parte de modelagem de uma nova API. Futuramente teremos outro post focando na documentação de uma API já existente.

## Modelando a API da Payfast

Para modelar nossa nova API, utilizaremos o Swagger Editor. Você pode [instalá-lo localmente](https://github.com/swagger-api/swagger-editor#running-locally), executando uma aplicação NodeJS, ou utilizar a versão online em [editor.swagger.io](http://editor.swagger.io/).

Vamos modelar a API da Payfast, uma aplicação de pagamentos bem simples que é estudada no curso [SOA na prática](https://www.caelum.com.br/curso-java-ee-soa-web-services-mensageria/).

Pra começar, devemos definir algumas informações iniciais, como a versão do Swagger que estamos usando: \[code language="ruby"\] swagger: '2.0' \[/code\]

O título, descrição e versão da API devem ser definidos: \[code language="ruby"\] info: title: Payfast API description: Pagamentos rápidos version: 1.0.0 \[/code\]

Em `host`, inserimos o endereço do servidor da API, em `basePath` colocamos o contexto da aplicação e em `schemes` informamos se a aplicação aceita HTTP e/ou HTTPS.

\[code language="ruby"\] host: localhost:8080 basePath: /fj36-payfast/v1 schemes: - http - https \[/code\]

## Defininindo o modelo de dados

De alguma forma, precisamos definir quais dados são recebidos e retornados pela API.

Na nossa API, recebemos dados de uma _Transação_, que tem um código, titular, data e valor. A partir disso, geramos um _Pagamento_ com id, status e valor.

![Modelo de dados da API do Payfast](https://blog.caelum.com.br/wp-content/uploads/2016/02/modelo-payfast.png)

Em um WSDL, esse modelo de dados é definido através de um XML Schema (XSD). No caso do Swagger, o modelo de dados fica em um [JSON Schema](http://json-schema.org/) na seção `definitions` do contrato.

De acordo com o JSON Schema, em `type` podemos usar tipos primitivos de dados para números inteiros (`integer`), números decimais (`number`), textos (`string`) e booleanos (`boolean`). Esses tipos primitivos podem ser modificados com a propriedade `format`. Para o tipo `integer` temos os formatos `int32` (32 bits) e `int64` (64 bits, ou long). Para o `number`, temos os formatos `float` e `double`. Não há um tipo específico para datas, então temos que utilizar uma `string` com o formato `date` (só data) ou `date-time` (data e hora).

Além dos tipos primitivos, podemos definir objetos com um `type` igual a `object`. Esses objetos são compostos por várias outras propriedades, que ficam em `properties`.

No nosso caso, o modelo de dados com os objetos `Transacao` e `Pagamento` ficaria algo como:

\[code language="ruby"\] definitions: Transacao: type: object properties: codigo: type: string titular: type: string data: type: string format: date valor: type: number format: double Pagamento: type: object properties: id: type: integer format: int32 status: type: string valor: type: number format: double \[/code\]

## Defininindo os recursos da API

Com o modelo de dados pronto, precisamos modelar os recursos da nossa API e as respectivas URIs. No Payfast, teremos o recurso `Pagamento` acessível pela URI `/pagamentos`.

Um POST em `/pagamentos` cria um novo pagamento. Se o pagamento criado tiver o id 1, por exemplo, as informações estariam acessíveis na URI `/pagamentos/1`.

Podemos fazer duas coisas com o nosso pagamento: para confirmá-lo, devemos enviar um PUT para `/pagamentos/1`; para cancelá-lo, enviamos um DELETE.

![Máquina de estados da API do Payfast](https://blog.caelum.com.br/wp-content/uploads/2016/02/estados-payfast.png)

No Swagger, as URIs devem ser descritas na seção `paths`:

\[code language="ruby"\] paths: /pagamentos: post: summary: Cria novo pagamento

/pagamentos/{id}: put: summary: Confirma um pagamento delete: summary: Cancela um pagamento \[/code\]

## Definindo os parâmetros de request

Para a URI `/pagamentos`, que recebe um POST, é enviada uma transação no corpo da requisição, que deve estar no formato JSON. É feita uma referência ao modelo `Transacao` definido anteriormente.

\[code language="ruby" highlight="5-12"\] paths: /pagamentos: post: summary: Cria novo pagamento consumes: - application/json parameters: - in: body name: transacao required: true schema: $ref: '#/definitions/Transacao' \[/code\]

Já para a URI `/pagamentos/{id}`, é definido um _path parameter_ com o id do pagamento. Esse parâmetro pode ser descrito na seção `parameters`, logo acima da seção `paths`, e depois referenciado nos métodos.

\[code language="ruby" highlight="1-8,16-17,20-21"\] parameters: pagamento-id: name: id in: path description: id do pagamento type: integer format: int32 required: true paths: /pagamentos: #código omitido...

/pagamentos/{id}: put: summary: Confirma um pagamento parameters: - $ref: '#/parameters/pagamento-id' delete: summary: Cancela um pagamento parameters: - $ref: '#/parameters/pagamento-id' \[/code\]

## Definindo os tipos de response

Definidos os parâmetros de _request_, precisamos modelar o _response_.

Depois da criação do pagamento, deve ser retornado um response com o status `201` (Created) juntamente com a URI do novo pagamento no _header_ `Location` e uma representação em JSON no corpo da resposta.

\[code language="ruby" highlight="7-8,10-18"\] paths: /pagamentos: post: summary: Cria novo pagamento consumes: - application/json produces: - application/json #código omitido... responses: '201': description: Novo pagamento criado schema: $ref: '#/definitions/Pagamento' headers: Location: description: uri do novo pagamento type: string \[/code\]

Após a confirmação de um pagamento, é simplesmente retornado o status `200` (OK). O mesmo retorno acontece após um cancelamento.

\[code language="ruby" highlight="6-8,13-15"\] /pagamentos/{id}: put: summary: Confirma um pagamento parameters: - $ref: '#/parameters/pagamento-id' responses: '200': description: 'Pagamento confirmado' delete: summary: Cancela um pagamento parameters: - $ref: '#/parameters/pagamento-id' responses: '200': description: 'Pagamento cancelado' \[/code\]

O contrato final do exemplo utilizado pode ser aberto no Swagger Editor em: [bit.ly/swagger-editor-payfast-api](http://bit.ly/swagger-editor-payfast-api)

Perceba que no menu superior temos a opção _Generate Server_ para gerar um esqueleto do servidor em Java (JAX-RS e Spring-MVC), PHP (Slim e Silex), Python (Flask), entre outras tecnologias. Há também a opção _Generate Client_, que gera clientes nessas tecnologias e em diversas outras.

## Concluindo

A abordagem utilizada nesse post foi a conhecida como _Contract-First_ ou _API-First Development_.

Modelamos a API pensando nos dados, nos recursos, URIs, métodos, parâmetros e respostas. Começamos a definição do serviço criando primeiro a API de comunicação, para só posteriormente pensar na implementação.

Esta abordagem gera um desacoplamento entre implementação e interface de uso, além de permitir que tando o lado cliente quanto o lado servidor possam iniciar seu desenvolvimento assim que a API estiver definida, mesmo sem uma implementação finalizada.

Uma outra abordagem (talvez mais comum) é começar pela implementação para só depois pensar na documentação e talvez realizar ajustes de modelagem. Essa abordagem é conhecida como _Contract-Last_ e o uso dela com Swagger será abordado em outro post.

E você? Já usou o Swagger em algum projeto para modelar uma nova API, no estilo Contract-First? Conte-nos como foi a experiência!
