---
title: "Documentando uma API JAX-RS com Swagger"
date: "2016-03-22"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em um [post anterior](https://blog.caelum.com.br/modelando-apis-rest-com-swagger/), falamos sobre como modelar uma API REST com o Swagger. Essa abordagem, em que pensamos nos detalhes da API antes da implementá-la, é conhecida como Contract-First ou API-First.

Uma outra abordagem, talvez mais comum, é iniciar pela implementação da API para só então documentá-la. Essa abordagem é conhecida como _Contract-Last_.

Vamos utilizar o Swagger para documentar uma API REST tanto para máquinas quanto para humanos.

## Lembrando da API do Payfast

Documentaremos a API do Payfast, uma API de pagamentos simplificada que implementamos no curso [SOA na prática](https://www.caelum.com.br/curso-java-ee-soa-web-services-mensageria/), utilizando JAX-RS.

Entre as classes de modelo, temos `Transacao` e `Pagamento`: \[code language="java"\] public class Transacao { private String numero; private String titular; private LocalDate data; private BigDecimal valor;

//getters e setters... } \[/code\]

\[code language="java"\] public class Pagamento { private Integer id; private String status; private BigDecimal valor;

//getters e setters... } \[/code\]

Na nossa API REST, um POST em `/pagamentos` cria um novo pagamento a partir de uma transação. Então é possível confirmar o pagamento com um PUT ou cancelá-lo com um DELETE.

![Máquina de estados da API do Payfast](https://blog.caelum.com.br/wp-content/uploads/2016/02/estados-payfast.png)

Uma implementação dessa API utilizando JAX-RS seria algo como:

\[code language="java"\] @Path("/pagamentos") public class PagamentoResource { //... @POST @Consumes(MediaType.APPLICATION\_JSON) public Response criarPagamento(Transacao transacao) throws URISyntaxException { //aqui, código que cria pagamento a partir da transacao... return Response.created(new URI("/pagamentos/" + pagamento.getId())).entity(pagamento) .type(MediaType.APPLICATION\_JSON).build(); }

@PUT @Path("/{id}") @Produces(MediaType.APPLICATION\_JSON) public Response confirmarPagamento(@PathParam("id") Integer id) { //aqui, código que confirma pagamento... return Response.ok().entity(pagamento).build(); }

@DELETE @Path("/{id}") @Produces(MediaType.APPLICATION\_JSON) public Response cancelarPagamento(@PathParam("id") Integer id) { //aqui, código que cancela pagamento... return Response.ok().entity(pagamento).build(); } } \[/code\]

_Nota: Para que a serialização de/para JSON funcione para o `java.time.LocalDate` da classe `Transacao` em um servidor Java EE 7, são necessárias [algumas configurações](https://gist.github.com/alexandreaquiles/b00ca3fdb35e45f0c53d). Fazemos isso na classe `JacksonJavaTimeConfiguration`._

Não podemos esquecer de habilitar o JAX-RS na nossa aplicação: \[code language="java"\] @ApplicationPath("/v1") public class PagamentoService extends Application { } \[/code\]

## Configurando o Swagger na sua API

Para usar o Swagger para documentar nossa API, precisamos de seus jars. A melhor maneira é utilizar alguma ferramenta de gerenciamento de dependências. Para configurar a dependência com o Maven: \[code language="xml"\] <dependency> <groupId>io.swagger</groupId> <artifactId>swagger-jaxrs</artifactId> <version>1.5.7</version> </dependency> \[/code\]

_Nota: Pode ser interessante excluir algumas dependências transitivas do `swagger-jaxrs`._

Por meio da classe `BeanConfig`, do pacote `io.swagger.jaxrs.config`, fazemos configurações básicas como título, descrição e versão da API, endereço do servidor e contexto da aplicação, se é usado HTTP ou HTTPS e pacotes cujos recursos REST devem ser escaneados. Criamos um objeto dessa classe no construtor de `PagamentoService` e ao invocar o método `setScan`, as configurações são realizadas.

\[code language="java"\] @ApplicationPath("/v1") public class PagamentoService extends Application { public PagamentoService() { BeanConfig conf = new BeanConfig(); conf.setTitle("Payfast API"); conf.setDescription("Pagamentos rápidos"); conf.setVersion("1.0.0"); conf.setHost("localhost:8080"); conf.setBasePath("/fj36-payfast/v1"); conf.setSchemes(new String\[\] { "http" }); conf.setResourcePackage("br.com.caelum.payfast"); conf.setScan(true); } } \[/code\]

Além disso, precisamos carregar as classes `ApiListingResource` e `SwaggerSerializers` do Swagger. Para isso, sobrescrevemos o método `getClasses` de `Application`, em `PagamentoService`. Uma coisa chata é que, ao fazermos isso, precisamos adicionar manualmente a classe `JacksonJavaTimeConfiguration` e o recurso `PagamentoResource`.

\[code language="java"\] @ApplicationPath("/v1") public class PagamentoService extends Application { public PagamentoService() { //código omitido... } @Override public Set<Class<?>> getClasses() { Set<Class<?>> resources = new HashSet<>(); resources.add(JacksonJavaTimeConfiguration.class); resources.add(PagamentoResource.class); //classes do swagger... resources.add(ApiListingResource.class); resources.add(SwaggerSerializers.class); return resources; } } \[/code\]

Para que o Swagger gere a documentação para o nosso recurso, devemos anotar a classe `PagamentoResource` com `@Api`: \[code language="java" highlight="1"\] @Api @Path("/pagamentos") public class PagamentoResource { //restante do código... } \[/code\]

## Obtendo a documentação gerada pelo Swagger

Então, podemos obter o JSON e YAML que descrevem nossa API nas seguintes URLs, respectivamente: [http://localhost:8080/fj36-payfast/v1/swagger.json](http://swagger-payfast.7e14.starter-us-west-2.openshiftapps.com/v1/swagger.json) e [http://localhost:8080/fj36-payfast/v1/swagger.yaml](http://swagger-payfast.7e14.starter-us-west-2.openshiftapps.com/v1/swagger.yaml)

O JSON e o YAML gerados pelo Swagger a partir do nosso recurso `PagamentoResource` usam a linguagem [OpenAPI](https://openapis.org/specification) para detalhar URIs, Content-Types, métodos HTTP aceitos, códigos de resposta e modelos de dados. O YAML gerado ficaria assim: \[code language="ruby"\] --- swagger: "2.0" info: description: "Pagamentos rápidos" version: "1.0.0" title: "Payfast API" host: "localhost:8080" basePath: "/fj36-payfast/v1" schemes: - "http" paths: /pagamentos: post: operationId: "criarPagamento" parameters: - in: "body" name: "body" required: false schema: $ref: "#/definitions/Transacao" responses: default: description: "successful operation" /pagamentos/{id}: put: operationId: "confirmarPagamento" parameters: - name: "id" in: "path" required: true type: "integer" format: "int32" responses: default: description: "successful operation" delete: operationId: "cancelarPagamento" parameters: - name: "id" in: "path" required: true type: "integer" format: "int32" responses: default: description: "successful operation" definitions: Transacao: type: "object" properties: numero: type: "string" titular: type: "string" data: type: "string" format: "date" valor: type: "number" Pagamento: type: "object" properties: id: type: "integer" format: "int32" status: type: "string" valor: type: "number" \[/code\]

Bem parecido com o que fizemos no post anterior.

## Corrigindo alguns detalhes

Há algumas diferenças entre o YAML modelado para a nossa API no post anterior e o YAML gerado pelo Swagger a partir de `PagamentoResource`.

Para o POST em `/pagamentos`, as diferenças mais relevantes são:

- Não há uma descrição (summary)
- Sumiram os content-types recebidos e enviados
- O response ficou com uma descrição genérica, sem o cabeçalho de `Location` nem o status `201`
- O parâmetro está não obrigatório e com um nome ruim (`body`)

Para definir uma descrição e content-types para o POST em `/pagamentos`, devemos utilizar a anotação `@ApiOperation`. Para o status e cabeçalho no response, usamos as anotações `@ApiResponses`, `@ApiResponse` e `@ResponseHeader`. Para modificar a obrigatoriedade e nome do parâmetro, usamos `@ApiParam`. \[code language="java" highlight="1-14,18-21"\] @ApiOperation( value = "Cria novo pagamento", consumes = MediaType.APPLICATION\_JSON, produces = MediaType.APPLICATION\_JSON) @ApiResponses( @ApiResponse( code=201, message="Novo pagamento criado", response = Pagamento.class, responseHeaders= @ResponseHeader( name="Location", description="uri do novo pagamento", response=String.class))) @POST @Consumes(MediaType.APPLICATION\_JSON) public Response criarPagamento( @ApiParam( value="Transação", name="transacao", required=true) Transacao transacao) throws URISyntaxException { //código omitido... } \[/code\]

Após essas configurações, o trecho do POST no YAML passaria a ser gerado assim: \[code language="ruby" highlight="3,6-9,12-14,18-25"\] /pagamentos: post: summary: "Cria novo pagamento" description: "" operationId: "criarPagamento" consumes: - "application/json" produces: - "application/json" parameters: - in: "body" name: "transacao" description: "Transação" required: true schema: $ref: "#/definitions/Transacao" responses: 201: description: "Novo pagamento criado" schema: $ref: "#/definitions/Pagamento" headers: Location: type: "string" description: "uri do novo pagamento" \[/code\]

Bem parecido com o que tinhamos antes!

As diferenças mais importantes para o PUT e o DELETE são as seguintes:

- Não há o status `200` no response
- O path parameter está duplicado

Podemos usar a anotação `@ApiResponse` para melhorar as informações do response. Infelizmente, não há como evitar a duplicação do parâmetro `id`.

\[code language="java" highlight="1-5,13-17"\] @ApiResponses( @ApiResponse( code=200, message="Pagamento confirmado", response = Pagamento.class)) @PUT @Path("/{id}") @Produces(MediaType.APPLICATION\_JSON) public Response confirmarPagamento(@PathParam("id") Integer pagamentoId) { //código omitido... }

@ApiResponses( @ApiResponse( code=200, message="Pagamento cancelado", response = Pagamento.class)) @DELETE @Path("/{id}") @Produces(MediaType.APPLICATION\_JSON) public Response cancelarPagamento(@PathParam("id") Integer pagamentoId) { //código omitido... } \[/code\]

O trecho correspondente do YAML ficaria:

\[code language="ruby" highlight="11-14,24-27"\] /pagamentos/{id}: put: operationId: "confirmarPagamento" parameters: - name: "id" in: "path" required: true type: "integer" format: "int32" responses: 200: description: "Pagamento confirmado" schema: $ref: "#/definitions/Pagamento" delete: operationId: "cancelarPagamento" parameters: - name: "id" in: "path" required: true type: "integer" format: "int32" responses: 200: description: "Pagamento cancelado" schema: $ref: "#/definitions/Pagamento" \[/code\]

## Documentação para humanos

Temos o JSON e o YAML, mas esses formatos não são legíveis para pessoas. São quase tão difíceis de ler quanto um WSDL.

Em Web Services do estilo SOAP, é bastante comum que sejam disponibilizados PDFs que focam em descrever a API para humanos. É o caso dos [Correios](http://www.correios.com.br/para-voce/correios-de-a-a-z/pdf/calculador-remoto-de-precos-e-prazos/manual-de-implementacao-do-calculo-remoto-de-precos-e-prazos) e da [Nota Fiscal Paulista](http://www.nfp.fazenda.sp.gov.br/pdf/webservice.pdf), por exemplo.

Será que precisamos escrever um PDF manualmente? Seria interessante alguma maneira de transformar esse JSON ou YAML em uma página HTML com boa usabilidade.

Para isso, há o projeto **Swagger UI**! Na [página da ferramenta](https://github.com/swagger-api/swagger-ui/releases), podemos baixar um zip com o último release. Depois de extrair, basta copiar o conteúdo da pasta `dist` para uma pasta `doc` no _document root folder_ (WebContent ou webapp) do nosso projeto.

Então, a página da documentação estaria acessível em: [http://localhost:8080/fj36-payfast/doc/](http://swagger-payfast.7e14.starter-us-west-2.openshiftapps.com/doc/)

O estranho é que a documentação exibida não seria da nossa API, mas de uma tal de PetStore. É um exemplo que vem configurado no Swagger UI.

Precisamos modificar o seguinte trecho de JavaScript da página `index.html`, corrigindo a URL. \[code language="javascript"\] url = "http://petstore.swagger.io/v2/swagger.json"; \[/code\]

Devemos mudar para: \[code language="javascript"\] url = "../v1/swagger.json"; \[/code\]

Ao acessar novamente a página, teríamos uma documentação razoavelmente legível: ![Exemplo de Swagger UI para a Payfast API](https://blog.caelum.com.br/wp-content/uploads/2016/02/swagger-ui.png)

O Swagger UI, através de um código JavaScript, transforma o swagger.json nessa documentação. É possível disparar requisições de teste com diferentes content-type. É possível utilizar autorização, oauth, entre várias outras funcionalidades.

O código desse post pode ser encontrado em: [https://github.com/alexandreaquiles/fj36-payfast-swagger](https://github.com/alexandreaquiles/fj36-payfast-swagger)

E você? Já usou o Swagger em algum projeto para documentar uma API existente, no estilo Contract-Last? Conte-nos como foi a experiência!
