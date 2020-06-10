---
title: "Publicando SpringBoot e Frontend em produção"
date: "2019-01-10"
author: "jopss.sossoloti"
authorEmail: "jopss.sossoloti@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Ao criarmos uma aplicação nova hoje em dia, temos vários suportes aos devs no ambiente de desenvolvimento. IDEs e gerenciadores de deploy, como maven/gradle no Java ou npm no front, facilitam bastante a carga da aplicação. Usando ainda o SpringBoot, a facilidade de subir um projeto é muito mais simplificada, bastando rodar uma classe principal contendo o método main que faz o restante das coisas para nós (como baixar um tomcat e deixar o WAR no ar).

Agora, pensando em ambiente de produção, as coisas são um pouco mais complicadas. Usando frontend Angular, não podemos simplesmente rodar “ng serve --prod”, ou com Java e SpringBoot, rodar “java -jar meuprojeto.jar”. Se entrarmos com ssh e rodarmos esses comandos, travaríamos o console. Existe a opção de deixar esses comandos rodando em background, mas no caso de precisar reiniciar a máquina (remotamente), esses comandos não estaria mais em execução.

Em produção, com ambiente Linux, devemos pensar em configurações mais robustas, e por isso, um pouco mais complexas. Vamos mostrar aqui como fazer isso de forma, digamos, manual (sem Docker). No frontend é interessante instalar um servidor de página com Nginx. Já no caso do backend podemos baixar e configurar na mão um servidor com o Tomcat. Como estamos usando SpringBoot, podemos deixar ele fazer esse trabalho para nós, mas ao menos vamos configurar um serviço para rodar nossa aplicação.

Para fins deste post, vamos chamar de “harbor-frontend” um projeto feito em Angular6 e NPM, e “harbor-backend” outro projeto feito em Java com SpringBoot e Maven, ambos vão ser configurados em uma máquina remota Linux Ubuntu. Cada linha de configuração deste post deve ser executado individualmente. Ainda, irei usar o editor de texto "vim", mas use qual achar melhor.

## Envio dos Projetos

Sendo primeiro deploy dos seus projetos ou atualização, temos que decidir como enviar os projetos atualizados para o seu servidor remoto Linux. De forma simples, podemos enviar um ZIP do projeto frontend e um WAR/JAR do projeto backend, ou então, clonar os projetos na máquina servidora e atualizá-los com GIT.

Por ser mais prático, irei fazer a segunda opção.

Logue remotamente no seu servidor Linux. Se a sua máquina também for Linux/Mac, use o SSH no seu terminal. Caso seja Windows, use a interface Putty.

## Preparando o Servidor

Após entrar remotamente, atualize seu repositório de apps do Ubuntu:

```
sudo apt-get update
```

Vamos então iniciar as instalações. Baixe e instale o GIT:

```markup
sudo apt-get install git
git --version (para verificar se está ok)
```

Baixe e instale o node. No momento da escrita deste post, ao tentar instalar direto pelo apt-get, virá a versão 6 do node. Para instalar a versão 8 em diante, devemos configurar o PPA, adicionar o caminho do node 8 e instalar libs extras.

```bash
curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
sudo bash nodesource_setup.sh
sudo apt-get install nodejs
sudo apt-get install build-essential
npm --version (para verificar se está ok)
```

Vamos instalar o servidor de página web para o projeto frontend (vamos configurá-lo mais adiante):

```bash
sudo apt-get install nginx
sudo systemctl status nginx (para verificar se esta ok)
```

Agora, vamos baixar e instalar o JDK 8

```markup
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java8-installer
java -version (para verificar se está ok)
```

E instalar o Maven.

```markup
sudo apt-get install maven
mvn --version (para verificar se está ok)
```

Por fim, vamos então clonar os dois projetos do repositório git para a máquina remota. Estando na home do seu usuário, vamos baixar os dois projetos individualmente:

```bash
git clone <url-harbor-frontend>
git clone <url-harbor-backend>
```

