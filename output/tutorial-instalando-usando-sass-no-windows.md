---
title: "Tutorial: instalando e usando o Sass no Windows"
date: "2016-01-26"
author: "natan.souza"
authorEmail: "natan.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma coisa que eu achava antes de mexer com Sass é que eu precisava usar Linux ou Mac, ou que daria muito trabalho instalar tudo no meu PC. Mas dá pra trabalhar com Sass no Windows **sim**!

E a galera pediu no post "[CSS menos sofrido com Sass](https://blog.caelum.com.br/css-menos-sofrido-com-sass)" um guia de como trabalhar com Sass no nosso sistema operacional do coração. Primeiramente mostro como preparar o ambiente e depois um teste rápido. Então segue um guia de **como instalar o Sass no Windows**:

## 1 - Instale o Ruby

[Baixe aqui o RubyInstaller](http://rubyinstaller.org/downloads) de acordo com a sua versão\* do Windows e instale **se preocupando** com a observação da imagem abaixo:

[![instalando ruby windows](https://blog.caelum.com.br/wp-content/uploads/2016/01/instalacao-ruby-windows.jpg)](https://blog.caelum.com.br/wp-content/uploads/2016/01/instalacao-ruby-windows.jpg)

_Cuidado: Marque a opção "Add Ruby executable to your PATH"!_

[](https://blog.caelum.com.br/wp-content/uploads/2015/12/instalando-ruby-windows.gif)\* Se não souber  se o seu Windows é 32 ou 64 bits, basta dar o atalho Win+PauseBreak. Será mostrada uma tela com informações do seu sistema, e em "Tipo de sistema", a versão que você tem instalada.

## 2 - Confira se está tudo bem

Dê uma conferida se está tudo bem até agora, indo no prompt / terminal (Win+R > cmd) e dando o comando:

\[code\] ruby -v \[/code\]

Deve aparecer a versão do Ruby como na imagem abaixo:

[![verificar versao ruby](https://blog.caelum.com.br/wp-content/uploads/2016/01/verificar-versao-ruby.jpg)](https://blog.caelum.com.br/wp-content/uploads/2016/01/verificar-versao-ruby.jpg)

Não esquente se a versão estiver um pouco diferente.

## 3 - Ruby instalado, instale o Sass

No prompt mesmo, dê o comando:

\[code\]

gem install sass

\[/code\]

Se deu algum erro, confere se o Ruby está instalado mesmo e/ou tenta [instalar manualmente a gem](https://stackoverflow.com/questions/5778804/installing-ruby-gems-manually).

## 4 - Verifique o Sass

Aquela conferida básica para ver se o Sass foi instalado mesmo:

\[code\] sass -v \[/code\]

Deve aparecer a versão do Sass instalada:

[![verificar versao sass](https://blog.caelum.com.br/wp-content/uploads/2016/01/verificar-versao-sass.jpg)](https://blog.caelum.com.br/wp-content/uploads/2016/01/verificar-versao-sass.jpg)

## 5 - Ambiente todo ok, agora um teste!

Crie uma pasta para teste. Dentro dela crie um arquivo chamado "teste.scss". E nesse arquivo, coloque um código como esse:

\[code language="css"\] $cor-legal: #BADA55;

.course { color: $cor-legal; }

\[/code\]

## 6 - Compile o Sass

Agora na pasta de teste, abra o prompt digitando "cmd ." (sem aspas) na barra de endereços. O prompt vai abrir direto na pasta. Agora compile o arquivo scss em um arquivo CSS com o comando:

\[code language="css"\] sass teste.scss:teste.css \[/code\]

O ideal é não ter que compilar na mão, mas como estamos testando não tem problema.

## 7 - Dê uma conferida no CSS

Se foi tudo ok até aqui, foi gerado um teste.css na sua pasta. Dé uma conferida se ele está parecido com esse:

\[code language="css"\]

.course { color: #BADA55; }

\[/code\]

## 8 - Agora é só alegria! Tranquilo, né? :)

### Outros sistemas operacionais?

Se você usa Mac, o Ruby já vem instalado, só instalar o Sass do mesmo jeito que mostrei. Se você usa Linux, o comando "\\curl -sSL https://get.rvm.io | bash -s stable" (sem aspas) instala o Ruby para você.

### O Sass não é só variáveis

Eu explico as principais features do Sass no curso da Alura, dá uma olhada no [conteúdo do curso de Sass](https://www.alura.com.br/curso-online-sass). Escrevi também um [livro de Sass](https://www.casadocodigo.com.br/products/livro-sass) em que mostro essa fantástica ferramenta. Conseguiu instalar e testar 100%? Teve algum problema?
