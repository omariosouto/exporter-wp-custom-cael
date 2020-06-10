---
title: "O que eu vi do QconSP 2018"
date: "2018-05-15"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Nos dias 9, 10 e 11 de maio aconteceu em São Paulo o evento [QconSP - 2018](https://qconsp.com/), reunindo muita gente boa, segmentado em várias tracks e com assuntos completamente variados. A [Caelum ajudou](https://blog.caelum.com.br/tag/qcon/) a trazer o evento para o Brasil lá atrás. Agora, quem está à frente da organização, que a propósito foi excelente, é a [C4Media](https://www.infoq.com/br/about-c4media), com o [Leandro Guimarães](https://twitter.com/leguimas?lang=en) liderando.

Há anos não participava da conferência, mas desde que comecei nessa vida de eventos notei um padrão: todo evento tem uma BuzzWord. Já passei por várias: SOA, TDD, frameworks MVC, injeção de dependência, REST, NoSQL, Machine Learning, Containers. Neste ano acho que definiria duas buzzwords: GraphQL e Microserviços. A primeira, [Alexandre Aquilies já projetou ano passado.](https://blog.caelum.com.br/todo-o-poder-emana-do-cliente-explorando-uma-api-graphql/) Ela foi citada como a responsável por solucionar todos os problemas que o REST trás: documentação, versionamento de API's, quantidade de dados trafegados, quantidade de requisições para se obter a informação desejada. Enfim, muitos problemas, fiquei até triste, pois a última vez que eu tinha ido ao evento, REST era a salvação do mundo, agora é um pesadelo. Já o segundo, creio [não ser mais novidade](https://blog.caelum.com.br/conheca-o-kumuluzee-seu-novo-framework-para-microservices/) para ninguém, haja vista que em [2015 o Adriano](https://blog.caelum.com.br/arquitetura-de-microservicos-ou-monolitica/) já estava falando disso.

Como há inúmeras tracks no evento, este post acaba sendo uma visão bem particular dos meus interesses, o evento [foi bem maior do que](https://twitter.com/search?f=tweets&vertical=default&q=%23qconsp&src=typd) eu conseguiria descrever neste post. 

O evento teve o start com o brasileiro com nome mais gringo possível [Martin Spier](https://twitter.com/spiermar), ﻿que simplesmente arrebentou com duas palestras sobre como monitorar a performance @Netflix. Eu sinceramente não sei se a galera sai triste ou feliz da palestra desse cara. Sabe por quê? Sinceramente? A netflix está em outro nível e o ["brazuca" é fora de série](https://resultadosdigitais.com.br/blog/martin-spier-rd-summit/), tanto na didática, oratória, condução na apresentação, como na escovação de bits.  
Martin trouxe alguns dados da @Netflix que, salvo engano, são mais ou menos esses:  

- 1 milhão de requests por segundo
- 100 milhões de horas de streaming
- 10 milhões de dispositivos
- 10 mil instâncias
- 10 TB/s - 1/3 da banda dos EUA
- [160 bilhões](https://twitter.com/lacerdaph/status/995066408231895040) de eventos por dia
- Scale up e Scale down com tempo de vida média de 36 horas
- \+ 100 microserviços

Enfim, outros muitos dados que eu nem consegui anotar pois nessa hora eu já estava triste! O principal problema dos caras é lidar com uma quantidade imensa de dispositivos (browser, celular, TV's, chromecast e afins), sem depreciá-los, e mantendo sempre o nível de performance para estar presente nos momentos WIN-WIN (aquela hora que você não tem nada pra fazer e escolhe a netflix pra diversão) dos seus clientes.  
Eles conseguem isso distribuindo seu [contéudo por CDN's](https://openconnect.netflix.com/) nos provedores e a parte transacional fica em 3 regiões da AWS (2 EUA e 1 Europa).

Algumas tecnologias que eles usam são: [Zuul](https://github.com/Netflix/zuul), que possui o conhecimento do estado do estado do sistema e atua como uma camada de proxy. [Chaos Monkey](https://github.com/Netflix/chaosmonkey), para trazer literalmente o CAOS para a infraestrutura deles e saber como ela se comporta. A stack é composta por ubuntu, python, node, lua e Java ([muito java](https://dzone.com/articles/most-popular-programming-languages-of-2017)). Por fim, para integração contínua usam  Jenkins, [Spinnaker](https://www.spinnaker.io/), [Automated Canary Analysis](https://medium.com/netflix-techblog/automated-canary-analysis-at-netflix-with-kayenta-3260bc7acc69). O release por meio da [plataforma Kayenta](https://github.com/spinnaker/kayenta) baseia-se em um score, e o sistema de CI decide se aquela aplicação deve ou não ir para produção.  

Toda essa infra é organizada em microserviços stateless e imutáveis, com autoscaling (up and down), usando como métrica sempre descobrir qual é o recurso limitante de cada microserviço.  
Ok, a estrutura é elástica, mas o que fazer nos vales? Ou seja, quando você já pagou pela infra e não está utilizando toda sua capacidade? Bom, eles usam para fazer encoding, tanto de codecs como conteúdo novo.  
  

Duas frases que o Spier disse que me marcaram muito é que não dá para esperar que a ferramenta de terceiro vá funcionar corretamente no seu ambiente, por isso: build your own! Além disso, essa eu até anotei:   

>   
> 
> Todos engenheiros da Netflix possuem acesso de ROOT no servidor de produção! 

Finalizando, ele mostrou como fazer a monitoração dessa infra para resolver uma pergunta simples: "Como descobrir o que está dando problema?". Eles usam basicamente 3 ferramentas/técnicas: [Atlas](https://github.com/Netflix/atlas) como sistema de monitoria central (footprinting), agregando os dados mas NÃO em real time. [Vector](https://github.com/Netflix/vector) para conectar a uma única instância e agregar os dados real time. E, por fim, [FlameScope](https://github.com/Netflix/flamescope) para analisar CPU, Memória, I/O em formato de [flamegraphs.](http://www.brendangregg.com/flamegraphs.html) 

Depois fui para a palestra do mestre [Milfont](https://twitter.com/cmilfont), o cara que mais odeia app nativa do mundo e ama [PWA](https://hipsters.tech/progressive-web-apps-hipsters-03/), e do [Yuri.](https://twitter.com/yuriadams) Eles falaram um pouco dessa evolução maluca no frontend, que a cada hora surge um framework novo que irá solucionar todos os seus problemas. Trouxeram um histórico muito bacana dessa evolução

- 1995 - PHP
- 1999 - Struts e frameworks MVC
- 2004 a 2007 - DOJO
- 2007 a 2010 - Angular, backbone, knockout
- 2011 - Ember
- 2013 - React, Vue, Angular 2\*

Antes a evolução era lenta, com os palms, celulares nokia, IE 6, JavaME e agora há inúmeros dispositivos: como lidar com isso? Um dos problemas principais acaba sendo a quantidade de requisiçoes REST que precisam ser feitas ao backend para preencher os dados na tela e a solução que eles usaram no case da CVC (companhia de turismo) foi o GraphQL. Além disso apontaram o [padrão flux](https://facebook.github.io/flux/docs/overview.html) como solução de arquitetura no front e sua implementações [Ember-Redux](https://github.com/ember-redux/ember-redux), [Vuedeux](https://www.npmjs.com/package/vuedeux) e [React Redux](https://github.com/reduxjs/react-redux).

Com respeito às linguagens, duas me chamaram muita atenção: [Kotlin](https://kotlinlang.org/) e [Rust](https://research.mozilla.org/rust/). A segunda não consegui ver, porém me falaram que o desempenho da linguagem é excelente. A ideia é ter uma linguagem que consiga competir com C. Escolhi então a primeira. Na Caelum, o [Alex](https://blog.caelum.com.br/como-preparar-o-ambiente-e-escrever-seu-primeiro-codigo-com-kotlin/) já vem [debatendo](https://blog.caelum.com.br/eu-preciso-mesmo-de-uma-data-class/) há um [tempo](https://blog.caelum.com.br/bean-validation-no-kotlin/) sobre a [linguagem](https://medium.com/collabcode/seja-tamb%C3%A9m-um-desenvolvedor-kotlin-770188949486) e os pontos que me chamaram atenção foram:

- Escrita de testes não por nome de método mas sim por texto claro
- o famoso [dataclass](https://blog.caelum.com.br/eu-preciso-mesmo-de-uma-data-class/) com boilerplate menor
- Lambdas mais fáceis que Java
- T.class mesmo que seja do tipo genérico retorna a classe
- Extension methods para código legado

Em suma, em um código que o pessoal da ZUP migrou de Java para Kotlin, foram retiradas 32 mil linhas e adicionadas 3 mil.

Uma outra galera também trouxe o case da CVC que foi migrado para PWA, ressaltando todas as suas vantagens como: não precisa de instalação física, atualização mais fácil, navegação offline e afins. Passaram até um site interessante [WHATWEBCANDO.today](https://whatwebcando.today/) para verificar o que você já pode fazer nas PWA's. Algumas desvantagens como ausência de comunicação bluetooth, usb, NFC também foram citadas.

[Lucas Cavalcanti do NuBank](https://hipsters.tech/tecnologias-no-nubank-hipsters-01/) trouxe as tecnologias funcionais que eles utilizam: Clojure, Datomic, Kafka. Uma frase ressaltada que me chamou muita atenção foi: " **É mais fácil adicionar código hoje do que há 3 anos atrás**". Ou seja, eles começaram o projeto greenfield total e hoje é mais fácil codificar do que no começo. Ele enfatizou bastante na imutabilidade, tanto de serviços como de infraestrutura. Os microsserviços são feitos baseados na arquitetura [Ports e Adapters](https://herbertograca.com/2017/09/14/ports-adapters-architecture/).

No segundo dia o Alberto aqui da Caelum trouxe o modelo de programação reativo, focando na escalabilidade vertical de um serviço, usando menos recurso. Ele utilizou a API do [Spring](https://domineospring.wordpress.com/2016/03/21/spring-5-e-reactive-programming-parte-1/) para mostrar códigos [reativos](https://dzone.com/articles/spring-webflux-getting-started) e ressaltou também também a API [Reativa do Java 9](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-9/). Ele também falou de alguns pontos negativos desse modelo de programação como tratamento de exceções, debug, testes e transação com banco de dados.  
Ainda neste tema, teve uma palestra sobre o [RxJava](https://github.com/ReactiveX/RxJava) com [Vert.x](https://vertx.io/). Programação reativa é a nova buzzword?

No quesito persistência, o Mário aqui da Caelum também trouxe o caso do Elo7. Como escolher qual é o melhor storage para guardar os dados? A resposta? Escolha o que melhor atende o seu problema. Mário embasou sua palestra no [Teorema CAP](https://blog.caelum.com.br/nosql-do-teorema-cap-para-paccl/) e discutiu as escolhas que eles tomaram (PostgreSQL, Cassandra, Spring Data, [JNoSQL](http://www.jnosql.org/)).  
  

Finalizei o segundo dia com apresentações sobre como lidar com segurança em microserviços usando [Json Web Token](https://blog.caelum.com.br/morte-a-sessao-entenda-esse-tal-de-stateless-session-com-tokens/) e como o Itaú está migrando código Cobol para Java com a ajuda do Kafka. Bom, com certeza essa foi uma variável em comum. Todo mundo está usando o [Kafka](https://blog.caelum.com.br/usando-o-padrao-observer-do-cdi-para-lidar-com-o-kafka/)! NuBank, Netflix, Natura, Itau

No último dia fui em duas palestras interessantes: como a Natura mudou sua infra e aumentou em quase 30% o faturamento e sobre WebAssembly do [Kumpera](https://twitter.com/kumpera).  
Alguns dados interessantes da Natura: 1% das vendas é por comércio eletrônico e 99% por relações com as consultoras de beleza. Eles remodelaram todo o ambiente focado em multi-clouding em uma empresa com mais de 50 anos e tiveram impactos significativos no negócio. Atualmente, eles fazem **2000 mil DEPLOY'S por mês.** A estrutura baseia-se em Azure, AWS, Kafka, Gray Log, ElasticSearch, Kubernetes.  

Já o Kumpera trouxe em sua palestra a possibilidade de usar uma linguagem só para qualquer tipo de plataforma: mobile, web, desktop, server. Ademais, enfatizou a necessidade de executar código de alta performance no browser. Ele explicou um pouco das limitações de JS e as motivações para o surgimento do [WebAssembly,](https://webassembly.org/) sendo um deles o problema de transpilar para JavaScript que algumas linguagens adotam (ELM, TypeScript, Clojure Script, Reason). Segundo ele: "**transpilação é uma gambiarra!**". Prosseguindo, finalizou a palestra com um código C# com todo o [mono](https://www.mono-project.com/) rodando no browser.

Finalizando o último dia, [Otávio Santana](https://twitter.com/otaviojava) deu uma palestra show sobre desenvolvimento open source. Geralmente nesse tipo de evento o pessoal da filmagem não faz a menor ideia do que está rolando, mas na palestra do Otávio eu vi muitos deles caindo na gargalhada. O [Paulo Silveira](https://twitter.com/paulo_caelum?lang=en) trouxe seu tema da moda: "[Você não é pago para escrever código](https://blog.caelum.com.br/voce-nao-e-pago-para-programar/)". Ele ressalta as soft skills que um profissional deve ter: ter um espírito "Olympian" e ter mais "acabativa" ao invés de muita "iniciativa". O engraçado mesmo foi ver a galera ao final perguntando se ele não sente falta de programar! 

Concluindo, foram ótimos 3 dias de evento, com temas muito bem distribuídos e novas buzzwords surgindo. O espaço era ótimo, excelente localização e queria agradecer a minha equipe do Banco do Brasil por ter me enviado nesta missão. Até o próximo ano!