## Mexendo no projeto Frontend

Precisamos deixar o seu projeto pronto para a publicação em produção. Entre na raiz do projeto front e faça:

```bash
npm install
ng build --prod
```

Isso compila, prepara e gera o diretório "dist" com todo o necessário para rodar em produção, já com minificações dos arquivos e outras melhorias.

Bom, com tudo pronto podemos publicar o projeto frontend de duas formas.

A primeira é copiar todo o conteúdo do diretório "dist" do seu projeto para o diretório executor padrão do nginx, que é o "/var/www/html". No entanto, para cada atualização, temos que compilar o projeto e copiar denovo o diretório "dist". Já a segunda forma seria configurar o nginx apontando-o diretamente para o diretório "dist" de dentro do seu projeto, e dessa forma, bastaria baixar a compilar as atualizações que o nginx já está com as páginas atualizadas.

Vamos da segunda forma e para fazer isso edite o conteúdo do arquivo de configuração do nginx.

```
sudo vim /etc/nginx/sites-available/default
```

Colocando estas configurações:

```markup
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root <path-diretorio>/harbor-frontend/dist/harbor-frontend;

        index index.html

        server_name <ip-ou-dominio-externo>;

        location / {
                try_files $uri $uri/ /index.html;
        }
}
```

Vamos a algumas explicações:

- A ideia desse arquivo nginx é configurar portas e caminhos para acesso externo.
- Primeiro dissemos que nossa porta é 80.
- Depois indicamos que essa porta 80 configurada será direcionada internamente para o diretório físico indicado no "root". Veja que esse diretório é aquele "dist" do nosso projeto frontend.
- Apontamos qual é o arquivo index da nossa aplicação.
- Indicamos qual é o IP externo ou domínio que nossa aplicação estará funcionado. Isso é importante para o Angular reconhecer certinho nossos arquivos.
- E indicamos que a localização raiz "/" aponta ao index. Isso também é importante para o Angular funcionar.

Como mudamos um arquivo de configuração, temos que reiniciar o serviço nginx:

```
sudo systemctl restart nginx
```

Pronto! Agora acesse o endereço do seu servidor (IP ou o nome) na porta 80 (porta padrão) e veja as páginas aparecendo. :)

Para atualizar o projeto frontend, basta mexer dentro do projeto, pois o nginx está apontando diretamente para lá. Assim, vá a raiz do projeto front e faça os comando em sequência:

```bash
git pull <url-harbor-frontend> 
npm install
ng build --prod
```

Não é necessário reiniciar o serviço nginx, basta acessar e tudo estará atualizado.

## Mexendo no projeto Backend

No Java, como estamos usando SpringBoot, bastaria criar o JAR do projeto e executá-lo em linha de comando. Mas, vamos fazer isso como um serviço do Linux, podendo iniciar, parar, reiniciar ou ver o status.

Primeiro, vamos construí-lo entrando na raiz do projeto back e fazendo:

```
mvn clean install
```

Será criado o diretório "target" que contém o JAR compilado e pronto para executar. Mas, para rodá-lo, precisamos dar permissão de execução a este arquivo:

```
chmod +x harbor-backend.jar
```

Agora, vamos configurar um serviço Linux apontando para esse JAR utilizando systemd. Para isso, criamos um arquivo dentro de "/etc/systemd" com o nome do nosso projeto e com extensão ".service":

```
vim /etc/systemd/system/harbor-backend.service
```

Com o seguinte conteúdo:

```bash
[Unit]
Description=Harbor Backend Spring Boot
After=syslog.target
After=network.target[Service]
User=harbor
Type=simple

[Service]
ExecStart=/usr/bin/java -jar <path-projeto>/target/harbor-backend.jar
Restart=always
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=harbor-backend

[Install]
WantedBy=multi-user.target
```

Vamos a algumas explicações:

- A ideia central desse arquivo é apontar ao JAR do seu projeto baixado.
- Em "ExecStart" você deve apontar o caminho completo do seu JAR.

