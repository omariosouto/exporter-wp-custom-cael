---
title: "Comece a trabalhar com Java no Amazon S3"
date: "2012-08-14"
author: "leonardo.wolter"
authorEmail: "leonardo.wolter@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

É relativamente simples [colocar sua aplicação no cloud da Amazon](https://blog.caelum.com.br/rodando-sua-aplicacao-na-amazon-do-brasil/ "Amazon"). Um dos serviços oferecidos e bastante utilizados é o  [Amazon Simple Storage Service (S3)](http://aws.amazon.com/s3/ "Amazon S3"), para armazenamento de dados em cloud.

[![](https://blog.caelum.com.br/wp-content/uploads/2030/06/atloutside-300x225.jpeg "atloutside")](https://blog.caelum.com.br/wp-content/uploads/2030/06/atloutside.jpeg)

A vantagem do serviço S3 é justamente não precisarmos ter tanta preocupação com a gestão do hardware e infraestrutura, podendo também diminuir custos. O S3 oferece um espaço virtualmente ilimitado (seus arquivos podem ter de 1byte a 5terabytes cada), sendo estes arquivos gravados redundantemente em diversos dispositivos e reparados caso sejam corrompidos. Um bucket pode ser armazenado em diferentes regiões (como em São Paulo, por exemplo), possibilitando uma menor latência. É frequentemente utilizado para armazenar imagens de sites onde há muito conteúdo gerado pelos usuários.

O S3 pode ser manipulado pelo [console](http://aws.amazon.com/ "Amazon") manualmente. Mas o interessante é codificar para realizar as operações de storage. Para isso, há APIs nas mais diversas linguagens [AWS SDK](http://aws.amazon.com/sdkforjava/)(disponível em [Java](http://aws.amazon.com/java/), [Mobile](http://aws.amazon.com/mobile/)(iOS eAndroid) , [PHP](http://aws.amazon.com/php/), [Python](http://aws.amazon.com/python/), [Ruby](http://aws.amazon.com/ruby/) e [Windows & .NET](http://aws.amazon.com/net/).).

Além da API, a Amazon disponibiliza um [plugin para Eclipse](http://aws.amazon.com/eclipse/ "Amazon Plugin")(com o [AWS SDK](http://aws.amazon.com/sdkforjava/) incluso),  que será necessário para acompanhar este post.

Considerando que você já tenha uma conta na Amazon com o S3. Dentro dele, temos uma divisão (_bucket_) chamada `caelum-imagens`:

[![](https://blog.caelum.com.br/wp-content/uploads/2030/06/amazon_bucket-e1340723595822.png)](https://blog.caelum.com.br/wp-content/uploads/2030/06/amazon_bucket-e1340723595822.png)

Tendo em mãos o plugin da amazon, vamos iniciar um novo projeto no Eclipse. Ao criar o novo projeto, selecione _AWS Java Project_:

[![](https://blog.caelum.com.br/wp-content/uploads/2030/06/awsjavaproject.png)](https://blog.caelum.com.br/wp-content/uploads/2030/06/awsjavaproject.png)

Digite o nome do seu Projeto, clique em _Configure AWS accounts_ e digite a sua `Access Key ID` e a sua `Secret Access Key` (que podem ser encontradas no menu [Security Credentials](https://portal.aws.amazon.com/gp/aws/securityCredentials), na sua conta da Amazon):

![](https://blog.caelum.com.br/wp-content/uploads/2030/06/exemplo_accesskey.png)

No final desse processo, será criado automaticamente um arquivo de nome `AwsCredentials.properties`, contendo os dados da nossa conta que digitamos anteriormente.

Para se conectar ao sistema, a API é simples: primeiro vamos ler o conteúdo do `AwsCredentials.properties`, jogando essa informação dentro de uma classe específica do AWS, chamada `PropertiesCredentials`. Com essas informações, instanciamos uma classe que é capaz de se comunicar com o S3, a `AmazonS3Client`. Observe o código que realiza essas operações:

Crie uma classe `ConectorDoS3` para teste, e coloque o seguinte código dentro do seu main:

\[java\] InputStream credentials = ConectorDoS3.class.getResourceAsStream("AwsCredentials.properties"); PropertiesCredentials awsCredentials = new PropertiesCredentials(credentials); AmazonS3 s3 = new AmazonS3Client(awsCredentials); \[/java\]

Pronto! Com a referência `s3` em mãos podemos efetuar [diversas operações através da API](http://docs.amazonwebservices.com/AWSJavaSDK/latest/javadoc/index.html) como, por exemplo, enviar uma imagem para o nosso bucket `caelum-imagens` através do metodo `putObject()`:

\[java\] s3.putObject(new PutObjectRequest("caelum-imagens", "logo.jpg", new File("diretorio-local/imagem.jpg"))); \[/java\]

De forma análoga podemos ler os dados gravados. Podemos usar o `GetObjetRequest` através do método `getObject`:

\[java\] S3Object object = s3.getObject(new GetObjectRequest("caelum-imagens", "logo.jpg")); InputStream stream = object.getObjectContent(); \[/java\]

Repare que o `[GetObjectRequest](http://docs.amazonwebservices.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/s3/model/GetObjectRequest.html)`, assim como o `Put`, pode ser configurado de diferentes formas para buscar apenas um pedaço do arquivo, etc. Em vez de buscar o arquivo com essa imagem, você pode também gerar uma URL, para que essa possa ser utilizada, por exemplo, numa tag `img` de um HTML. Isso é feito através do método `generatePresignedUrl`.

Esses são apenas os primeiros passos com o S3. Há muitos outros [detalhes e melhores práticas](http://aws.amazon.com/articles/1904) que você deve ficar atento para obter o melhor do serviço, considerando falhas e diminuindo latência e número de requests. [Conheça mais da Amazon AWS no nosso curso online](http://www.caelum.com.br/curso/online/amazon-web-services/), em especial o EC2.
