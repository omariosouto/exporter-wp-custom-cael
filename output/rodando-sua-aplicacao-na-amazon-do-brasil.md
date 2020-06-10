---
title: "Rodando sua aplicação na Amazon do Brasil"
date: "2011-12-20"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Desde o [início de nosso trabalho com o cloud em 2009](https://blog.caelum.com.br/o-cloud-computing-e-inevitavel/) temos [investido tempo e pesquisa](https://blog.caelum.com.br/vivendo-no-cloud-a-infraestrutura-externa-da-caelum-em-11-solucoes/) na utilização do cloud como plataforma para diminuir custos (e trabalho!) e potencializar produtos, através de [diversos vendors](https://blog.caelum.com.br/screencast-sua-app-no-cloud-com-openshift/).

Com a Amazon não é diferente: [nosso sistema de ensino online](https://blog.caelum.com.br/ha-bom-aprendizado-em-cursos-online/) está deployado lá. [Com o lançamento de grande parte da sua plataforma no Brasil](http://aws.amazon.com/about-aws/whats-new/2011/12/14/announcing-the-south-america-sao-paulo-region/), a Amazon se torna uma opção ainda mais interessante de onde ter sua aplicação. Vamos ver passo a passo como fazer um deploy e aproveitar das novas vantagens da Amazon por aqui.

Primeiro nos [logamos no painel AWS Manager](http://aws.amazon.com/console/), e escolhemos a região da América do Sul:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen7.png "screen7")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen7.png)

Depois escolhemos a imagem que usaremos para a máquina, no nosso caso [uma instalação linux limpa](http://aws.amazon.com/amis/4157), mantida pela própria Amazon. [Existem centenas de instalações possíveis](http://aws.amazon.com/amis) (de linux e windows) que podem ser buscadas no market da amazon.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen8.png "screen8")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen8.png)

Escolhemos então a potência da máquina e em qual _availability zone_ ela ficará. Comecemos com "`sa-east-1a`", que indica América do Sul:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen9.png "screen9")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen9.png)

Vale lembrar que uma aplicação realmente com alta disponibilidade precisaria ser deploiada em duas ou mais avaliability zones, [mesmo sendo pequena a chance de uma zona cair](http://www.infoq.com/news/2011/04/Amazon-EC2-Outage-Explained).

Nas duas próximas telas escolhemos detalhes do Kernel e do RAM, além de possíveis tags que queremos dar para a máquina (em geral para trabalhar através dos web services). No nosso caso deixaremos os defaults.

Quando uma nova máquina é criada, precisamos nos logar nela e, para isso, precisamos de uma chave privada de acesso. A amazon pergunta qual o nome que devemos dar a chave, criando e permitindo que baixemos o arquivo. Note que a chave contida nesse arquivo é importantíssima e qualquer um de sua posse poderá ter acesso a máquina.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen13.png "screen13")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen13.png)

Por fim, escolhemos quais portas desejamos deixar abertas. No caso de uma aplicação Java com Jetty ou Tomcat, adicionamos a porta padrão 8080:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen21.png "screen21")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen21.png)

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen22.png "screen22")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen22.png)

Isso mesmo, nada de complicados usuários e senhas. Nada de gerenciá-los através de ferramentas de webadmin desatualizadas. Nada de complicadas regras e configuração de firewalls. A Amazon então permite confirmar todas as opções, e o resultado é a máquina rodando:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/maquina_amazon_rodando_2.png "maquina_amazon_rodando_2")](https://blog.caelum.com.br/wp-content/uploads/2011/12/maquina_amazon_rodando_2.png)

A máquina possui um IP interno (para ser acessado de dentro da Amazon) e um DNS público, através do qual acessaremos a mesma de fora da Amazon. O DNS público pode ser visto ao clicar no nome da máquina:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen20.png "screen20")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen20.png)

Vamos acessar a máquina via ssh:

\[code\] chmod 700 caelum-exemplo.pem ssh -i caelum-exemplo.pem ec2-user@ec2-177-71-153-49.sa-east-1.compute.amazonaws.com \[/code\]

Resultando em:

\[code\] The authenticity of host 'ec2-177-71-153-49.sa-east-1.compute.amazonaws.com (177.71.153.49)' can't be established. RSA key fingerprint is 34:a6:de:34:88:fd:a1:73:ae:c5:03:f1:ed:8e:2f:96. Are you sure you want to continue connecting (yes/no)? yes Warning: Permanently added 'ec2-177-71-153-49.sa-east-1.compute.amazonaws.com,177.71.153.49' (RSA) to the list of known hosts.

\_\_| \_\_|\_ ) \_| ( / Amazon Linux AMI \_\_\_|\\\_\_\_|\_\_\_|

See /usr/share/doc/system-release/ for latest release notes. There are 16 security update(s) out of 24 total update(s) available \[/code\]

Agora fazemos download do jetty, descompactamos e rodamos o servidor (se preferir, utilize o Tomcat):

\[code\] wget http://download.eclipse.org/jetty/8.1.0.RC1/dist/jetty-distribution-8.1.0.RC1.tar.gz tar zxf jetty-distribution-8.1.0.RC1.tar.gz jetty-distribution-8.1.0.RC1/bin/jetty.sh start \[/code\]

O servidor irá inicializar e você logo receberá a mensagem de que ele está ouvindo a porta 8080.

\[code\] 2011-12-15 17:54:27.682:INFO:oejs.AbstractConnector:Started SelectChannelConnector@0.0.0.0:8080 STARTING \[/code\]

Pronto, acesse seu jetty:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen23.png "screen23")](https://blog.caelum.com.br/wp-content/uploads/2011/12/screen23.png)

E se desejarmos atualizar a imagem da máquina, com atualizações de seguranca? Se sua máquina não armazena o banco, mas sim somente arquivos voláteis (de fácil reinstalação), basta terminá-la e criar uma nova. Sistema operacional atualizado! Para manter dados voláteis, você pode usar o [RDS da Amazon](http://aws.amazon.com/rds/) para seu banco Mysql ou Oracle (ou outras máquinas para bancos não relacionais) e manter um script de configuração (usar técnicas de configuration management).

Mais: com apenas alguns cliques é muito fácil de [adicionar um load balancer através de sticky sessions](http://aws.amazon.com/pt/elasticloadbalancing/), configurar ips fixos, etc.

O que você está esperando para fazer seu primeiro deploy por lá?
