---
title: "Começando com AWS Eclipse Plugin com AWS SDK"
date: "2013-07-10"
author: "Natanael Pantoja"
authorEmail: "natanael.pantoja@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Recentemente a Amazon lançou um plugin para que o desenvolvedor possa trabalhar com sua infra direto do eclipse. Muita coisa que você pode fazer no Console do AWS, agora pode fazer diretamente pelo Eclipse! Não leva muito tempo para configurar.

Primeiro instale o plugin no eclipse em _Help -> Install new software_. Utilize: http://aws.amazon.com/eclipse

Após a instalação você precisa ir ao AWS Consol e no menu procure _Security Credentials_. Lá você encontrará algo como a imagem abaixo:

\[caption id="attachment\_5617" align="aligncenter" width="300"\][![Security Credentials](https://blog.caelum.com.br/wp-content/uploads/2013/06/Security-Credentials-300x70.png)](https://blog.caelum.com.br/wp-content/uploads/2013/06/Security-Credentials.png) Security Credentials\[/caption\]

Se você não tiver nenhuma chave criada, basta clicar no link que é disponibilizado para criar uma nova chave. Não é necessário criar uma chave para cada grupo.

Voltando ao plugin, a primeira coisa que você deve fazer é configurar suas chaves de acesso. Como na imagem abaixo, você vai precisar configurar seu `Access Key` e sua `Secret Access Key`. Os parâmetros adicionais serão necessários se você quer acessar suas instâncias, security groups entre outras coisas de dentro do eclipse.

\[caption id="attachment\_5618" align="aligncenter" width="300"\][![Configurações AWS Plugin](https://blog.caelum.com.br/wp-content/uploads/2013/06/Config-300x177.png)](https://blog.caelum.com.br/wp-content/uploads/2013/06/Config.png) Configurações AWS Plugin\[/caption\]

Pronto, com essas configurações você já pode visualizar tudo que vc tem na amazon. Basta selecionar o que se quer visualizar:

\[caption id="attachment\_5619" align="aligncenter" width="300"\][![Views AWS](https://blog.caelum.com.br/wp-content/uploads/2013/06/Screen-Shot-2013-06-17-at-15.44.53-300x115.png)](https://blog.caelum.com.br/wp-content/uploads/2013/06/Screen-Shot-2013-06-17-at-15.44.53.png) Views AWS\[/caption\]

Outra coisa interessante desse plugin é que ele já te ajuda com uma base de código baseado no seu SDK. Quando você cria seu primeiro projeto, alguns códigos de exemplo são criados para te dar uma boa introdução sobre o AWS SDK.

Lembrando que você não precisa criar o projeto diretamente pelo plugin. Basta você ter no seu projeto o SDK no path e um `AwsCredentials.properties` na raiz do seu projeto com as seguintes informações:

\[code\] secretKey=\[sua secrete key\] accessKey=\[sua access key\] \[/code\]

Você pode baixar o jar diretamente do repositório do [maven](http://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk/1.0.002 "Amazon Maven")

\[code\] <xml> <dependency> <groupId>com.amazonaws</groupId> <artifactId>aws-java-sdk</artifactId> <version>1.0.002</version> </dependency> </xml> \[/code\]

E, para subir uma máquina, basta ir menu EC2 Instances selecionar sua instância clicar com o botão direito e iniciar clicar em start instances como na figura abaixo.

[![EC2 Instances](https://blog.caelum.com.br/wp-content/uploads/2013/07/Screen-Shot-2013-07-08-at-22.14.48-300x58.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/Screen-Shot-2013-07-08-at-22.14.48.png)

[![Start Instances](https://blog.caelum.com.br/wp-content/uploads/2013/07/Screen-Shot-2013-07-08-at-22.15.45-300x193.png)](https://blog.caelum.com.br/wp-content/uploads/2013/07/Screen-Shot-2013-07-08-at-22.15.45.png)

Para quem tem muitas instâncias e precisa criar muitas temporárias, o plugin certamente ajuda muito!

Para mais informações sobre o plugin: [http://docs.aws.amazon.com/AWSToolkitEclipse/latest/GettingStartedGuide/Welcome.html](http://docs.aws.amazon.com/AWSToolkitEclipse/latest/GettingStartedGuide/Welcome.html)

Para começar com o SDK da Amazon: [http://docs.aws.amazon.com/AWSSdkDocsJava/latest/DeveloperGuide/java-dg-setup.html](http://docs.aws.amazon.com/AWSSdkDocsJava/latest/DeveloperGuide/java-dg-setup.html)
