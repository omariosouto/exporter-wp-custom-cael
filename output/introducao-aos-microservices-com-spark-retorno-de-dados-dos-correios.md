---
title: "Introdução aos microservices com Spark: retorno de dados dos correios"
date: "2016-10-26"
author: "jopss.sossoloti"
authorEmail: "jopss.sossoloti@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

A arquitetura dos projetos tem sofrido uma tremulação na força com o advento da organização em microserviços. Já discutimos bastante sobre [os conceitos de microserviços](https://blog.caelum.com.br/arquitetura-de-microservicos-ou-monolitica/), o Raphael nos mostrou [como montar um bom projeto](https://blog.caelum.com.br/conheca-o-kumuluzee-seu-novo-framework-para-microservices/) e depois falamos como [melhorar a forma de publicação em um servidor de aplicação](https://blog.caelum.com.br/micro-profile-javaee-com-wildfly-swarm/).

Várias teorias e casos de sucessos já são um tanto conhecidas [em artigos e vídeos de apresentações](https://www.infoq.com/br/search.action?queryString=Microservicos&page=1&searchOrder=&sst=4g3FT8jokisP9zzr). Estamos recebendo um bom tanto de informações sobre os benefícios de organizar as nossas aplicações em microserviços. O que quero mostrar aqui é um caso bem simples e palpável que utilizei em um projeto que está em produção.

Um dos problemas quando tem-se um _hype_ sobre uma técnica é que podemos estar fadado ao uso em demasiado dessa técnica, mesmo em casos que não faça sentido. Sou do ponto de vista que partir desde o começo para uso de microserviço em um projeto pode ser um problema. O monolito [pode ser uma boa estratégia inicial](http://martinfowler.com/bliki/MonolithFirst.html) e migrar microservicos que façam sentido depois (claro que tem [discussões sobre isso](http://martinfowler.com/articles/dont-start-monolith.html)). Foi a estratégia que seguimos.

Criamos a app web monolítica e estava encaminhando bem, até que os _stackholders_ decidiram iniciar outro projeto, menor, mas em paralelo com o atual. Reorganizamos a equipe e começamos o novo projeto. Havia algumas semelhanças entre os dois, e uma delas, é que ambos necessitam de dados sobre correios.

- Precisamos da listagem de estados.
- Através de um estado, precisamos das cidades.
- Através do IBGE, precisamos da cidade relativa.
- Com um CEP, precisamos dos dados de endereços.

Isso é bem comum em várias aplicações, e poderia fazer sentido para uma terceira que possa aparecer. Com isso, centralizamos em um ponto único, que foi desenhado como um microserviço. Assim, dentro desse projeto contém:

1. Regras, dados e acessos a qualquer domínio sobre Correios.
2. Telas HTML.
3. Banco de dados.
4. Porta de entrada REST com retorno JSON.

Como um microserviço  é parte de um todo (que é o sistema), acredito que deve fazer parte da mesma rede interna em que estão publicados os demais serviços ou projetos. Assim, neste caso, não há acessos externos e questões de segurança foram designados a outros serviços. Ainda, publica-lo tem que ser uma tarefa fácil e rápida.

Dentre as opções de frameworks, escolhemos o Spark Java e a [documentação dele](http://sparkjava.com/) é simples e rápida. O Spark utiliza um Jetty _embedded_ para subir a aplicação, então, bastamos executar a classe Main para deixá-lo no ar. Ele utiliza métodos estaticos para suas configurações, e ainda, usamos Java 8 para deixar mais claro as configurações dos _endpoints_.

Na classe principal executora, podemos configurar as portas e os endpoints.

\[code language="java"\] import static spark.Spark.\*;

public class Main { public static void main(String\[\] args) { port(4567); new Initializer().init(); } } \[/code\]

A classe _Initializer_ possui configurações extras e inicializa os endpoints. Segue um exemplo.

\[code language="java"\] import br.com.jopss.microservico.correios.infra.CorreiosEndpoints; import br.com.jopss.microservico.correios.infra.JsonTransformer; import br.com.jopss.microservico.correios.dominio.ICorreiosService; import static spark.Spark.\*;

public class CorreiosEndpoint extends CorreiosEndpoints {

public void publish() { get("/:versao/correios/ufs", "text/plain", (req, resp)) { ICorreiosService servico = super.getServico(req, ICorreiosService.class); return super.retornar(servico.buscarUfs(), resp); }, new JsonTransformer()); } } \[/code\]

O Spark disponibiliza método estáticos **get** e **post** para adicionarmos as nossas regras. Esses métodos possuem três parâmetros:

1. Uma String contendo a URL.
2. Outra String contendo o Content-Type.
3. Um objeto da interface Route, que possui um único método com Request e Response, e como tal, podemos [usar o Lambda do Java 8](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/).

A indicação que iremos retornar como resultado um JSON é feita pela classe JsonTransformer. Há dois detalhes a serem vistos nesse código acima, que foi feito para o nosso projeto.

Primeiro é a identificação da versão na URL. Essa versão irá verificar internamente qual ação corresponde aquela versão. Assim, podemos ter publicado, para um mesmo _Service_, duas ou três versões distintas. Quando faço

\[code language="java"\] super.getServico(req, ICorreiosService.class) \[/code\]

Internamente ele identifica qual versão daquele serviço será injetado (utilizamos o Spring IoC para isso).

O segundo detalhe é o retorno. A chamada a

\[code language="java"\] super.retornar(servico.buscarUfs(), resp); \[/code\]

Cria o retorno de um objeto único Resposta, a ser encapsulado os dados e mensagens.

Essas configurações são passos extras que pode deixar seu microserviço um pouco mais inteligente. A versão e a resposta única padroniza os dados e as regras, melhorando a manutenção. Podemos por exemplo adicionar um novo campo de resposta ou uma nova versão de regra sem quebrar os acessos já existentes.

O exemplo acima mais detalhado com a configuração total pode ser conferido [no github](https://github.com/jopss/microservico-correios).

1. Clone o projeto: git clone https://github.com/jopss/microservico-correios.git
2. Execute o maven: mvn clean install.
3. Configure a base de dados PostgreSQL ("bd\_correios\_desenv" com o schema "main").
4. Importe na sua IDE e execute a classe Main (como uma aplicação java desktop mesmo).
5. Acesse pelo navegador: http://localhost:4567/
