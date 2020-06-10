---
title: "Buscas Eficientes com Elasticsearch"
date: "2017-06-27"
author: "jopss.sossoloti"
authorEmail: "jopss.sossoloti@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Com o aumento do trafego e armazenamento de dados nas empresas as soluções voltadas para BigData são os atrativos do momento. Falando sobre NoSQL, existem várias formas e tecnologias para implementá-las, e aqui, vamos dar uma olhada no tão comentado Elasticsearch.

A ideia do Elasticsearch é que alem de armazenar os dados de forma não relacional, ele prove uma infra interna muito boa para retornar buscas muito pesadas. Por ser um motor de pesquisa textual altamente escalável, permite armazenar e analisar grandes volumes de informações praticamente em tempo real (eles mesmos usam o termo _near real time_).

Para instalá-lo, é muito simples, basta seguir o tutorial do próprio site deles. Na verdade, [os tutoriais e API](https://www.elastic.co/guide/en/elasticsearch/reference/current/_exploring_your_cluster.html) (em inglês) são muito simples e fácil de ler, tirando quase todas as dúvidas sobre a tecnologia. No entanto, vamos comentar aqui alguns passos e detalhá-los.

Em ambiente Unix, vamos baixá-lo e já estamos pronto para uso.

\[code language="bash"\] curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.4.1.tar.gz tar -zxvf elasticsearch-5.4.1.tar.gz \[/code\]

E para executá-lo:

\[code language="bash"\] cd elasticsearch-5.4.1/bin ./elasticsearch -Ecluster.name=search-cluster-exemplo -Enode.name=search-node-exemplo \[/code\]

Isso irá subir o servidor Elasticsearch, deixar pronto para acesso na porta 9200 e apto a receber consultas via Rest. Ao bater no navegador a url raiz [http://127.0.0.1:9200](http://127.0.0.1:9200), temos o resultado abaixo, indicando que esta no ar e com detalhes sobre a versão.

\[code language="javascript"\] { "name" : "search-node-exemplo", "cluster\_name" : "search-cluster-exemplo", "cluster\_uuid" : "oXT9j3sSTiy-fc8mKT-bBg", "version" : { "number" : "5.4.1", "build\_hash" : "2cfe0df", "build\_date" : "2017-05-29T16:05:51.443Z", "build\_snapshot" : false, "lucene\_version" : "6.5.1" }, "tagline" : "You Know, for Search" } \[/code\]

#### Cluster, Node e Index

No primeiro comando que executamos, você deve ter notado que passamos os parâmetros **cluster.name** e **node.name**. Eles não são parâmetros obrigatórios, mas é importante criar os nomes de cada um deles para sabermos o que estamos criando. Vamos então definir rapidamente os conceitos internos do Elasticsearch. Irei aqui subdividi-los em dois tipo.

Configuração:

1. **Clusters**: a estrutura do topo, serve para agrupamento dos nós.
2. **Nodes**: entidade servidora. Agrupa todos os índices de dados.

Dados:

1. **Index**: categoriza os dados da aplicação e agrupa os tipos.
2. **Types**: subcategoria dos dados, organiza e agrupa os documentos.
3. **Documents**: JSON com os dados crus gravados. Um documento esta fisicamente dentro de um Type.

Imagine a arquitetura de árvore de dados, onde "o topo" são os Cluster e "as folhas" são os Documents. Veja que falamos no plural, porque o Elasticsearch tem o conceito clusterização vertical. Ele foi justamente feito para ser bem simples de gerenciar esses clusters e nós, de forma que a qualquer momento pode-se aumentar ou diminuir eles, sem nenhuma perda de dados. Pela url [http://127.0.0.1:9200/\_cat/health?v](http://127.0.0.1:9200/_cat/health?v), podemos verificar o estado no nosso cluster, com contagem de nós e indices.

Inicialmente não temos nenhum Index criado por padrão, tanto que caso tentamos listar os indices pela url [http://127.0.0.1:9200/\_cat/indices?v](http://127.0.0.1:9200/_cat/indices?v), não temos nenhum resultado gerado. Mas, criar um indice é muito simples, basta criar um PUT para o seu node que ele irá criá-lo sem dificuldades. Em nossos exemplos, irei utilizar o comando cURL do linux. Com o Elasticsearch de pé, faremos no terminal o seguinte comando:

\[code language="bash"\] curl -X PUT http://127.0.0.1:9200/produtos \[/code\]

Isso vai criar um Index chamado _produtos_. Dentro dele, podemos adicionar os Types e os Documents, e esses dois serão adicionados ao mesmo tempo por um segundo comando PUT. Para fins de visualização, vou quebrar o conteúdo JSON em linhas, mas caso for executar, deixe sem quebras.

\[code language="bash"\] curl -X PUT http://127.0.0.1:9200/produtos/eletronicos/1 -d '{ "descricao": "Liquidificador Potente", "marca": "Chinesa", "preco": "230.0" }' \[/code\]

Isso vai criar o Type **eletronicos** dentro do Index **produtos**, com o **id 1** e os dados indicados pelo JSON. Esse comando pode ser usado tanto para inserir quanto para atualizar um dado no Elasticsearch. Caso queira deletar esse dado, basta mudar o verbo HTTP para delete, sem passar nenhum dado interno.

\[code language="bash"\] curl -X DELETE http://127.0.0.1:9200/produtos/eletronicos/1 \[/code\]

#### Consultas

As consultas são o grande trunfo do Elasticsearch. Podemos fazer vários tipos de agrupamentos e organizações com retorno muito rápido. Internamente, ele mantém os dados em cache para deixar o resultado ainda mais performático. As consultas são feitos por verbo HTTP GET passando os parâmetros desejados. Pegando o exemplo de dado criado anteriormente, caso queira consultar pelo nome do produto, podemos fazer de duas formas: direto como parâmetro GET da URL ou criando um corpo JSON interno. A segunda opção garante melhor visualização e muito mais opções de uso.

Pela URL GET:

\[code language="bash"\] curl http://127.0.0.1:9200/produtos/\_search?pretty&q=descricao:liquid \[/code\]

Pelo JSON interno:

\[code language="bash"\] curl http://localhost:9200/produtos/\_search?pretty -d '{ "query": { "match": { "descricao": "liquid" } } }' \[/code\]

Note que usamos um parâmetro chamado pretty. Ele serve somente para melhorar o retorno dos dados de forma tabulada. Existem alguns tipos diferentes de seletores match, além de agrupadores, ordenadores, paginação e outros formatos de consultas. Ele faz tudo o que uma base relacional e um pouco mais além com suas opções de reduções.

#### E na realidade?

Vamos tentar analisar um exemplo mais real. Vamos simular um banco de dados relacional PostgreSQL e, como a mesma quantidade de dados, analisar o Elasticsearch. Vamos pegar como base de uma tabela de Pessoa, contendo:

- ID.
- Data de criação.
- Nome.
- Tipo de pessoa.

Inserimos nos dois bancos 1.466.485 registros aleatórios de pessoas e vamos analisar os resultados de consultas simples.

**PostgresSQL**

\[code language="bash"\] select \* from pessoa where nome like '%a%' order by nome, tipopessoa; -- Quantidade de resultados: 602.187 -- Tempo: 1m 6s, independente de quantidade de vezes consultadas.

select \* from pessoa order by nome, tipopessoa; -- Quantidade de resultados: 1.466.485 -- Tempo: 2m 37s, independente de quantidade de vezes consultadas. \[/code\]

**Elasticsearch**

\[code language="bash"\] curl http://localhost:9200/pessoas\_idx/\_search?pretty -d '{"query": { "match": { "nome": "a" }}, "sort": \[{ "nome": "asc", "tipopessoa": "asc" }\] }' -- Quantidade de resultados: 602.187 -- Tempo: 1.2s no primeiro acesso, depois media de 10 milesegundos

curl http://localhost:9200/pessoas\_idx/\_search?pretty -d '{"query": { "match\_all": {} }, "sort": \[{ "nome": "asc", "tipopessoa": "asc" }\] }' -- Quantidade de resultados: 1.466.485 -- Tempo: 1.9s no primeiro acesso, depois media de 12 milesegundos \[/code\]

Como esperado, claro, o Elasticsearch foi muito mais rápido. As estruturas internas muito diferentes e o cacheamento automático do Elastic fazem as consultas ficarem absurdamente mais rápidas a partir do segundo acesso.

Um detalhe bem importante. Fizemos no Elasticsearch uma consulta com ordenador por um atributo textual. Por padrão, ordenadores e agregadores neste tipo de campo não são possíveis por questões de performance. Assim, temos que habilitar os campos textuais a ser um "fielddata". Para o nosso exemplo, seria o comando abaixo, antes de executar as consultas.

\[code language="bash"\] curl -X PUT http://localhost:9200/pessoas\_idx/\_mapping/pessoas\_type?pretty -d '{"properties": {"nome": {"type": "text","fielddata": true}, "tipopessoa": {"type": "text","fielddata": true}}}' \[/code\]

A comparativa entre os bancos não nos diz que relacional ou o PostgreSQL não deva ser usado. Pelo contrário! Bancos NoSQL possuem seus defeitos, como não garantir integridade relacional entre os dados. Assim, relacional e NoSQL devem ser usados ao seu propósito. Vamos a um por exemplo:

- Em uma aplicação onde os dados devem ter integridades relacionais, possuem um banco de dados padrão como PostgreSQL.
- Mas, há uma funcionalidade que precisa de feedback muito rápido na Home do sistema, como uma daquelas caixas de texto com autocomplete automático sugerido: o usuário ao digitar o nome dos produtos, o sistema vai filtrando e exibindo o resultado para pesquisa.

Fazer isso em um banco relacional ou ficará lento, ou necessitará de ajustes finos, com configurações especificas no SQL ou mesmo um cache interno na própria aplicação. O que poderia ser melhor arquitetado é atrelar somente essa funcionalidade ao Elasticsearch, e o restante da aplicação, continuar a ser banco relacional PostgreSQL.

Mas, você pode estar se perguntando como os dados dos produtos estarão no Elasticsearch, já que a base principal é um relacional? Existem configurações que automatizam o Elasticsearch a sincronizar os dados com outras bases, relacionais ou não. Para isso há a ferramenta chamada [LogStash](https://www.elastic.co/products/logstash), que pode ser configurado a suportar sincronização automática com o PostgreSQL. Dessa forma, o programador se preocupa em gravar os produtos no relacional, e deixa a cargo do LogStash em puxar os dados para o Elastic. Além do mais, essa configuração é muito simples, basicamente editar um arquivo de configuração contendo os dados de acesso a base relacional e para qual Node ele sincronizará os dados.

Outra vantagem do Elastisearch é a ferramenta visual chamada [Kibana](https://www.elastic.co/products/kibana). Ele mostra uma análise de dados com gráficos automáticos. Inclusive pode-se usar o Kibana para executar os scripts de query do Elastisearch sem precisar ir ao terminal Linux.

Caso queiram tentar simular as consultas deste post, os scripts estão [neste GitHub](https://github.com/jopss/elasticsearch-exemplo).

#### O que tem mais?

Você notou que ao subir o Elasticsearch ele disponibiliza várias URL Rest para gerenciamento dos dados. No entanto, você pose usá-lo em sua linguagem de programação preferida por meio de suas APIs, como por exemplo, a [Java API](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-api.html). Elas dispõem exatamente as mesmas features das URL Rest, mas você programa como uma DSL.

Outro ponto interessante são os servidores Cloud já configurados e prontos para uso. A [AmazonAWS](https://aws.amazon.com/pt/elasticsearch-service/), o [Google Platform](https://console.cloud.google.com/launcher/details/click-to-deploy-images/elasticsearch) e o próprio [Elastic Cloud](https://www.elastic.co/cloud) possuem ótimas ferramentas Cloud, mas pagas. Se você pode investir nessa infra, vale muito a pena.

Sobre as consultas, o Elasticsearch tem conceitos de paralelismo e consultas múltiplas. Caso queira, dê uma olhada [na documentação](https://www.elastic.co/guide/en/elasticsearch/reference/current/_batch_processing.html) sobre isso.

Por fim, passamos aqui bem por cima nos conceitos do Elasticseach, mas existem muita coisa boa para minerar dados, até mesmo usando Machine Learning.

E você, esta utilizando alguma forma de busca não relacional? Quais ferramentas BigData estão usando? Não deixe de conferir nossa [Trilha de Infraestrutura no Alura](https://www.alura.com.br/cursos-online-infraestrutura).
