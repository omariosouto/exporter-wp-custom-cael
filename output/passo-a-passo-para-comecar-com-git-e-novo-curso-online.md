---
title: "Como instalar o GIT e novo curso online"
date: "2012-01-17"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando iniciamos com desenvolvimento profissional, uma das primeiras perguntas que surge é: "onde os arquivos desse projeto ficarão armazenados?". É necessário, principalmente quando trabalhamos em conjunto com outros desenvolvedores, que o projeto esteja armazenado em um local acessível a todos os membros da equipe, com as respectivas permissões para que os mesmos sejam alterados, para que novos arquivos sejam criados e que arquivos existentes possam ser modificados e excluídos.

Tendo em vista esse cenário, podemos definir que qualquer computador, numa rede onde todos os membros da equipe tenham acesso, é candidato a "local de armazenamento do projeto". Mas e se ocorrer algum problema com esse computador?

Então podemos pensar em armazenar os arquivos do projeto em um servidor, com redundância de dispositivos de armazenamento e backups constantes. Uma pasta compartilhada na rede em uma máquina mais segura do que uma estação de trabalho torna-se uma opção melhor. Já podemos chamar esse local de **repositório de código**?

Apesar dessa segurança adicional, ficamos um pouco restritos quanto a recuperar arquivos excluídos, não temos como desfazer alterações no nosso projeto seletivamente e também não temos informações suficientes sobre o projeto para saber "qual versão desse arquivo eu devo recuperar do backup?". Podemos criar um histórico (_log_) textual e adicioná-lo como parte do projeto. Agora perceba que ficamos com um ambiente com requisitos demais para um simples "local de armazenamento de projeto"?

Pensando nesse problema foram criadas ferramentas de **controle de versão**, e [nosso novo curso online](http://www.caelum.com.br/curso/online/git/) apresentará uma das mais utilizadas hoje em dia, o **Git**, que ainda possui a vantagem de ser distribuído, em relação ao CVS e SVN. O Git é utilizado por muitos projetos de código aberto, podemos citar o [kernel do Linux](http://git.kernel.org/), o [Rails](https://github.com/rails/rails) e o [Hibernate](https://github.com/hibernate/hibernate-orm) como exemplos. É possível, porém, utilizá-lo para projetos privados com restrições e permissões de acesso.

Uma grande vantagem do Git é a existência de ferramentas comerciais de hospedagem de código na Web. A maior provedora de hospedagem de repositórios Git, abertos ou privados, é o [Github](http://www.github.com/). Para projetos aberto, ele é gratuito. A Caelum hospeda [seus projetos dentro do github](http://www.github.com/caelum), que por sua vez hospeda toda sua aplicação no cloud. Dessa forma, para você começar a utilizar o git, não é necessário instalar o seu servidor. Vamos então instalar o cliente.

O Git é uma ferramenta baseada em linha de comando, ou seja, realizamos as operações de controle dos arquivos pelo prompt de comando. Se você é adepto de ferramentas gráficas não se preocupe, existem algumas opções para facilitar seu trabalho e plugins para o Eclipse.

Durante [o curso de repositórios remotos e Git](http://www.caelum.com.br/curso/online/git/) aprenderemos outros comandos que o Git nos fornece para solucionar diversos problemas desde o trabalho básico de criação de um repositório, commits, branches, conflitos, como trabalhar no repositório sem conexão com a internet, trabalhar em diferentes implementações simultaneamente num mesmo projeto, modificação de um arquivo por mais de uma pessoa simultaneamente no projeto e tratar os conflitos, entre outros.

## Download e instalação do Git

A instalação do Git é diferente para cada sistema operacional. A seguir, mostraremos como todo o processo é feito em cada um deles, para depois iniciarmos com os comandos básicos.

## Windows

<iframe src="http://player.vimeo.com/video/34909539?title=0&amp;byline=0&amp;portrait=0" width="541" height="304" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

Você pode acompanhar o processo de instalação do Git no Windows no vídeo acima ou os passos a seguir.

Para instalar o Git, iniciaremos acessando a página [http://code.google.com/p/msysgit/](http://code.google.com/p/msysgit/) e baixando o instalador do msysgit. O instalador da versão mais recente, no momento, é o `Git-1.5.6.1-preview20080701.exe`. Uma vez baixado, basta executar o programa e seguir as instruções.

Na primeira tela de boas vindas, apenas clique em _Next_ e, na próxima tela, indique o diretório onde deseja instalar o Git em seu computador.

Em seguida, indique quais utilitários serão instalados junto do Git. Marque todas as opções, exceto _git-cheetah shell extension_. A seguir, defina o nome do menu do Git para o menu _Iniciar_ do Windows, podendo deixar o valor padrão.

O msysgit instala por padrão em seu computador um programa próprio chamado Git Bash, onde é possível manipular os repositórios de arquivos que trabalharemos no futuro. Caso você prefira, é possível usar o próprio prompt do Windows. Basta na próxima janela marcar a opção _Run Git from the Windows Command Prompt_, como indicado na imagem a seguir:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-windows-5.png "instalacao-windows-5")](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-windows-5.png)

Na janela a seguir, indique que queremos que as quebras de linha sejam consideradas da mesma maneira, independente do sistema operacional trabalhado. Para isso, marque a primeira opção:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-windows-6.png "instalacao-windows-6")](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-windows-6.png)

