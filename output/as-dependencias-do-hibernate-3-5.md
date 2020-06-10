---
title: "As dependências do Hibernate 3.5 e 3.6"
date: "2010-04-14"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O primeiro desafio de um iniciante em Hibernate, antes mesmo de fazer um tutorial prático, como o disponível na [apostila FJ-21 da Caelum](http://www.caelum.com.br/curso/fj-21-java-web/), é organizar as dependências necessárias. Até o Hibernate 3.3, a maneira a qual ele era distribuído dava trabalho pra quem precisasse usar a JPA: além do zip contendo o hibernate, era necessário fazer o download de um outro jar contendo a implementação do `EntityManager` e ainda outro contendo as anotações e interfaces da JPA.

A versão 3.5, como a 3.6, além de trazer uma implementação da JPA2, juntou todos esses pacotes em um só. A documentação está ainda mais completa, assim como a parte explicando o processo de [setup do seu primeiro projeto](http://docs.jboss.org/hibernate/stable/core/reference/en/html/tutorial.html#tutorial-firstapp-setup). Apesar disso, como se pode ler na documentação, ela é feita assumindo que você esteja usando o maven. Muitos iniciantes em Java ainda tem dificuldades pra encarar o Maven, ou há ainda quem prefira usar o Ant, Ivy ou gerenciar as dependências manualmente. Quais dependências preciso então para rodar uma aplicação com Hibernate 3.5/3.6?

Para facilitar seu trabalho, deixamos aqui um zip com todas essas dependências do Hibernate 3.5.0, além de um arquivo de configuração para o Log4J, um hibernate.cfg.xml e jars opcionais. **O arquivo do Hibernate antigo não é mais recomendado. Melhor seguir o passo a passo e obter o Hibernate mais novo.**

Ou então você pode juntar esses jars seguindo o passo a passo: primeiramente precisa baixar a distribuição 3.5/3.6 a partir da [página de downloads do hibernate no sourceforge](http://sourceforge.net/projects/hibernate/files/hibernate3/). Ao descompactá-la, encontramos os seguintes arquivos e diretórios:

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/hibernate-dir.png)](https://blog.caelum.com.br/wp-content/uploads/2010/04/hibernate-dir.png)

O `hibernate3.jar` é um jar contendo as classes fundamentais do Hibernate. Agora, dentro de `lib`, temos o diretório `required`, que são dependências que sempre precisaremos utilizar junto com o Hibernate. Todas essas 6 dependências são necessárias:

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/lib-required-dir.png)](https://blog.caelum.com.br/wp-content/uploads/2010/04/hibernate-dir.png)

Dentro de `lib`, ainda precisamos do jar `hibernate-jpa-2.0-api-1.0.0.Final.jar` que está na pasta `jpa` para poder utilizar as anotações do Java EE. Caso você va usar o MySQL, também precisa [fazer o download do driver JDBC](http://dev.mysql.com/downloads/connector/j/) para ele.

Ainda não terminou. Precisamos do nosso logging funcionando. Se você tentar executar algum exemplo agora, vai receber o conhecido _Exception in thread "main" java.lang.NoClassDefFoundError: org/slf4j/impl/StaticLoggerBinder_. O SL4J, usado pelo Hibernate, é uma casca para as outras ferramentas de logging, e precisa então de um jar que indique qual logger será utilizado como backend. Na [página de distribuições do SL4J](http://www.slf4j.org/dist/) você deve baixar a versão correspondente que está usando, seria a 1.5.8 no caso do Hibernate 3.5.0, e pegar o adaptador `slf4j-log4j12-1.5.8.jar`. Para o Hibernate 3.6.2, é necessário o `slf4j-log4j12-1.6.1.jar`. Também precisamos do [jar do Log4J 1.2.x](http://logging.apache.org/log4j/1.2/download.html).

**Principais dependências opcionais**

Usar o Hibernate facilita muito o desenvolvimento com banco de dados, porém para uma performance e escalabilidade adequada [é necessário ficar atento em alguns pontos fundamentais](https://blog.caelum.com.br/2008/01/28/os-7-habitos-dos-desenvolvedores-hibernate-e-jpa-altamente-eficazes/), e também simples, para que ele não se torne um gargalo na sua aplicação. Dentro do diretório `optional`, destaco como essenciais o **c3p0** para pool de conexão e o **ehcache** para second level cache. O ehcache necessitará do commons-logging por sua vez.

Para você tirar todo o poder da JPA 2, vai querer também usar [a api de meta modelos](http://blogs.sun.com/ldemichiel/entry/java_persistence_2_0_proposed), e pra isso precisa do [gerador de modelos do Hibernate](http://repository.jboss.com/maven2/org/hibernate/hibernate-jpamodelgen/1.0.0.Final/).
