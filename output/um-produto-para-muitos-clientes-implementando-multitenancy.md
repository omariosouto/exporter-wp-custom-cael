---
title: "Um produto para muitos clientes: implementando multitenancy"
date: "2010-08-23"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

São diversos as aplicações web disponíveis, como [quadros eletrônicos](http://pivotaltracker.com), [sistemas de tracking](http://lighthouse.com), [email e aplicações para empresas](http://www.google.com/enterprise/), ou até mesmo [controle de clientes e vendas](http://salesforce.com). Eles até ganharam um pomposo nome dentro do cloud computing: Software as a Service (SAAS).

O que essas aplicações possuem em comum? Todas elas atendem diversos clientes sem que um tenha conhecimento da existência dos outros.

Em [um post recente no Tectura.com.br](http://www.tectura.com.br/topics/abordagens_de_multitenant) foram discutidas vantagens e desvantagens de diversas abordagens para produtos com a necessidade de suportar mais de um tenant.

A [Microsoft categoriza três tipos de abordagens](http://msdn.microsoft.com/en-us/library/aa479086.aspx#mlttntda_topic2) dependendo do nível de compartilhamento de recursos entre os clientes e apresente um relatório onde analisa os custos contra a segurança.

Em um extremo, nada é compartilhado entre cada cliente: para cada nova conta criada dentro de seu serviço, é criada uma nova máquina na cloud e uma instalação limpa é executada, com seu próprio banco.

\[caption id="attachment\_3057" align="alignnone" width="300" caption="Nessa abordagem o processo de autorização é automático, um cliente é criado através da instalação automática de uma nova máquina virtual e os recursos são compartilhados se o ambiente for uma cloud."\][![Dois servidores, dois bancos](https://blog.caelum.com.br/wp-content/uploads/2010/08/dois-dois-300x152.png "Dois servidores, dois bancos")](https://blog.caelum.com.br/wp-content/uploads/2010/08/dois-dois.png)\[/caption\]

Na outra ponta, tudo é compartilhado: novos clientes são inseridos no mesmo conjunto de máquinas e a instalação é feita através de um simples insert no banco, adicionando um novo cliente. Qual abordagem escolher?

\[caption id="attachment\_3060" align="alignnone" width="300" caption="A autorização é feita programaticamente na aplicação, os recursos são compartilhados entre todas elas, escala-se tipicamente através do uso de load balancers e replicação master/slave e a cada novo cliente basta executar um insert no banco."\][![Um servidor, um banco](https://blog.caelum.com.br/wp-content/uploads/2010/08/um-um-300x150.png "Um servidor, um banco")](https://blog.caelum.com.br/wp-content/uploads/2010/08/um-um.png)\[/caption\]

Em qualquer abordagem onde o banco seja compartilhado por diversas empresas, precisamos garantir a segurança dos dados de cada uma, para que nenhum acesso indevido ocorra, trazendo preocupações de autorização para dentro do código de nossa aplicação. Caso seja criado para cada cliente um banco em uma máquina no cloud, essa questão fica concentrada em um único ponto da arquitetura, a segurança está implícita.

\[caption id="attachment\_3058" align="alignnone" width="300" caption="Diversos servidores e um banco implica em clientes não afetarem uns aos outros, um controle programático de autorização e novos clientes são criados a partir da instalação de um novo contexto web."\][![Dois servidores, um banco](https://blog.caelum.com.br/wp-content/uploads/2010/08/dois-um-300x149.png "Dois servidores, um banco")](https://blog.caelum.com.br/wp-content/uploads/2010/08/dois-um.png)\[/caption\]

Ao mesmo tempo, o processo de customização de seu serviço por cliente também é afetada de acordo com o nível de compartilhamento de dados entre eles. Na abordagem onde os clientes compartilham o mesmo servidor web, a customização é uma preocupação de nossa aplicação, enquanto ao utilizarmos aplicações web distintas para cada cliente, podemos facilmente separar customizações por instância, por deploy efetuado.

A escalabilidade é afetada pois compartilhar recursos em memória na camada web, [entre eles dados cacheados do banco ou filas](https://blog.caelum.com.br/escalando-sistemas-com-solucoes-nosql/), permitem diminuir o tempo de processamento ou de latência, aumentando o número de requisições suportados.

O processo de escalar também está diretamente ligado: quando uma máquina não aguentar mais as requisições, será levantada uma outra máquina que funcionará em cluster para um, n ou todos os clientes?

Outro fator importante é como limitar o uso do serviço de acordo com as regras contratadas pelo cliente e controlar o dano que um pode causar a outros. Em um pico de uso por parte dos usuários de um cliente, [ativamos o chargeback](http://www.virtualizationconference.com/node/723565), dependendo do tipo de serviço que é prestado. Implementações comuns de chargeback em cloud ([baseados em virtualização](http://www.vmware.com/virtualization/virtual-machine.html)) permitem um pico de consumo temporário ou até mesmo mover a aplicação de uma máquina para outra sem que cliente algum perçeba o que está acontecendo.

\[caption id="attachment\_3058" align="alignnone" width="300" caption="Diversos servidores e um banco implica em clientes não afetarem uns aos outros, um controle programático de autorização e novos clientes são criados a partir da instalação de um novo contexto web."\][![Dois servidores, um banco](https://blog.caelum.com.br/wp-content/uploads/2010/08/dois-um-300x149.png "Dois servidores, um banco")](https://blog.caelum.com.br/wp-content/uploads/2010/08/dois-um.png)\[/caption\]

Ao invés daqueles usuários atrapalharem a performance de outros clientes, é alocado memória e processador distintos dos atuais para ele.

A simplicidade do código é um dos fatores mais afetados pela escolha feita: um código que sabe da existência de múltiplos clientes está diretamente ligado a uma chave que identifica o cliente atual. E essa ligação se reflete por todos os lados do código, uma vez que o comportamento da aplicação em geral depende dele: uma chave estrangeira, um relacionamento, que vai permear toda a aplicação.

Caso a aplicação implemente o suporte a multi tenant através de instalções web distintas, com configurações para cada cliente apontando para um bancos distintos, o código fica mais simples e fácil de manter, uma vez que não é necessário se preocupar com a existência de outros clientes.

Por fim, uma preocupação que surge para a empresa provedora dos serviços é de como agrupar os dados existentes em todos os clientes e gerar relatórios de administração e estatísticas que permitam a melhora do serviço prestado. Quando possuimos um único banco, escalado através de master slave ou múltiplos nós, basta executarmos queries longas (sql ou não), mas se o sistema estiver distribuido entre diversos bancos sem ligação entre si, [um processo de batch deve rodar para agrupar os dados e permitir o consumo posterior pelas ferramentas de BI](http://cloudcomputing.sys-con.com/node/1086696).

[É importante analisar todos esses pontos antes de tomar a decisão](https://blog.caelum.com.br/entao-voce-quer-ser-um-arquiteto-java/) se o controle de espaço de aplicação por cliente será feito no nosso código, na camada web, no banco ou em algum outro ponto. Como discutimos bastante [no curso de arquitetura](http://www.caelum.com.br/curso/fj-91-arquitetura-design-projetos-java/), toda e qualquer decisão implica em um tradeoff: o importante é saber o que está sendo trocado e qual é o impacto disso.