Nesse instante, a cópia dos arquivos será feita para o computador, e em seguida a mensagem de que a instalação foi finalizada aparecerá.

## MacOS

<iframe src="http://player.vimeo.com/video/35134612?title=0&amp;byline=0&amp;portrait=0" width="541" height="304" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

Você pode acompanhar o processo de instalação do Git no Mac OS/X no vídeo acima ou os passos a seguir.

Para instalar o Git no Mac OS X (Snow Leopard ou superior), há um instalador pronto, basta baixá-lo em [http://code.google.com/p/git-osx-installer/downloads/list?can=3](http://code.google.com/p/git-osx-installer/downloads/list?can=3). Na listagem, selecione o arquivo `git-1.7.7.3-intel-universal-snow-leopard.dmg`.

Após baixá-lo clique duas vezes para ter acesso ao pacote de instalação. Agora é só clicar duas vezes no arquivo `.pkg` de dentro do `.dmg` para iniciar o processo de instalação.

Na tela seguinte, selecione a opção "_Instalar para todos os usuários desse computador_" para continuar. Depois é só prosseguir com a instalação padrão.

Para testar a instalação, abra a aplicação "Terminal" e digite o comando "git". A saída deve ser similar à imagem:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-mac-5.png "instalacao-mac-5")](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-mac-5.png)

Opcionalmente, é possível configurar o Terminal para completar os comandos do Git ao pressionarmos a tecla "tab", além de mostrar na linha de comando se a pasta atual está sendo rastreada pelo Git. Para isso é necessário adicionar as seguintes linhas ao arquivo de perfil do usuário para o prompt de comando, habitualmente encontrado na pasta `home` do usuário com o nome de `.bash_profile`. Adicione as seguintes linhas ao fim do arquivo:

\[sourcecode\] if \[ -f /usr/local/git/contrib/completion/git-completion.bash \]; then . /usr/local/git/contrib/completion/git-completion.bash fi GIT\_PS1\_SHOWDIRTYSTATE=true

PS1='\\u@\\h:\\w $(\_\_git\_ps1 "(%s)")\\$ ' \[/sourcecode\]

Ao iniciar uma nova janela ou aba do Terminal, as alterações estarão aplicadas. Caso queira aplicar as alterações imediatamente no Terminal digite `source ~/.bash_profile`.

## Linux

<iframe src="http://player.vimeo.com/video/34909502?title=0&amp;byline=0&amp;portrait=0" width="541" height="304" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

Você pode acompanhar o processo de instalação do Git no Linux no vídeo acima ou os passos a seguir.

Em qualquer sistema Linux, podemos utilizar o gerenciador de pacotes da respectiva distribuição para instalar o Git. No Ubuntu e no Debian, por exemplo, basta instalar o pacote `git-core` para ter o Git instalado. No Fedora, o pacote `git`. E no Gentoo, o pacote `dev-util/git`.

Portanto, por exemplo, para fazer a instalação do Git no Ubuntu, basta executar o comando:

\[sourcecode\]sudo apt-get install openssl git-core\[/sourcecode\]

Siga as instruções do prompt de comando, primeiro confirmando a instalação dos pacotes e suas dependências, depois confirmando a instalação do pacote `git-core`, como demonstrado na imagem:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-linux-1.png "instalacao-linux-1")](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-linux-1.png)

É possível testar se o Git foi corretamente instalado rodando o comando `git` no prompt de comando. A saída deve ser uma mensagem de ajuda, similar à demonstrada na imagem:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-linux-2.png "instalacao-linux-2")](https://blog.caelum.com.br/wp-content/uploads/2012/01/instalacao-linux-2.png)

Ao instalar o Git, um script de ajuda que completa os comandos do Git no terminal é instalado, juntamente com um script que demonstra na linha de comando se estamos em um diretório que é um projeto rastreado pelo Git. Para ativá-los é necessário modificar o arquivo de perfil do prompt de comando, normalmente esse arquivo encontra-se na pasta "home" do usuário e chama-se ".bash\_profile" (em alguns casos ".bashrc" ou ".zshrc").

Caso nenhum dos arquivos citados exista, crie-o, caso exista, adicione as seguintes linhas ao final dele:

\[sourcecode\] if \[ -f /etc/bash\_completion.d/git \]; then . /etc/bash\_completion.d/git fi GIT\_PS1\_SHOWDIRTYSTATE=true

PS1='\\u@\\h:\\w $(\_\_git\_ps1 "(%s)")\\$ ' \[/sourcecode\]

Salve o arquivo, a partir de agora, caso estivermos em uma pasta rastreada pelo Git no prompt de comando, seremos informados. Para ativar as alterações imediatamente, execute o comando `source` informando o arquivo de perfil que alteramos:

\[sourcecode\] source ~/.bash\_profile \[/sourcecode\]

## Clonando um repositório

Para testar, faça agora um clone do repositório do vraptor:

\[sourcecode\] git clone git@github.com:caelum/vraptor.git \[/sourcecode\]

Repare que, diferente de muitos outros sistemas de controle de versão, você possui agora **todo** o repositório do VRaptor, inclusive todo o histórico de modificações, possibilitando trabalhar de maneira offline e concorrente a outros desenvolvedores. Mais detalhes [no novo curso de Git online](http://www.caelum.com.br/curso/online/git/).
