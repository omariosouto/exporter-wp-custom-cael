---
title: "Como compartilhar seu localhost de forma simplificada com o Ngrok"
date: "2019-03-11"
author: "marcio.souto"
authorEmail: "mario.souto@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

## Introdução

Olá, eu me chamo Mario Souto, trabalho como desenvolvedor e instrutor no Grupo Caelum Alura e nas horas vagas faço consultoria e algumas doideras.

Quando estou trabalhando em algum projeto normalmente trabalho com uma versão do código rodando na minha máquina para só depois mandar isso para produção. Em alguns casos não possuo um servidor de testes antes de ir para prod, logo eu preciso garantir que na minha máquina está funcionando tudo certinho e depois é ver como ta em produção.

Para facilitar o processo de validar com o cliente eu comecei a utilizar uma ferramenta bem bacana chamada Ngrok que me permite compartilhar meu localhost com qualquer pessoa \\o

## Preparando o ambiente

Para começarmos, primeiramente vamos precisar instalar o ngrok, para fazer isso devemos ir até o [site da ferramenta na área de download (https://ngrok.com/download)](https://ngrok.com/download)

![Site do ngrok na área de download](http://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-7.png)

Vale ressaltar que no windows, é baixado um .exe que você irá precisar sempre que for utilizar o ngrok.

## Testando na prática

Agora que temos o ngrok instalado, vou pegar um exemplo de um site em wordpress para trabalharmos. O freela que estou desenvolvendo está rodando em: http://localhost:9080/

![Site de exemplo aberto](http://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-11.png)

Para poder permitir que o cliente visualize o que eu fiz até agora, basta ir até o terminal (no caso do windows, rodar o executável baixado do site) e digitar `**ngrok http 9080**`

![Terminal com o ngrok rodando](http://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-8.png)

Agora, basta acessar o endereço no navegador e…

![URL do Ngrok aberta](http://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-9.png)

Nada apareceu :(

Bom isso acontece porque o WordPress tem uma URL padrão que é definida no banco de dados. Para tudo funcionar com o ngrok, devemos mudar essa URL manualmente no arquivo wp-config.php

```php

define( "WP_HOME", "http://61dc494a.ngrok.io/" );

define( "WP_SITEURL", "http://61dc494a.ngrok.io/" );
```

Ou você pode pegar automaticamente a URL que estiver acessando (lembre-se de alterar o https caso queira liberar o acesso como HTTPs):

```php

define( "WP_HOME', "http://" .$_SERVER["HTTP_HOST"]. "/" );

define( "WP_SITEURL", "http://" .$_SERVER["HTTP_HOST"]. "/" );
```

![Site abrindo publicamente através do Ngrok](http://blog.caelum.com.br/wp-content/uploads/2019/02/pasted-image-0-10.png)

Com isso conseguimos liberar nosso site para o cliente visualizar de forma simples e para cancelar a URL basta apertar Ctrl + C no processo rodando do ngrok no terminal :)

Um outro uso bem bacana do ngrok é conseguir testar o comportamento de uma PWA que depende de uma conexão HTTPs sem precisar publicá-la.

Espero que tenha gostado do post, em breve trarei mais dicas, não deixe de [me seguir nas minhas redes sociais](https://twitter.com/omariosouto) e acompanhar meus outros posts em meu site pessoal [https://mariosouto.com](https://mariosouto.com) até mais \\o