Depois de criado o arquivo, reinicie os serviços do Linux:

```
systemctl daemon-reload
```

E agora, podemos usar este comando para ver o status do nosso serviço java:

```
sudo systemctl status harbor-backend
```

Podemos ainda iniciar (start), parar (stop) e reiniciar (restart) caso necessário.

Para atualizar o projeto backend, basta mexer dentro do projeto, pois o serviço criado está apontando diretamente para lá. Assim, vá a raiz do projeto backend e faça os comando em sequência:

```bash
sudo systemctl stop harbor-backend
git pull <url-harbor-backend>
mvn clean install
sudo systemctl start harbor-backend
```

Feito isso seu projeto Java está no ar!! \\o/ ....... mas, temos um problema :(

A porta padrão iniciada no Tomcat pelo SpringBoot é a 8080 (ou a que você configurou no application.properties) e normalmente os servidores não estão com essa porta aberta. Em tese, se tentar acessar seu servidor na porta 8080, retornará um 404, apesar de tudo funcionando corretamente com o Java.

Podemos resolver mexendo nas configurações do seu servidor (dentro da Amazon ou Azure por exemplo) e habilitar a porta 8080. Essa é a forma mais fácil, mas teremos uma porta não padrão pública acessível.

Seria interessante se somente a porta padrão 80 estivesse disponível ao mundo externo (não só questão de segurança, mas alguns clientes do seu site podem ter bloqueios de porta na rede). No entanto, já estamos ocupando a porta 80 com o nosso servidor frontend nginx e não é possível deixar dois aplicativos usando a mesma porta.

Para nossa felicidade, o nginx possui um esquema chamado Proxy Reverso. Com ele, podemos configurar padrões de URL para ser redirecionado internamente para outro endereço (da mesma máquina ou não) ou mesmo um diretório local. Dessa forma, vamos fazer com que tudo que chegue na porta 80 no padrão de URL /backend/ possa ser direcionado localmente para a porta 8080.

Editando o arquivo "/etc/nginx/sites-available/default" vamos adicionar as linhas logo abaixo do fechamento do "location /" (ainda dentro das chaves do "server")

```bash
location /backend/ {
    proxy_set_header Accept-Encoding "";
    proxy_buffering off;
    proxy_pass http://127.0.0.1:8080/;
}
```

Reinicie o serviço nginx e pronto! Ele está redirecionando tudo que chega na porta externa 80 onde na URL contem o padrão "/backend/" para a porta interna 8080. :)

Caso você tenha banco de dados, não esqueça de instalá-lo no servidor e deixá-lo disponível para o seu Java.

## URL Externas

Com o serviço Java e o nginx no ar, agora teremos dois padrões de URL públicas disponíveis:

- www.exemplo-harbor.com.br -> projeto frontend
- www.exemplo-harbor.com.br/backend/ -> redirecionamento interno para tomcat java 8080

Não se esqueça de configurar no seu projeto frontend nas propriedades de produção o endereço backend correto. Outro detalhe é que caso o seu projeto Java possua um contexto configurado (como ao invés de acessar "localhost:8080/" você acessa "localhost:8080/projeto/"), deve ser especificado essa raiz do contexto lá no redirecionamento nginx (na entrada proxy\_pass).

## Automatização de Ambiente

Podemos automatizar os passos de publicação em produção de forma mais avançada configurando um servidor de integração contínua com o Jenkins, onde ele é acionado após um commit do desenvolvedor. O Jenkins faria os passos descritos nesse post sobre atualizar o projeto (git) e executar o comando de deploy (maven e npm).

Podemos ainda melhorar as configurações jogando os projetos front e back em containers Docker, que faria as mesmas coisas mas de forma incremental e padronizada.

Mas diz aí, como você publica seus projetos em produção? Não deixe de conferir [nossas turmas presenciais de Java e Web](https://www.caelum.com.br/todos-os-cursos), além das [aulas de Java na plafatorma Alura](https://www.alura.com.br/cursos-online-programacao/java).
