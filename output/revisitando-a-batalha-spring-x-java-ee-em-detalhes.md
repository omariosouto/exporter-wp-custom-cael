---
title: "Revisitando a batalha Spring x Java EE em detalhes"
date: "2015-12-01"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A discussão sobre qual das duas plataformas utilizar no [próximo projeto](http://www.slideshare.net/gui0rafa/spring-vs-java-ee-qconsp-2012) existe faz muito tempo. Inclusive ultimamente teve discussão entre as duas plataformas. [Jurgen Hoeller](https://spring.io/team/jhoeller) postou sobre a adoção da mais nova versão do [Java EE](https://spring.io/blog/2015/06/04/happy-second-birthday-java-ee-7-how-is-it-going-in-production) e foi respondido no [blog da Oracle](https://blogs.oracle.com/reza/entry/the_ghosts_of_java_ee).

Olhando um pouco a história, há muito, mas muito tempo atrás, o Java EE era realmente muito complicado e nem era necessário entrar numa discussão, usar o Spring era um caminho mais simples e mais fácil de evoluir, muito melhor. Aí chegou a versão 5 do Java EE e a discussão voltou a ficar um pouco mais quente.

Nessa versão foi introduzida um suporte um pouco melhor a injeção de dependências, mas tudo ainda estava bem centrado nos EJBs. Também foi nessa versão que criar um EJB passou a ser bem mais simples, e ele passou a ser enxergado "apenas" como um objeto com super poderes, ao invés de algo terrivelmente assustador e complicado de configurar. Além disso a parte dos Entity Beans ficou bem mais simples, com a chegada da primeira versão da JPA. Aí veio o Java EE 6 e tudo ficou ainda mais próximo.

Foi introduzida a especificação sobre o container de injeção de dependências, o CDI. Ele passou a ser a estrela da companhia e a ideia é que essa especificação seja o ponto de integração entre todas as outras. A versão 7 seguiu o mesmo ritmo, existem mais componentes ligados ao CDI e outras especificações que ainda careciam de uma configuração mais simples, como a do [JMS](https://blog.caelum.com.br/a-nova-api-do-jms-2-0-no-java-ee-7/), ganhou uma nova versão muito mais simples.

Nesse mesmo tempo o Spring também evoluiu. Hoje está na versão 4 e com a [versão 5](https://spring.io/blog/2015/08/03/coming-up-in-2016-spring-framework-4-3-5-0) já planejada, e a configuração ficou muito mais simples. Além disso, eles possuem diversos [projetos anexos](http://spring.io/projects) ao _core_ do framework, que realmente facilitam a vida do desenvolvedor. O auge da facilidade veio com a criação do [Spring Boot](https://domineospring.wordpress.com/2015/06/26/comecando-com-a-magia-do-spring-boot/), onde quase todas as configurações são feitas de [maneira automática](https://domineospring.wordpress.com/2015/11/16/configuracoes-automaticas-do-spring-boot/), restando ao desenvolvedor fazer apenas o estritamente necessário.

Perceba que gastamos mais tempo falando da evolução do Java EE do que do Spring, e isso não vem do fato deles evoluirem mais. É que chama atenção como eles deram uma guinada nos rumos da especificação por conta do terreno que estava sendo perdido.

Passada essa declaração inicial, sobre as evoluções, vamos tentar olhar para alguns pontos específicos que podem nos ajudar a olhar de maneira mais crítica para cada uma das plataformas. Vamos passar pelos seguintes pontos:

1. Ritmo de atualização
2. Diferença entre alguns dos principais componentes das plataformas
3. Integração entre os componentes das plataformas
4. Documentação
5. Ambiente de desenvolvimento
6. Possível uso das plataformas em conjunto
7. Pragmatismo no momento das escolhas

## **Ritmo de atualização**

Esse é um quesito complicado para a especificação do Java EE. Cada nova especificação precisa passar por um processo de aprovação, para que ela seja integrada ao conjunto das especificações que compõe a plataforma. Enquanto isso, no Spring, cada ideia nova que eles tem, e que acham válida, já pode entrar como um novo componente lançado como _alpha,_ apenas para que o desenvolvedor já possa ir provando.

Você pode visitar a página [oficial de projetos](http://spring.io/projects) ou até mesmo o [github deles](https://github.com/spring-projects). Existem projetos que são consolidados, mas também existem vários que os desenvolvedores ainda estão meio por fora, por exemplo: o [spring-reactive](https://github.com/spring-projects/spring-reactive) e o [spring-integration-java-dsl](https://github.com/spring-projects/spring-integration-java-dsl). Esses projetos podem ir para frente ou não, mas eles são concebidos e facilmente integráveis ao seu ambiente de desenvolvimento. Para algo entrar na especificação Java EE, de vez em quando, demora bastante tempo.

Vide a especificação que trata de aplicações web [Action Based](https://ozark.java.net/). Desenvolvemos aplicações seguindo essa linha faz anos e só agora vamos ter algo suportado nativamente pelos servidores de aplicação.  A mesma coisa vale para outras especificações, por exemplo o JMS. Há muito tempo que o Spring tem o _JMSTemplate_ para facilitar o uso dessa especificação e só na versão 7 veio algo semelhante no Java EE.

A Red Hat e a Apache até tentam criar projetos para compor o mundo Java EE, como o Arquillian, Forge e o DeltaSpike. Só que falta para eles documentação e, por incrível que pareça, sensibilidade para entender realmente que tipo de atualizações facilitam a vida dos desenvolvedores.

Uma coisa que pode ficar na sua mente é: a preocupação do time do Spring é em criar projetos que possam ser agregados a plataforma e realmente ajudar os mais variados tipos de desenvolvedores. Enquanto que a preocupação do Java EE é de padronizar e não em criar coisas novas.

## Diferença entre alguns dos principais componentes das plataformas

Existem certos componentes que são mais comentados dentro das plataformas. Alguns exemplos: segurança, mensageria, web services, framework web etc. Vamos pegar alguns deles para analisarmos.

### Segurança

A parte de segurança no Java EE é provida pela especificação JAAS. Por sinal ela é o próximo alvo de melhoria, que vai ser entregue na versão 8 da spec. Toda configuração do JAAS varia de servidor para servidor, e esse é um dos maiores problemas dela. Por mais que você realmente aprenda como ela funciona, uma parte da configuração que você fizer para um projeto, não vai poder ser reaproveitada para o outro, caso você mude de **servidor.** 

Um outro ponto negativo é o fato de que as proteções dos endereços são feitas no próprio _web.xml,_ algo pouco flexível para certos tipos de projetos.  Um terceiro ponto que **eu** não gosto é o fato de que usuários, perfis e outros serviços não serem expressados por interfaces dentro da especificação. Então por mais que o seu projeto use JPA, você terá que fazer uma configuração de query para algum servidor, ensinando-o como buscar as informações. Ao invés de implementar uma interface no seu DAO.

Já o Spring Security é completamente baseado em interfaces e possibilita que sua configuração seja feita de modo programático. Por já ter sido pensando de maneira mais orientada a objetos, eles possuem diversos pontos de extensões que podem ser explorados de maneira mais tranquila pelos desenvolvedores.

### Mensageria

Caso o seu projeto precise fazer alguma integração baseada em mensageria, você vai precisar ter disponível um servidor dedicado que cuide disso para você. Caso esteja trabalhando dentro de um servidor de aplicação, isso já vem pronto. Com a versão 7 do Java EE, receber e mandar mensagens ficou muito simples. Registrar um tópico ou uma fila se resume simplesmente ao fato de anotar uma classe com as informações devidas. A mesma coisa vale para receber as mensagens enviadas para uma _destination_.

Em um projeto baseado em Spring, o que mais vai mudar é que você vai precisar saber informações específicas sobre o seu servidor de mensageria para fazer a configuração. Nada que não possa ser aprendido, mas é um passo a mais.  Em relação a receber as mensagens, é tudo muito parecido.

### Web Services SOAP

Aqui, de novo, ainda é mais simples dentro de um servidor de aplicação Java EE. Toda infraestrutura necessária já é provida pelo seu servidor de aplicação e tudo isso é exposto para você através de simples annotations. Essa é uma especificação que realmente acertou muito na sua padronização, expor um web service SOAP é muito simples dentro de container Java EE.

Em contrapartida, usando o Spring-WS, você precisa configurar bastante detalhe.  Por exemplo é necessário que seja configurada a Servlet que vai processar as chamadas que vierem via SOAP. Além disso, você precisa configurar o objeto que define o seu WSDL. São estes tipos de detalhes que não queremos ter de nos preocupar em primeira mão e é justamente por esse motivo que eu nunca uso essa extensão deles.

### Framework Web

O mundo Java EE, até agora, só nos ofereceu o JSF, que é um framework baseado em componentes e que deu bons passos na sua evolução a partir da versão 2. Para muita gente, o problema dele é que ele é justamente baseado em componentes e isso não pode ser aproveitado quando a sua aplicação demanda um cuidado maior com o front. A boa noticia é que a partir da última versão, criar uma tela com o JSF ficou muito mais flexível e você pode aplicar praticamente todas as boas práticas que você sentir necessidade.

Já o Spring entrega para a gente um framework action based, o Spring MVC. Em geral ele é muito útil para qualquer tipo de aplicação web, inclusive as que o JSF se propõe a resolver. Também é muito maduro e muito bem integrado com todo ecossistema do Spring. O mundo Java EE está produzindo uma [nova especificação](https://www.jcp.org/en/jsr/detail?id=371) justamente para este tipo de aplicação, o problema é ela chegar muito tempo depois do Spring MVC e não trazer nada que ele já não te entregue de mão beijada. Para mim, é uma grande falha do mundo Java, pois o Spring MVC não vai seguir a spec e vocês, talvez, terão de se adaptar a uma nova implementação.

Esse é um tópico que me dá um pouco de medo. Realmente não vejo motivo para ter mais de uma versão de framework action based e component based. São ferramentas que usamos muito no dia-a-dia e que deveriam ser as melhores possíveis. Qual o motivo de ter diversidade e esforços espalhados nesses tipos de frameworks, se podíamos investir e ter os 2 melhores do planeta? Ainda não vi um argumento muito forte. Pego exemplos de outras linguagens: Rails no mundo Ruby e ASP.NET MVC para a plataforma .NET. Frameworks super completos e que suprimem a necessidade da criação de outros. Podem até existir, mas não são tão relevantes. No mundo Java, para mim, a exceção foi o Play, apenas porque eles propuseram uma ferramenta realmente diferente, apesar de ser fortemente inspirada pelo Rails.

### Outras tecnologias/especificações

Poderíamos ainda debater sobre outros componentes. Por exemplo, o Spring MVC consegue fazer o papel do JAX-RS numa aplicação, apesar deles também terem criado o Spring Hateoas. Temos o Spring Data JPA que facilita muito a utilização da JPA. O último é mais uma junção de forças enquanto que o primeiro, realmente não causa um impacto tão grande na sua vida. A ideia era pegar algumas das principais para olharmos e também para deixar a seguinte mensagem: tudo está muito parelho, use uma tabela de decisão calçada por uma visão pragmática para você tomar a melhor decisão.

## Integração entre os componentes das plataformas

Aqui o Spring ainda está na frente. Como tudo, desde sempre, gira em torno do _core,_ todas as extensões se integram de maneira transparente. Você coloca o Spring Security e qualquer objeto gerenciado pode ser protegido. Você adiciona o Spring Cache e o retorno de qualquer método pode ser guardado no cache. Adicionamos o Spring Social e já temos uma integração com o Security, para que o usuário logado já entre no contexto.

No mundo Java EE ainda precisamos fazer, muitas vezes, com que os componentes se integrem manualmente. Por exemplo temos o JAAS, mas não temos nada que possamos usar para proteger os trechos das páginas. Temos a especificação de Cache, mas ela não se integra com os métodos dos beans gerenciados pelo CDI. Temos o CDI, mas ainda nem todos os componentes podem ser injetados através dele. É necessário uma integração fina entre tudo, tem que funcionar como um projeto só, não como vários projetos separados suportados pelo servidor de aplicação.

## Documentação

Documentação deveria ser o ponto de partida para o aprendizado de qualquer coisa. Esse é um outro quesito que o Spring leva vantagem. A documentação oferecida pelo site vai bem além de uma visão superficial da tecnologia. Contém exemplos de uso e vários dos projetos possuem um github com vários exemplos que podem ser facilmente importados e analisados pelo desenvolvedor. Com um pouco de paciência, você acaba achando a maioria das coisas que precisa na própria documentação.

A Red Hat até tenta fazer isso com as extensões que ela produz, como o [Swarm](http://wildfly.org/swarm/) ou o [Keycloak](http://keycloak.jboss.org/), mas não cria exemplos de uso para todas as implementações de especificações que estão dentro do servidor. A mesma coisa acontece para todos e, dessa forma, o desenvolvedor tem que ficar caindo em fóruns e tutoriais para conseguir aprender.

## Ambiente de desenvolvimento

Para mim essa sempre foi uma das principais dificuldades da plataforma Java. Apesar de termos boas IDEs, como o Eclipse e o IntelliJ, um bom ambiente de desenvolvimento vai muito além disso. Vamos pegar algumas necessidades que podemos ter:

1. Geração de configurações que se repetem entre projetos
2. Geração de códigos de negócio que se repetem entre os projetos
3. Deploy e redeploy das aplicações em ambiente de desenvolvimento
4. Geração do ambiente necessário para a execução da aplicação
5. Execução de testes e diferenciação por ambiente

### Configurações e códigos repetitivos

A maioria dos projetos Java passam sempre pelos mesmos problemas. Vou até tirar da jogada que vários dos projetos fazem uso de outras ferramentas, principalmente as ligadas ao front, como Gulp e afins. Apesar de claramente terem seu valor automatizando outras tarefas, trazem um pouco mais de complexidade para o ambiente de desenvolvimento.

Caso você tenha optado pelo pacote comum de desenvolvimento de aplicações baseadas no Java EE, você vai encontrar algumas das dificuldades citadas acima. Precisa configurar o JSF, é necessário criar o faces-config.xml. Vai usar o JAAS dentro do Wildlfy, precisa ter o jboss-web.xml no seu projeto. Quer usar o JAX-RS, é necessário criar a classe que indica o contexto raiz dos seus serviços. A mesma coisa vale para códigos de negócio que se repetem, como tudo necessário para escrever um CRUD, listeners do JMS etc. Fazer da primeira vez é interessante, na segunda você realmente aprende e partir da terceira já fica repetitivo. A Red Hat se movimentou e criou um projeto bem interessante, chamado [Jboss Forge](http://forge.jboss.org/), que automatiza um monte dessas coisas. Junto com outros desenvolvedores da Caelum, também criei um projeto para facilitar isso, o SetupMyProject, [confira aqui](http://setupmyproject.com/).

Apesar de gerar ser uma boa coisa, o melhor era nem precisar ficar configurando a maior parte destas coisas. E aí o Spring fez uma jogada de mestre e criou o projeto chamado [Spring Boot](http://projects.spring.io/spring-boot/). Eles colheram tudo que a gente mais usa e decidiram por uma pilha de tecnologias. Basta que um jar seja adicionado no _classpath_ e toda configuração de infraestrutura é feita automaticamente, por classes deles. Por exemplo, quer usar um servidor como o Redis para guardar seus objetos cacheados, adicione o jar do mesmo e o Spring Boot já permite que você receba injetado a conexão com ele. Isso porque o Redis tem uma porta de funcionamento default e também vem sem proteção com senha por padrão, para que me obrigar a configurar? Outro ponto interessante, é que ele traz suporte aos servidores embedded. Caso use ele, acabou a necessidade de obrigar os devs a ficarem baixando tomcat ou jetty e ainda terem que ficar aprendendo a configurá-los. Colocou o jar no _classpath,_ ele já vai usar o servidor selecionado e rodar o seu projeto.

### Deploy e redeploy das aplicações em ambiente de desenvolvimento

Além disso o Spring Boot trouxe uma extensão que permite que novos redeploys em desenvolvimento sejam feitos, na maioria das vezes, em menos de três segundos, é o [Spring Boot dev tools](https://domineospring.wordpress.com/2015/09/23/ficando-ainda-mais-produtivo-com-o-spring-boot/). Tudo isso junto deixa você mais produtivo, e vai contar no fim do mês para você e para a empresa que você trabalha.

### Geração do ambiente necessário para a execução da aplicação

Para finalizar, gerar o arquivo de deploy é algo que precisamos o tempo inteiro. O Spring Boot já criou uma extensão para o Maven que nos permite gerar um jar que pode ser executado direto, sem a necessidade de instalar e configurar um servidor em produção, ótima opção para a maioria dos projetos.

### Execução de testes e diferenciação por ambiente

Uma ajuda nesse tópico realmente pode ser útil durante o desenvolvimento. Por exemplo, realizar testes de integração é uma tarefa bem comum para a gente. Como verificar se suas JPQL estão funcionando? Não tem jeito, você é obrigado a bater no banco de testes e também a limpá-lo entre cada execução de testes. No mundo Spring [isso está pronto](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/integration-testing.html) enquanto que para o mundo Java EE, o mais próximo que temos é o [Arquillian](http://arquillian.org/). A diferença é na complexidade do setup, o Arquillian exige muita configuração e tudo só para emular o container, não tem nada que efetivamente te ajude a controlar melhor os testes.

A mesma diferença vale para diferenciar os ambientes. Pensando no caso mais simples, precisamos de um DataSource para desenvolvimento e outro para produção. O mais perto que temos disso no Java EE é a parte _[alternatives](https://docs.oracle.com/javaee/7/tutorial/cdi-adv002.htm)_ do CDI. Só que é tudo baseado em XML e você precisa trocar a configuração do _beans.xml._ O [DeltaSpike](https://deltaspike.apache.org/documentation/projectstage.html) tenta ajudar nisso, permitindo que você use anotações específicas dele para informar quais classes devem ser gerenciadas em função do ambiente. O problema é que o Servidor de Aplicação não sabe nada sobre isso e mais uma vez perdemos a integração. Em contrapartida, a parte de [Profiles](http://docs.spring.io/spring/docs/current/spring-framework-reference/htmlsingle/#beans-environment) do Spring é muito simples e bem integrada a toda a plataforma.

## Possível uso das plataformas em conjunto

Essa é uma daquelas coisas que muitas vezes são rechaçadas na arquitetura do projeto. Não existe nenhuma regra do mundo que te faça optar por um caminho e, obrigatoriamente, ter que abandonar o outro. Por exemplo, o Spring Security é completamente baseado em _Servlet Filter,_ o que faz com que ele seja plugável em qualquer aplicação web Java que rode sobre um _Servlet Container._ Só que você vai ficar limitado a proteger só as suas URLs, pois os beans estarão sendo gerenciados pelo CDI/EJB.

Vamos agora pensar numa aplicação baseada no Spring, você vai querer usar o Spring Data, por exemplo. Só que para isso, precisa do EntityManager provido pelo servidor de aplicação, já que o DataSource está configurado pelo servidor. Você pode configurar o Spring para buscar a referência para o _EntityManager_ através da JNDI. O mesmo vale para o controle transacional! Essa é uma característica do Spring, [ele não evita](https://domineospring.wordpress.com/2015/07/27/spring-security-spring-jpa-e-o-javaee/) o que está pronto num servidor Java EE, ele simplesmente te dá outras opções.

Para fechar essa parte, tanto o Spring quanto qualquer implementação do CDI, possuem classes que representam o container dos objetos gerenciados. Se você estiver encurralado, por exemplo dentro de uma classe gerenciada pelo CDI precisando de acesso a algum objeto gerenciado pelo Spring, você pode fazer uso dessas classes. Caso tenha ficado curioso, dê uma [olhada aqui](https://domineospring.wordpress.com/2015/11/03/um-pouco-de-gambi-nao-faz-mal-a-ninguem-spring-em-ambientes-nao-gerenciados/) para ver como acessar o container do Spring de maneira programática ou [aqui](https://github.com/weld/core/blob/a872d0573e9cae8c26edbb109ee6b89ec0445d9a/environments/se/core/src/test/java/org/jboss/weld/environment/se/test/container/instance/ContainerInstanceTest.java#L38) para ver como acessar o container do CDI.

## Pragmatismo no momento das escolhas

Supostamente isso não precisaria ser destacado. De todo jeito acho que vale o comentário, decida pela plataforma pautado pelas suas necessidades e as habilidades do time em cada uma delas.

Para mim, a plataforma do Spring ainda está em vantagem por conta que eles podem fazer tudo integrado ao framework, ao contrário do Java EE que precisa fazer tudo voltado para a especificação. Para a nossa sorte o Java EE está avançando muito e, como disse, a versão 7 está muito melhor e mais simples.

Um outro ponto que você precisa levar em consideração é que a decisão da plataforma base nem sempre está nas mãos dos programadores. Você pode acabar se vendo obrigado a usar uma plataforma apenas porque o sistema existente já faz uso dela, então conhecer as duas te deixa mais preparado para a maioria dos projetos que você vai enfrentar.

Como vimos, por mais que elas sejam duas plataformas que muitas vezes tentam atingir o mesmo propósito, você pode considerar em juntar as forças, quando for necessário. Deixe sempre essa possibilidade em aberto!

## Aprendendo mais

Não deixe de conferir nossos cursos e livros sobre esses assuntos. Na Casa do Código você pode encontrar dois livros sobre Spring, o [meu](http://www.casadocodigo.com.br/products/livro-spring-mvc) e o de [Henrique Lobo](http://www.casadocodigo.com.br/products/livro-spring-framework). Também pode encontrar livros que tratam sobre o [Java EE](http://www.casadocodigo.com.br/products/livro-javaee). Tem também especificamente sobre o [CDI](http://www.casadocodigo.com.br/products/livro-cdi) e sobre [JPA e JSF](http://www.casadocodigo.com.br/products/livro-jsf-jpa).  Caso prefira fazer aprender através de aulas presenciais, a Caelum também já disponibiliza os treinamentos, você pode conferir a ementa sobre o [Spring](https://www.caelum.com.br/curso-spring-framework/) e sobre o [Java EE](https://www.caelum.com.br/curso-plataforma-java-ee/). Em breve ainda teremos os mesmos treinamentos no [Alura](http://www.alura.com.br)!
