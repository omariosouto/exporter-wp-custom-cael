---
title: "Usando um banco de dados no serviço RDS da Amazon"
date: "2015-06-23"
author: "lucas.takeshi"
authorEmail: "lucas.takeshi@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nos dias de hoje, é normal encontrar diversas aplicações web que utilizam algum banco de dados para armazenar suas informações. Enquanto o sistema está sendo desenvolvido, é relativamente simples configurar a sua máquina para rodar algum dos bancos de dados, como o MySQL. Assim, basta que a aplicação web, que está rodando localmente, aponte para o banco de dados instalado na sua máquina. No entanto, quando for necessário hospedar a aplicação em um servidor, onde ficará o banco de dados?

Uma solução rápida para este problema é instalar o banco de dados na própria máquina onde estará rodando o servidor de aplicação ou servlet container. Por exemplo, a AWS (Amazon Web Services) oferece o serviço de EC2 (Elastic Compute Cloud), que são máquinas redimensionáveis na nuvem para uso de serviços da web. Desta forma, é possível acessá-las usando ferramentas de acesso remoto através da rede, como o SSH. Então, se a aplicação que está hospedada em um EC2 precisar de um banco, basta fazer o SSH para a máquina e executar os comandos necessários para instalá-lo. Por exemplo, se esta máquina está usando um sistema operacional Ubuntu, é possível instalar o MySQL com essas duas linhas de comando:

\[code language="bash"\] $ sudo apt-get install mysql-server $ sudo apt-get install mysql-client \[/code\]

Assim, fica faltando apenas o arquivo de configuração do banco de dados apontar para o MySQL da EC2. Como eles estão na mesma máquina, esta configuração é similar a utilizada durante o desenvolvimento no seu computador. Ou seja, é possível rodar a aplicação com banco de dados em produção praticamente da mesma forma que é feita localmente. Mas o que acontece se alguém acidentalmente desligar a máquina EC2? Ou se ocorre alguma falha na Amazon e a instância é perdida? Nestes casos, não apenas a aplicação fica fora do ar, como o banco de dados é perdido. Isto ocorre pois, ao desligar uma instância da EC2, tudo que estava salvo no disco efêmero da máquina enquanto ela estava rodando acaba sendo apagado quando a máquina é desligada.

Essa é apenas a ponta do iceberg. Qualquer arquitetura mais robusta vai precisar da separação desses componentes. Assim, mesmo que a instância do servidor seja perdida, o banco de dados ficará intacto. No entanto, por estarem em diferentes lugares, é criado um custo de comunicação entre as informações que são enviadas que não existe quando tudo está na mesma máquina. Inicialmente, este custo pode não ser significativo, porém podem surgir problemas de performance conforme o sistema avança. Por exemplo, queries que devolvem muitos resultados precisarão de mais tempo para transferir a grande quantidade de dados entre as máquinas.

Pensando nisso, a Amazon criou um serviço específico para o gerenciamento de bancos de dados relacionais, conhecido como RDS (Relational Database Service). Desta forma, é possível contratar uma máquina feita especificamente para rodar seu banco de dados a parte do servidor. E como ele foi desenvolvido para ser usado em conjunto com outros serviços web da Amazon, é possível criar redes de acesso de baixa latência entre uma máquina EC2 e um RDS, diminuindo o custo de comunicação. Além disso, por se tratar de um serviço específico para bancos de dados, muitos problemas que eram resolvidos manualmente, como a criação de backups, são tratados automaticamente pelo RDS. No entanto, criar toda essa estrutura é bem mais complexo do que simplesmente contratar os dois serviços.

A primeira coisa a se fazer é escolher a região. Como na Amazon só é possível criar uma rede entre instâncias que estão na mesma região, tudo que for necessário criar a partir de agora terá que estar no mesmo lugar. No entanto, algumas delas não oferecem todos os serviços da Amazon, ou seja, é necessário escolher uma que possua tanto o serviço EC2 quanto o RDS. Além disso, os preços cobrados variam de região para região. A região utilizada para os exemplos a seguir é a Virgínia do Norte.

