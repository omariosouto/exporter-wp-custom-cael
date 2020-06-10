---
title: "Mais git com o hub: a linha de comando do Github"
date: "2015-05-05"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Utilizar o git para controle de versão e Github para hospedagem desse código é uma das grandes certezas em boa parte das empresas e projetos de hoje em dia. O Github sempre procura inovar e oferecer novos recursos, atalhos e etc, porém, a necessidade de abrir o navegador e interagir com sua interface gráfica em alguns momentos pode ser um pouco improdutivo. Quer um exemplo simples? Pense nos passos que precisamos fazer sempre que queremos criar um novo repositório. Abrir o site, clicar no ícone de + no canto da tela, clicar em _New Repository_, escolher o nome e finalmente clicar em concluir.

![Screen Shot 2015-04-17 at 22.50.37](https://blog.caelum.com.br/wp-content/uploads/2050/04/Screen-Shot-2015-04-17-at-22.50.37.png)

Feito isso, o repositório estará criado, mas ainda será necessário associá-lo ao nosso projeto local. Por exemplo, considerando que criamos um projeto chamado **exemplo** no Github, e já temos o projeto com o mesmo nome criado em nosso workspace local, faríamos:

\[code language="bash"\] git remote add origin git@github.com:Turini/post\_sample.git git push -u origin master \[/code\]

É um processo bem simples e o próprio Github mostra as linhas que você precisará executar localmente, mas são vários passos envolvendo o terminal e browser para uma tarefa tão trivial, não acha? Pra nos ajudar com essas e outras tarefas rotineiras existe o [hub, a ferramenta de linha de comando oficial do github](https://github.com/github/hub). Ela nos oferece uma serie de features e comandos extras deixando nosso trabalho com Github mais simples e produtivo.

### Instalando e configurando

Se você usa mac, pode fazer o download via HomeBrew (brew install hub). Caso contrário, poderá baixar [o binário da última versão](https://github.com/github/hub/releases) compilada e adicionar no seu path, ou clonar o repositório e rodar um script de build. As instruções de instalação estão [disponíveis no README](https://github.com/github/hub#installation) do projeto no github. Para confirmar que a instalação funcionou, basta executar o comando **hub version**. A saída será parecida com:

\[code language="bash"\] git version 1.7.6 hub version 2.2.0 \[/code\]

### Tudo mais simples com hub

Você perceberá que esse plugin não oferece tantas features assim, mas as poucas que oferece, fazem seu uso valer a pena. Vou mostrar uma seleção pessoal das minhas favoritas aqui, mas você encontrará a lista completa no [README do projeto](https://github.com/github/hub#commands) ou executando o comando **hub help**.

### Criando um novo projeto

Lembra de todos os passos necessários para criar um projeto no github? Abrir o browser, dar vários clicks, etc. Com hub basta você acessar a pasta do projeto local que pretende hospedar -- ou criar um novo, utilizando o `git init nome-do-projeto` --, e depois executar:

\[code language="bash"\] hub create \[/code\]

Tudo pronto, além de criar o repositório remoto no seu Github, ele vai fazer o **remote add** adicionando esse repositório como origem. Legal, não é? Se você quiser testar, basta fazer:

\[code language="bash"\] git init exemplo\_hub cd exemplo\_hub hub create \[/code\]

Quase que instantaneamente ele exibirá a mensagem:

\[code language="bash"\] Updating origin created repository: Turini/exemplo\_hub \[/code\]

Só isso. Você ainda pode utilizar o comando **hub browse**, que abrirá a página do novo projeto em seu navegador default:

![Screen Shot 2015-04-17 at 22.56.14](https://blog.caelum.com.br/wp-content/uploads/2050/04/Screen-Shot-2015-04-17-at-22.56.14.png)

Também é possível criar o projeto em outro repositório que você tenha acesso, passando o `NOME_DO_REPO/NOME_DO_PROJETO`. Posso, por exemplo, executar o seguinte comando para criar um projeto chamado exemplo\_hub no repositório da caelum:

\[code language="bash"\] hub create caelum/exemplo\_hub \[/code\]

### Outras facilidades: Clone e Fork

Fazer o clone de um projeto é uma necessidade essencial, fazemos isso o tempo todo! Para clonar o repositório do VRaptor 4 em sua máquina, por exemplo, você faria algo como:

\[code language="bash"\] git clone git@github.com/caelum:vraptor4.git \[/code\]

Com hub o processo é um pouquinho menos verboso. Repare:

\[code language="bash"\] hub clone caelum/vraptor \[/code\]

É um detalhe pequeno, mas bastante conveniente. O ganho maior está realmente no processo de fork, onde tradicionalmente somos obrigados a abrir o navegador, acessar o repositório do Github, clicar em fork, e depois fazer um clone de nosso fork ou atualizar o endereço remoto do clone que fizemos do projeto original.

![Screen Shot 2015-04-17 at 22.59.54](https://blog.caelum.com.br/wp-content/uploads/2050/04/Screen-Shot-2015-04-17-at-22.59.54.png)

Que tal no lugar disso fazer apenas um **hub fork**? Basta acessar a pasta do projeto que você clonou, executar esse comando e pronto. Além de criar o fork em sua conta do Github, o hub também vai atualizar a origem remota do repositório para você.

### Pull requests e muito mais!

Depois de fazer as alterações no seu fork, você provavelmente fará um pull-request para o projeto original. Como isso é feito atualmente? Via interface do Github. Novamente: abrir o navegador, ir até sua branch, clicar em pull-request, mudar a origem para o repositório original e por fim digitar o titulo e a descrição. Com hub você executa um simples **hub pull-request**. Um editor será exibido e você digitará apenas o título e opcionalmente uma descrição, separados por uma quebra de linha.

\[code language="bash"\] Título desse meu pull request

Alguma descrição opcional

\# Requesting a pull to Turini:master from Turini:exemplo # # Write a message for this pull request. The first block # of text is the title and the rest is description. \[/code\]

Caso o pull request não tenha uma descrição, você ainda pode utilizar a opção -m, passando o título no próprio commando:

\[code language="bash"\] git pull-request -m "Título do meu pull request" \[/code\]

Além disso você pode passar outros parâmetros, como -b (destino) e -h (origem)

\[code language="bash"\] git pull-request -m "nova feature" -b github:hub -h Turini:feature \[/code\]

Existem diversos comandos além dos comentados aqui no post. Você pode ver um pouco mais no repositório do projeto:

[https://github.com/github/hub#commands](https://github.com/github/hub#commands)

Comecei a utilizar essa ferramenta há pouco tempo, mas já senti bastante ganho de produtividade nas tarefas simples do meu dia a dia. E você, que ferramentas tem utilizado para otimizar sua produtividade?
