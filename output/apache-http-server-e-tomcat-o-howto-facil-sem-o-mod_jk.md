---
title: "Apache HTTP server e Tomcat, o HowTo fácil sem o mod_jk"
date: "2006-09-26"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Migramos os nossos sitemas do servidor compartilhado da locaweb para um servidor dedicado, porém enfrentamos o clássico problema: responder php e java na mesma porta de acordo coma URL. O nosso blog é gerenciado pelo [wordpress](http://www.wordpress.org/), que precisa do PHP, já os nossos outros serviços precisam de Java 5 e tomcat 5.5.

Eu e o Guilherme Moreira somos apenas usuários comuns do linux, sem conhecimentos avançados. Para piorar estamos acostumados com o **debian**, e o servidor novo é um **fedora**. Tentamos fugir do apache http server (daqui em diante referido apenas como _apache_) e mais ainda do [mod\_jk](http://tomcat.apache.org/connectors-doc/howto/quick.html).

A vantagem de usar o `mod_jk` é que o apache poderia servir o conteúdo estático das nossas web applications, mas já sabemos a algum tempo que o tomcat e o apache [têm performance muito parecida quando estão servindo arquivos estáticos](http://marc.theaimsgroup.com/?t=106036187700004&r=1&w=2) (htmls e imagens, por exemplo).

Corremos então atrás de alternativas mais simples, no meio do caminho [encontramos soluções estranhas](http://wiki.apache.org/tomcat/UsingPhp) que não gostamos, e a melhor a que chegamos foi usar o [mod\_proxy](http://httpd.apache.org/docs/2.2/mod/mod_proxy.html).

Vamos então a um tutorial bem sucinto para essa instalação em qualquer distribuição linux, omitindo qualquer detalhe sobre descompactação e download de arquivos básicos. Caso você queira instalar tudo isso no windows, só que com o `mod_jk`, você pode acessar o excelente [howto de 15 minutos](http://plentz.org/2005/11/10/webserver-em-15-minutos/) que o [Diego Plentz](http://www.plentz.org/) criou.

Baixe os fontes do Apache 2.2 e configure o build com o que precisamos ([mod\_rewrite](http://httpd.apache.org/docs/2.2/mod/mod_rewrite.html) para o wordpress): `./configure—prefix=/usr/local/apache —enable-rewrite \ —enable-shared=max \ —enable-so \ —enable-proxy`

Você poderia adicionar outros módulos. Agora basta o `make` e o `make install`.

Vamos ao PHP, baixe o fonte e configure o build: `./configure—prefix=/usr/local \ —enable-xslt \ —with-apxs2=/usr/local/apache/bin/apxs \ —with-mysql=/usr/bin/mysql_config`

`make` e o `make install` aqui também.

Pronto. Considere que o diretório `/var/www/blog.caelum.com.br` possui o nosso wordpress. No seu `/usr/local/apache/conf` (ou diretório similar) edite o `httpd.conf` e permita o acesso aos arquivos do diretório do wordpress: \[xml\] <Directory /var/www/blog.caelum.com.br> Options FollowSymLinks AllowOverride All Order allow,deny Allow from all </Directory> \[/xml\]

Crie a associação de `blog.caelum.com.br` para o diretório `/var/www/blog.caelum.com.br`:

``[xml] <VirtualHost *:80> ServerName blog.caelum.com.br DocumentRoot /var/www/blog.caelum.com.br </VirtualHost> [/xml]

Já, os outros sites que você quiser que sejam servidor pelo tomcat, você vai configurar o apache para agir como proxy, e delegar todas as requisições, e configuramos também para que ele tome o cuidado de reescrever alguns headers para que, por exemplo, não volte uma resposta com redirect para uma URL na porta 8080: [xml] <VirtualHost *:80> ServerName www.caelum.com.br ServerAlias caelum.com.br www.caelum.com.br ProxyPass / http://caelum.com.br:8080/ ProxyPassReverse / http://caelum.com.br:8080/ </VirtualHost> [/xml]

Aí no seu server.xml do tomcat, basta direcionar o _host_ caelum.com.br: [xml] <Host name="caelum.com.br" appBase="/xxx/yyy/webapps/caelum.com.br" unpackWARs="true" autoDeploy="true" xmlValidation="false" xmlNamespaceAware="false"> <Alias>www.caelum.com.br</Alias> </Host> [/xml]

Pronto! Muito simples para até para que é apenas usuário do linux. E bem mais fácil que com o `mod_jk`! Agora tudo parece estar rodando corretamente, com [apenas um probleminha ou outro](http://guj.com.br/posts/list/42117.java). Fica aí a receita de bolo para quem precisar, qualquer sugestão é muito bem vinda!``
