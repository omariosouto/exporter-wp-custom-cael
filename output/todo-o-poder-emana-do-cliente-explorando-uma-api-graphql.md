---
title: "Todo o poder emana do cliente: explorando uma API GraphQL"
date: "2017-09-13"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

foto por [Elena](https://www.flickr.com/photos/101692226@N05/32876263933/in/photolist-S6arSM-WBiENk-9uo8m4-7Pzo8k-ptuqCS-4vhPpf-7zufg6-91EGCR-X6do8f-8JS2ym-bxFSw4-pEqMbh-bmDGsu-dH6X8n-cAeaQf-5zMyPD-6WCzeH-ap7F3B-d3G3R-bMos6F-7Yuj4B-5ocki-aqTNtC-aqR9dg-49tFFy-fNc5aQ-9FRvrf-mnrvP-cnYW2C-8SnZHY-5kQwHN-6h1goW-87v6wV-dDVrX2-apaqqh-iKa1S-47otDV-4VvZQ9-7PDnuf-kRkpz-aLNbox-8SnX55-8SjU1R-8SjTot-dgzJnE-aqR9Bi-3CBVWN-8SnXAq-8SnYbu-7GNHa5)

Esse tal de GraphQL tem causado bastante burburinho. Dizem que é uma alternativa mais flexível e eficiente a APIs REST. Já foi detectado pelo seu radar?

No [episódio 55](http://hipsters.tech/startups-processos-e-mercado-global-hipsters-55/) do Hipsters Ponto Tech, o pessoal da [Pipefy](http://docs.pipefy.apiary.io) disse que uma API GraphQL no back-end, junto ao React e shadow DOM no front-end, levaram a uma melhoria drástica na performance. Outras empresas brasileiras como a [GetNinjas](https://labs.getninjas.com.br/compartilhando-dados-em-uma-arquitetura-de-microsservicos-usando-graphql-35a5aca4a7dc) e a [Taller](http://blog.taller.net.br/graphql-hoje-usando-apollo-em-aplicacoes-que-utilizam-apis-rest/) também tem usado o GraphQL.

Empresas gringas como a [Shopify](https://help.shopify.com/api/storefront-api/graphql-explorer), [Artsy](https://artsy.github.io/blog/2016/06/19/graphql-for-mobile/) e [Yelp](https://www.yelp.com/developers/graphql/guides/intro) provêem APIs com suporte ao GraphQL. O GitHub [migrou](https://githubengineering.com/the-github-graphql-api/) sua API de REST para GraphQL.

E claro, o Facebook, que desenvolveu o GraphQL em 2012 para uso interno e o [abriu ao público](https://code.facebook.com/posts/1691455094417024) em 2015.

## Quais as limitações de uma API REST?

Para ilustrar o que pode ser melhorado em uma API REST, vamos utilizar a versão 3 da API do GitHub, considerada muito consistente e aderente aos princípios REST.

Queremos uma maneira de avaliar bibliotecas open-source. Para isso, dado um repositório do GitHub, desejamos descobrir:

- o número de _stars_
- o número de _pull requests_ abertos

Como exemplo, vamos usar o repositório de uma biblioteca NodeJS muito usada: o framework Web minimalista [Express](https://blog.caelum.com.br/como-criar-um-servidor-http-com-expressjs/).

### Obtendo detalhes de um repositório

Lendo a documentação da API do GitHub, descobrimos que para [obter detalhes sobre um repositório](https://developer.github.com/v3/repos/#get), devemos enviar uma requisição `GET` para `/repos/:owner/:repo`. Então, para o repositório do Express, devemos fazer:

```http

GET https://api.github.com/repos/expressjs/express
```

Como resposta, obtemos:

- 2.2 KB _gzipados_ transferidos, incluindo cabeçalhos
- 6.1 KB de JSON em 110 linhas, quando descompactado

```http

200 OK
Content-type: application/json; charset=utf-8
```

```json

{
    "id": 237159,
    "name": "express",
    "full_name": "expressjs/express",
    "private": false,
    "html_url": "https://github.com/expressjs/express",
    "description": "Fast, unopinionated, minimalist web framework for node.",
    "fork": false,
    "issues_url": "https://api.github.com/repos/expressjs/express/issues{/number}",
    "pulls_url": "https://api.github.com/repos/expressjs/express/pulls{/number}",
    "stargazers_count": 33508,
    ... 
}
```

O JSON retornado tem diversas informações sobre o repositório do Express. Por meio da propriedade `stargazers_count`, descobrimos que há mais de 33 mil stars.

Porém, **não** temos o número de pull requests abertos.

### Obtendo os pull requests de um repositório

Na propriedade `pulls_url`, temos apenas uma URL: `https://api.github.com/repos/expressjs/express/pulls{/number}`.

Um bom palpite é que sem esse `{/number}` teremos a lista de todos os pull requests, o que pode ser confirmado na [seção de pull requests](https://developer.github.com/v3/pulls/#list-pull-requests) da documentação da API REST do GitHub.

> _O `{/number}` da URL segue o modelo proposto pela [RFC 6570 (URI Template)](https://tools.ietf.org/html/rfc6570)._

Mas como filtrar apenas pelos pull requests abertos?

Na mesma documentação, verificamos que podemos usar a URL `/repos/:owner/:repo/pulls?state=open` ou simplesmente `/repos/:owner/:repo/pulls`, já que o filtro por pull requests abertos é aplicado por padrão. Em outras palavras, precisamos de outra requisição:

```http

GET https://api.github.com/repos/expressjs/express/pulls
```

A resposta é:

- 54.1 KB _gzipados_ transferidos, incluindo cabeçalhos
- 514 KB de JSON em 9150 linhas, quando descompactado

```http

200 OK
Content-type: application/json; charset=utf-8
Link: <https://api.github.com/repositories/237159/pulls?page=2>; rel="next",
      <https://api.github.com/repositories/237159/pulls?page=2>; rel="last"
```

```json

[
    {
        //um pull request...
        "url": "https://api.github.com/repos/expressjs/express/pulls/3391",
        "id": 134639441,
        "html_url": "https://github.com/expressjs/express/pull/3391",
        "diff_url": "https://github.com/expressjs/express/pull/3391.diff",
        "patch_url": "https://github.com/expressjs/express/pull/3391.patch",
        "issue_url": "https://api.github.com/repos/expressjs/express/issues/3391",
        "number": 3391,
        "state": "open",
        "locked": false,
        "title": "Update guide to ES6",
        "user": {
            "login": "jevtovich",
            "id": 13847095,
            "avatar_url": "https://avatars3.githubusercontent.com/u/13847095?v=4",
            ...
        },
        "body": "",
        "created_at": "2017-08-08T11:40:32Z",
        "updated_at": "2017-08-08T17:28:01Z",
        ...
    },
    {
        //outro pull request...
        "url": "https://api.github.com/repos/expressjs/express/pulls/3390",
        "id": 134634529,
        ...
    },
    ...
]
```

É retornado um array de 30 objetos que representam os pull requests. Cada objeto ocupa uma média de 300 linhas, com informações sobre status, descrição, autores, commits e _diversas_ URLs relacionadas.

Disso tudo, só queremos saber a contagem: 30 pull requests. Não precisamos de **nenhuma** outra informação.

Mas há outra questão: o resultado é paginado com 30 resultados por página, por padrão, conforme descrito [na seção de paginação](https://developer.github.com/v3/#pagination) da documentação da API REST do GitHub.

As URLs das próximas páginas devem ser obtidas a partir do cabeçalho de resposta `Link`, extraindo o `rel` (_link relation_) `next`.

> _Os links para as próximas páginas seguem o conceito de [hipermídia](https://blog.caelum.com.br/hipermidia-e-contratos-dinamicos-menor-acoplamento/) do REST e foram implementados usando o cabeçalho Link e o formato descrito na [RFC 5988 (Web Linking)](https://tools.ietf.org/html/rfc5988). Essa RFC sugere um punhado de link relations padronizados._

Então, a partir do `next`, seguimos para a próxima página:

```http

GET https://api.github.com/repositories/237159/pulls?page=2
```

Temos como resposta:

- 26.9 KB _gzipados_ transferidos, incluindo cabeçalhos
- 248 KB de JSON em 4394 linhas, quando descompactado

```http

200 OK
Content-type: application/json; charset=utf-8
Link: <https://api.github.com/repositories/237159/pulls?page=1>; rel="first",
      <https://api.github.com/repositories/237159/pulls?page=1>; rel="prev"
```

```json

[
  {
    //um pull request...
    "url": "https://api.github.com/repos/expressjs/express/pulls/2730",
    "id": 41965836,
    ...
  },
  {
    //outro pull request...
    "url": "https://api.github.com/repos/expressjs/express/pulls/2703",
    "id": 39735937,
     ...
  },
  ...
]
```

O array retornado contabiliza mais 14 objetos representando os pull requests. Dessa vez, não há o link relation `next`, indicando que é a última página.

Então, sabemos que há 44 (30 + 14) pull requests abertos no repositório do Express.

### Resumindo

No momento da escrita desse artigo, o número de stars do Express no GitHub é 33508 e o de pull requests abertos é 44. Para descobrir isso, tivemos que:

- disparar 3 requisições ao servidor
- baixar 83.2 KB de informações _gzipadas_ e cabeçalhos
- fazer parse de 768.1 KB de JSON ou 13654 linhas

O que daria pra melhorar? Ir menos vezes ao servidor, baixando menos dados!

Não é um problema com o REST em si, mas uma discrepância entre a modelagem atual da API e as nossas necessidades.

Poderíamos pedir para o GitHub implementar um recurso específico que retornasse somente as informações, tudo em apenas um request.

Mas será que o pessoal do GitHub vai nos atender?

## Mais flexibilidade e eficiência com GraphQL

Numa API GraphQL, o cliente diz exatamente os dados que quer da API, tornando a requisição muito **flexível**.

A API, por sua vez, retorna apenas os dados que o cliente pediu, fazendo com que a transferência da resposta seja bastante **eficiente**.

### Mas afinal de contas, o que é GraphQL?

GraphQL _não_ é um banco de dados, _não_ é um substituto do SQL, _não_ é uma ferramenta do lado do servidor e _não_ é específico para React (apesar de muito usado por essa comunidade).

Um servidor que aceita requisições GraphQL poderia ser implementado em _qualquer_ linguagem usando _qualquer_ banco de dados. Há várias [bibliotecas](http://graphql.org/code/#server-libraries) de diferentes plataformas que ajudam a implementar esse servidor.

Clientes que enviam requisições GraphQL também poderiam ser implementados em _qualquer_ tecnologia: web, mobile, desktop, etc. Diversas [bibliotecas](http://graphql.org/code/#graphql-clients) auxiliam nessa tarefa.

GraphQL é uma **query language para APIs** que foi [especificada](https://facebook.github.io/graphql/) pelo Facebook.

A _query language_ do GraphQL é **fortemente tipada** e descreve, através de um _schema_, o modelo de dados oferecido pelo serviço. Esse schema pode ser usado para verificar se uma dada requisição é válida e, caso seja, executar as tarefas no back-end e estruturar os dados da resposta.

Um cliente pode enviar 3 tipos de requisições GraphQL, os _root types_:

- [query](https://facebook.github.io/graphql/#sec-Query), para consultas;
- [mutation](https://facebook.github.io/graphql/#sec-Mutation), para enviar dados;
- [subscription](https://facebook.github.io/graphql/#sec-Subscription), para comunicação baseada em eventos.

### Montando uma consulta GraphQL

A versão 4 da API do GitHub, a mais recente, dá suporte a requisições GraphQL.

Para fazer nossa consulta às stars e aos pull requests abertos do repositório do Express usando a API GraphQL do GitHub, devemos começar com a [`query`](https://developer.github.com/v4/reference/query/):

```graphql

query {
}
```

Vamos usar o campo `repository` da `query`, que recebe os argumentos `owner` e `name`, ambos obrigatórios e do tipo String. Para buscar pelo Express, devemos fazer:

```graphql

query {
  repository (owner: "expressjs", name: "express") {
  }
}
```

A partir do objeto [`repository`](https://developer.github.com/v4/reference/object/repository/), podemos descobrir o número de stars por meio do campo `stargazers`. Como queremos apenas a quantidade de itens, só precisamos obter propriedade `totalCount` dessa [_connection_](https://developer.github.com/v4/reference/object/stargazerconnection/).

```graphql

query {
  repository (owner: "expressjs", name: "express") {
    stargazers {
      totalCount
    }
  }
}
```

Para encontrarmos o número de pull requests abertos, basta usarmos o campo `pullRequests` do `repository`. O filtro por pull requests abertos não é aplicado por padrão. Por isso, usaremos o argumento `states`. Da [connection](https://developer.github.com/v4/reference/object/pullrequestconnection/), obteremos apenas o `totalCount`.

```graphql

query { 
  repository(owner: "expressjs", name: "express") {
    stargazers {
      totalCount
    }
    pullRequests(states: OPEN) {
      totalCount
    }
  } 
}
```

Basicamente, é essa a nossa consulta! Bacana, não?

Uma maneira de "rascunhar" consultas GraphQL é usar a ferramenta [GraphiQL](https://github.com/graphql/graphiql), que permite explorar APIs pelo navegador. Há até _code completion_! Boa parte das APIs GraphQL dá suporte, incluindo [a do GitHub](https://developer.github.com/v4/explorer/).

### Tá, mas como enviar a consulta para a API?

A maneira mais comum de publicar APIs GraphQL é usar a boa e velha Web, com seu protocolo HTTP.

> _Apesar do HTTP ser o mais usado para publicar APIs GraphQL, teoricamente não há limitações em usar outros protocolos._

Uma API GraphQL possui apenas um _endpoint_ e, consequentemente, só uma URL.

É possível enviar requisições GraphQL usando o método `GET` do HTTP, com a consulta como um parâmetro na URL. Porém, como as consultas são relativamente grandes e requisições `GET` tem um [limite de tamanho](https://stackoverflow.com/a/417184), o método mais utilizado pelas APIs GraphQL é o `POST`, com a consulta no corpo da requisição.

No caso do GitHub a URL do endpoint GraphQL é: `https://api.github.com/graphql`

O GitHub só dá suporte ao método `POST` e o corpo da requisição deve ser um JSON cuja propriedade `query` conterá uma String com a nossa consulta.

Mesmo para consultas a repositórios públicos, a API GraphQL do GitHub precisa de um [token de autorização](https://github.com/settings/tokens).

```http

POST https://api.github.com/graphql
Content-type: application/json
Authorization: bearer f023615deb415e...
```

```json

{
"query":    "query {
                repository(owner: \"expressjs\", name: \"express\") { 
                    stargazers {
                        totalCount
                    } 
                    pullRequests(states: OPEN) {
                        totalCount
                    }
                }
            }"
}
```

O retorno será um JSON em que os dados estarão na propriedade `data`:

```json

{
    "data": {
        "repository": {
            "stargazers": {
                "totalCount": 33508
            },
            "pullRequests": {
                "totalCount": 44
            }
        }
    }
}
```

> _Na verdade, os JSONs de requisição e resposta ficam em apenas 1 linha. Formatamos o código anterior em várias linhas para melhor legibilidade._

Repare que os campos da consulta, dentro da `query`, tem exatamente a mesma estrutura do retorno da API. É como se a resposta fosse a própria consulta, mas com os valores preenchidos. Por isso, montar consultas com GraphQL é razoavelmente **intuitivo**.

### Resumindo

Obtivemos os mesmos resultados: 33508 stars e 44 pull requests. Para isso, tivemos que:

- disparar apenas 1 requisição ao servidor
- baixar somente 996 bytes de informações _gzipadas_, incluindo cabeçalhos
- fazer parse só de 93 bytes de JSON

São 66,67% requisições a menos, 98,82% menos dados e cabeçalhos trafegados e 99,99% menos JSON a ser "parseado". Ou seja, **MUITO mais rápido**.

## Considerações finais

Poderíamos buscar outros dados da API do GitHub: o número de _issues_ abertas, a data da última release, informações sobre o último commit, etc.

Uma coisa é certa: com [uma consulta GraphQL](https://gist.github.com/alexandreaquiles/1367baba2e3b803ed8604950fdc039bb), eu faria menos requisições e receberia menos dados desnecessários. Mais flexibilidade e mais eficiência.

Porém, existem várias outras questões que surgem ao estudar o GraphQL:

- como fazer um servidor que atenda a toda essa flexibilidade?
- é possível gerar uma documentação a partir do código para a minha API?
- vale a pena migrar minha API pra GraphQL?
- posso fazer uma "casca" GraphQL para uma API REST já existente?
- como implementar um cliente sem muito trabalho?
- quais os pontos ruins dessa tecnologia e desafios na implementação?

E você? O que achou do GraphQL? Já usa? Usaria?
