---
title: "Aumentando a produtividade com o git"
date: "2015-06-30"
author: "eric.camargo"
authorEmail: "eric.camargo@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já sabemos que utilizar o git facilita muito a nossa vida, mas será que ele já faz isso da melhor maneira possível? No começo, utilizar o git pode ser meio custoso até que todos da equipe tenham se ambientado a ele. Como podemos melhorar isso? Não é muito difícil, com simples modificações em seu terminal podemos melhorar muito nosso entendimento sobre o git e, consequentemente, nossa produtividade!

Cada empresa ou até mesmo equipes podem utilizar o git de maneiras diferentes, mas o mais comum é a utilização de _branches_. Para quem esta aprendendo, isso pode ser difícil de se entender e neste post vamos realizar uma pequena modificação que nos ajudará bastante.

#### Mostrar a branch automaticamente

Para deixar o git mais intuitivo, o primeiro passo é deixá-lo um pouco mais colorido e mostrando em qual _branch_ estamos trabalhando, com este comando fica bem simples:

`function git_nome_branch () { git branch 2>/dev/null | grep -e '^*' | sed -E 's/^\* (.+)$/(\1) /' }`

`PS1="\[\033[01;32m\]\u@\h:\[\033[01;34m\]\w\[\033[31m\]\$(git_nome_branch)\[\033[m\]$"`

![Captura de Tela 2015-05-19 às 16.22.11](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-19-às-16.22.11-300x56.png)

Você pode executar este comando em seu terminal para testar como vai ficar, se não gostar e quiser desfazer essa alteração é só fechar o _shell_. Caso tenha gostado e queira manter essa alteração, basta colocá-la no arquivo `~/.bashrc` ou `~/.bash_profile`, e apenas isso aumentará muito nossa produtividade, mas só é útil para quem trabalha com mais de uma _branch_.

#### Informações rápidas sobre o status

Para melhorar ainda mais nossa vida, podemos fazer algumas coisinhas a mais, como por exemplo instalar um novo _shell_ para o terminal com temas melhores e mais avançados como por exemplo o [Zsh](http://www.zsh.org/), que pode ser instalado por um simples `apt-get`:

`sudo apt-get install zsh`

Deixando o Zsh como nosso _shell_ padrão basta selecioná-lo como `/bin/zsh` e poderemos aumentar o seu poder com o framework [Oh My Zsh](https://github.com/robbyrussell/oh-my-zsh), que possui vários temas e funcionalidades diferentes:

_Curl:_

`curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh`

ou

_Wget:_

`wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O - | sh`

Para modificar seu tema, abra o arquivo `~/.zshrc`, nele já haverá muitas opções para deixar seu terminal mais poderoso, basta trocar esta linha pelo tema que preferir:

`ZSH_THEME="<nome_do_tema>"`

Lembrando que ainda é possível alterar as cores e fontes do terminal para ter uma interface ainda melhor ou usar alguns temas pré-definidos para o Zsh.

#### Exemplos de temas para o Zsh

`ZSH_THEME="robbyrussell"` Note que o nosso terminal agora mostra um "x" informando que existem arquivos não adicionados pelo git:

[![Captura de Tela 2015-05-21 às 14.11.29](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-14.11.29-300x86.png)](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-14.08.08.png)

[![Captura de Tela 2015-05-21 às 14.17.07](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-14.17.07-300x79.png)](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-14.11.29.png)

`ZSH_THEME="apple"` Neste tema o terminal mostra um "+" para informar que existem arquivos adicionados mas não commitados:

[![Captura de Tela 2015-05-21 às 13.58.19](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-13.58.19-300x87.png)](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-13.58.19.png)

[![Captura de Tela 2015-05-21 às 14.19.32](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-14.19.32-300x101.png)](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-13.58.19.png)

`ZSH_THEME="miloshadzic"` Este tema é parecido com o primeiro, porém é mostrado um "raio" quando um arquivo é adicionado:

![Captura de Tela 2015-05-21 às 14.08.08](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-14.08.08-300x114.png)

![Captura de Tela 2015-05-21 às 14.23.19](https://blog.caelum.com.br/wp-content/uploads/2015/06/Captura-de-Tela-2015-05-21-às-14.23.19-300x99.png)

Esses foram só alguns exemplos de temas dentre os [diversos temas disponíveis para o Zsh](http://zshthem.es/all/), que apesar de serem bem variados devemos tomar cuidado para que nosso terminal não fique muito poluído com excesso de informações.

Quais outras ferramentas você usa para melhorar sua produtividade com o git?