Depois escolher onde ficará a máquina, o próximo passo é criar uma forma em que somente pessoas autorizadas conseguirão acessá-la remotamente via SSH. Para isto utiliza-se o serviço de chaves privadas. Assim, somente quem tiver a chave poderá se conectar com o servidor. Para criar esta chave, são necessários os seguintes passos:

1. No dashboard da AWS, abrir o console da Amazon EC2.
2. Clicar em **Key Pairs > Create Key Pair**.
3. Escolher o nome da chave no campo **Key pair name.[![criandoChave1](https://blog.caelum.com.br/wp-content/uploads/2020/05/criandoChave1-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/criandoChave1.png)**
4. Clicar em **Create**. Ela será automaticamente baixada no computador depois que for criada.
5. Configurar as permissões da chave no computador, que deve ser somente de leitura para o usuário. Por exemplo, para colocar esta permissão na chave acima no Linux, basta fazer o comando:

\[code language="bash"\] chmod 400 minha-chave.pem \[/code\]

No entanto, apenas a chave não é suficiente para conectar na máquina remotamente por SSH.  Para isto, é preciso liberar os endereços por onde será possível acessá-la, seja por SSH como também por HTTP para os usuários da aplicação. Estes endereços são feitos a partir do serviço VPC da Amazon, que constroi a rede utilizada pelas máquinas. Para criá-la, basta:

1. No dashboard do AWS, abrir o console do serviço VPC.
2. Clicar em **Start VPC Wizard.**
3. Em **Select a VPC Configuration**, verificar que a opção **Single Public Subnet** esteja selecionada.
4. Clicar em **Select**.
5. Em **VPC with a Single Public Subnet**, fazer o seguinte:
    1. Escolher um nome para o VPC em **VPC name**.
    2. Em **Availability Zone**, escolher a primeira zona que aparecer na lista.
    3. Em **Subnet name**, trocar de **Public subnet** para **Public subnet 1**.[![vpc4](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc4-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc4.png)
    4. Clicar em **Create VPC**.
    5. Clicar em **OK** na página de confirmação.
6. Voltar para o console do VPC e clicar em **Route Tables**. Na linha cujo valor na coluna **Main** for um **Yes**, clicar no campo da coluna **Name**, digitar o nome **Main** e depois apertar Enter. Na outra linha, cuja coluna **Main** aparece **No**, colocar o nome **Custom**.[![vpc10](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc10-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc10.png)
7. Agora será necessário criar uma segunda sub-rede para o seu servidor de aplicação:
    1. No menu de navegação, clicar em **Subnets > Create Subnet.**
    2. Em **Name tag**, colocar o nome **Public subnet 2.**
    3. Em **VPC**, selecionar a VPC criada acima.
    4. Em **Availability Zone**, selecionar a primeira zona da lista.
    5. Em **CIRD block**, digitar **10.0.1.0/24**.[![vpc14](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc14-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc14.png)
    6. Clicar em **Yes, Create**.
    7. Na tabela de sub-redes, selecionar a **Public subnet 2.** No menu que aparece logo abaixo, clicar na aba **Route table** e depois em **Edit.** Em **Change to**, **s**elecionar o tabela de rotas **Custom** e clicar em **Save**.

Com a VPC e as sub-redes para acesso público criadas, a próxima etapa é criar as sub-redes por onde o EC2 e o RDS irão se conectar. Os passos são parecidos com a etapa anterior:

1. No menu de navegação, clicar em **Subnets > Create Subnet.**
2. Em **Name tag**, colocar o nome **Private subnet 1.**
3. Em **VPC**, selecionar a VPC criada anteriormente.
4. Em **Availability Zone**, selecionar a primeira zona da lista.
5. Em **CIRD block**, digitar **10.0.2.0/24.[![vpc19](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc19-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc19.png)**
6. Clicar em **Yes, Create**.
7. Clicar novamente em **Create Subnet**.
8. Em **Name tag**, colocar o nome **Private subnet 2**.
9. Em **VPC**, selecionar novamente a VPC.
10. Em **Availability Zone**, selecionar a segunda zona da lista.
11. Em **CIRD block**, digitar **10.0.3.0/24**.**[![vpc21](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc21-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/vpc21.png)**
12. Clicar em **Yes, Create**.

Neste momento, já temos as chaves privadas para acesso via SSH e os endereços e sub-redes por onde serão feitos os acessos externos e entre as intâncias EC2 e RDS. Agora falta liberar o acesso externo via SSH ou HTTP nos grupos de segurança. Para isso:

1. No dashboard do AWS, abrir o console do serviço EC2.
2. Clicar em **Security Group > Create Security Group**.
3. Escolher um nome e uma descrição para o grupo de segurança.
4. Selecionar a VPC criada anteriormente.
5. Na tabela **Inbound**, será necessário adicionar duas regras:
    1. Clicar em **Add Rule**, selecionar em **Type** a opção **SSH**. Caso apenas um computador específico possa acessar o EC2, o campo **Source** deve ser preenchido com o **IP público** deste computador. Caso contrário, o valor será **0.0.0.0/0**, ou seja, qualquer um poderá realizar um SSH nesta instância.
    2. Clicar novamente em **Add Rule e** selecionar a opção **HTTP** em **Type**.[![ec2sg6](https://blog.caelum.com.br/wp-content/uploads/2020/05/ec2sg6-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/ec2sg6.png)
    3. Clicar em **Create**.

Depois de preparar todos esses recursos, o próximo passo é criar uma instância da EC2:

1. No dashboard do AWS, abrir o console do serviço EC2.
2. Clicar em **Instances > Launch Instances.**
3. Em **Choose an Amazon Machine Image**, escolher qual tipo de sistema operacional você deseja. O sistema escolhido neste caso foi o **Ubuntu Server 14.04 LTS (HVM), SSD Volume Type**.
4. Em **Choose an Instance Type**, selecionar o tipo de instância necessário para a sua aplicação web. Neste exemplo, a opção foi o **t2.micro**. Para saber sobre cada um dos tipos a acesse a [lista de instâncias da Amazon](http://aws.amazon.com/pt/ec2/instance-types/).
5. Clicar em **Next: Configure Instance Details**. Nesta página, faça o seguinte:
    1. Escolher a sua VPC em **Network** e selecionar uma das **sub-redes públicas** em **Subnet.**
    2. Verificar que o **Auto-assign Public IP** está **Enable**, caso contrário a instância não terá um endereço IP público.[![ec2Instance6](https://blog.caelum.com.br/wp-content/uploads/2020/05/ec2Instance6-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/ec2Instance6.png)
    3. Clicar em **Review and Launch**. Se você precisar especificar qual o tipo de **root volume**, faça a sua seleção e clique em **Next.**
6. Em **Review Instace Launch**, clicar em **Edit security group**. Em **Configure Security Group**, clicar em **Select a existing group** e selecionar o grupo de segurança criado acima.![ec2Instance10](https://blog.caelum.com.br/wp-content/uploads/2020/05/ec2Instance10-1024x518.png)
7. Clicar em **Review and Launch**.
8. Em **Review Instance Launch**, clicar em **Launch**.
9. Na caixa **Select an existing key pair or create a new key pair**, selecionar **Choose an existing key pair,** passar chave criada anteriormente e clicar no checkbox de confirmação.[![ec2Instance13](https://blog.caelum.com.br/wp-content/uploads/2020/05/ec2Instance13-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/ec2Instance13.png)
10. Clicar em **Launch Instances**.
11. Voltar para o painel de navegação, clicar em **Instances** e aguardar o status da sua instância mudar de pending para running.

Neste momento, a máquina EC2 está pronta para ser usada, então falta apenas o banco de dados no RDS. O processo é similar ao EC2, ou seja, criar um grupo de segurança e depois a instância. Para criar o grupo de segurança:

1. Abrir o console do serviço EC2.
2. Clicar em **Security Group > Create Security Group**.
3. Escolher um nome e uma descrição para o grupo de segurança.
4. Selecionar a VPC criada anteriormente.
5. Na tabela **Inbound**, clicar em **Add Rule**. Então selecionar **MySQL** em **Type**. Em **Source**, será necessário escolher o **Custom IP**. Para isto, digite o nome do grupo de segurança do EC2 e selecione este grupo quando ele aparecer.[![rdssg6](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdssg6-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdssg6.png)
6. Clicar em **Create.**

Por fim, falta criar a instância do RDS:

1. Abrir o console do serviço Amazon RDS.
2. Clicar em **Subnet Groups > Create DB Subnet Group** e siguir os seguintes passos:
    1. Escolher um nome e uma descrição.
    2. Selecionar a VPC.
    3. Em **Availability Zone**, selecionar a primeira zona disponível. Então, em **Subnet ID**, selecionar a sub-rede privada e clicar em **Add**.
    4. Em **Availability Zone**, selecionar a segunda zona disponível. Então, em **Subnet ID**, selecionar a sua sub-rede privada e clicar em **Add**.[![rdsInstance4](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance4-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance4.png)
    5. Clicar em **Create**.
3. Ainda no console do RDS, clicar em **Instances > Launch DB Instance**.
4. Em **Select Engine**, selecionar o banco **MySQL** e clicar em **Select**.
5. Em **Do you plan to use this database for production purposes**, decidir se a aplicação precisará do plano [Multi-AZ Deployment](http://aws.amazon.com/pt/rds/details/multi-az/). Neste exemplo, a opção foi por não utilizá-lo.
6. Clicar em **Next Step**.
7. Em **Specify DB Details**, faça:
    1. Manter a licença e versão do banco padrões.
    2. Em **DB Instance Class** selecionar o tipo de instância do RDS. Neste caso, foi escolhido o **db.t2.micro**. Para saber sobre os tipos de instância e seus preços, acesse a [tabela de preços do RDS](http://aws.amazon.com/pt/rds/pricing/). 
    3. Em **Multi-AZ Deployment**, selecionar se será usado o plano. Neste exemplo, foi escolhido o **No.**
    4. Selecionar o **General Purpose (SSD)** em **Storage Type** e definir o tamanho do banco em **Allocated Storage**.[![rdsInstance12](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance12-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance12.png)
    5. Em **DB Instance Identifier**, escolher um nome para a instância.
    6. Em **Master Username**, digitar o nome do usuário do banco.
    7. Escolher a senha deste usuário em **Master Password** e **Confirm Password**.[![rdsInstance13](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance13-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance13.png)
    8. Clicar em **Next Step**.
8. Em **Configure Advanced Settings**, faça:
    1. Em **Network & Security**, selecionar a VPC criada anteriormente e selecionar o grupo de segurança do RDS em **VPC Security Groups.![rdsInstance15](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance15-1024x518.png)**
    2. Em **Database Options**, escolher um nome para o **Database Name**.[![rdsInstance16](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance16-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance16.png)
    3. Clicar em **Launch DB Instance**.
    4. Quando a mensagem que a instância está sendo criada aparecer, clicar em **View Your DB Instances**.
    5. Aguardar o status mudar de creating para available.

Com a instância RDS pronta é possível usá-la a partir da máquina EC2. Para isto, primeiro será necessário fazer um SSH para a máquina:

\[code language="bash"\]

ssh -i minha-chave.pem ubuntu@52.7.233.209

\[/code\]

Como neste exemplo o banco é o MySQL, o servidor precisa do mysql-client instalado para conectar com o banco:

\[code language="bash"\]

sudo apt-get install mysql-core-client-5.6

\[/code\]

Agora o acesso ao banco pode ser feita de forma similar ao acesso a um banco MySQL local. A diferença é que será necessário passar o endereço e a porta do banco. Estas informções podem ser obtidas no campo **Endpoint** presente nas configurações da instância RDS no dashboard da AWS. O que aparece antes dos dois pontos corresponde ao endereço, enquanto que a porta aparece depois.[![rdsInstance20](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance20-1024x518.png)](https://blog.caelum.com.br/wp-content/uploads/2020/05/rdsInstance20.png)

Assim, o comando para acessar o banco MySQL criado acima é:

\[code language="bash"\]

mysql -h meubancords.cyga6r27ef9x.us-east-1.rds.amazonaws.com -P 3306 -u root -p

\[/code\]

Criar toda esta estrutura é relativamente trabalhoso, já que demanda muitos passos. Por outro lado, o sistema não corre mais o risco de perder o banco junto com o servidor. E você já usou estes serviços da Amazon? De que formas você hospeda a sua aplicação?
