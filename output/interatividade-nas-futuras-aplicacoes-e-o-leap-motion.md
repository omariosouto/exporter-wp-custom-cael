---
title: "Interatividade nas futuras aplicações e o Leap Motion"
date: "2013-09-05"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O teclado e o mouse são duas das maneiras mais antigas de se comunicar com um computador pessoal, junto com controles estilo vídeo game. Será que essas são as melhores maneiras para entrada e saída, além de manusear informações em um computador?

[![leap-motion](https://blog.caelum.com.br/wp-content/uploads/2013/09/leap-motion-300x165.png)](https://blog.caelum.com.br/wp-content/uploads/2013/09/leap-motion.png)

Nos últimos tempos, novas ferramentas surgiram que permitem trabalhar com o computador de outra maneira, muito além do número finito de entradas de um teclado ou dos atalhos de movimentos em uma tela touchscreen 2d. Uma delas é o [Leap Motion](https://www.leapmotion.com/), pequeno o suficiente para ser embutido em notebooks, que já está disponível.

Utilizando um leitor de infravermelho, o Leap cria na memória um mapa 3d do espaço próximo ao seu redor, sendo capaz de detectar mãos e dedos. Note que ele funciona de maneira diferente do Kinect, que consegue analisar a posição de corpos, mas em geral não de maneira tão fina quanto o Leap faz com os dedos.

Sua API é extremamente simples de usar, e criamos uma implementação de um dos jogos de mão mais popular do Brasil: o (como escreve) Jankenpo. A regra é simples, a cada rodada cada um dos dois jogadores escolhe uma de três opções: 2 dedos abertos para tesoura, mão fechada para pedra e mão aberta para papel. Papel ganha de pedra (embrulha), pedra ganha da tesoura (quebra) e tesoura ganha do papel (corta).

Para implementar o Jankenpo basta instanciar um controller do leap em nosso código:

\[code language="java"\] Controller controller = new Controller(); \[/code\]

E uma das maneiras de trabalhar com ele é registrar um listener, que será notificado de cada mudança de informação do sensor do Leap:

\[code language="java"\] controller.addListener(new JankenpoListener()); \[/code\]

O JoKenPoListener herda de com.leapmotion.leap.Listener e sobrescreve um único método:

\[code language="java"\] public void onFrame(Controller controller) { } \[/code\]

Pronto! Agora basta descobrirmos quantos dedos estão visíveis na mão do usuário. Para isso, acessamos a lista de mãos visíveis:

\[code language="java"\] Frame frame = controller.frame(); HandList maos = frame.hands(); \[/code\]

Repare que podemos ter 0, 1, 2 ou mais mãos visíveis a qualquer instante. Se nenhuma mão estiver visível, ou se o usuário mostrar mais de uma mão, vamos considerar que ele não escolheu nada, e retornar:

\[code language="java"\] if(maos.count() != 1) return; \[/code\]

Depois vamos acessar esta mão:

\[code language="java"\] Hand primeiraMao = frame.hands().get(0); \[/code\]

E agora perguntar quantos dedos estão a mostra:

\[code language="java"\] FingerList dedos = primeiraMao.fingers(); int dedosAMostra = dedos.count(); \[/code\]

Com o número de dedos a mostra, sabemos dizer se o usuário está fazendo uma tesoura, papel ou pedra:

\[code language="java"\] if(dedosAMostra==0) selecao = "PEDRA"; else if(dedosAMostra==2) selecao = "TESOURA"; else if(dedosAMostra==5) selecao = "PAPEL"; \[/code\]

Temos que cuidar dos outros casos, onde uma mão pode ter 1, 3, 4 ou mais de 6 dedos visíveis. No meu caso, vou lidar com 0 ou 1 dedos como sendo pedra, 2 tesoura e 3 ou mais papel:

\[code language="java"\] if(dedosAMostra < 2) selecao = "PEDRA"; else if(dedosAMostra == 2) selecao = "TESOURA"; else selecao = "PAPEL"; \[/code\]

Pronto! Basta ligar o LEAP e ver o resultado! Você pode ver uma aplicação swing com o código acima rodando no vídeo a seguir, e o código fonte se encontra aqui.

<iframe src="http://player.vimeo.com/video/73814064" width="500" align="center" height="313" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

Mas a API vai muito além de contar quantos dedos estão visíveis, ela pode nos dizer a posição de cada dedo no espaço 3d, e para que ponto estamos apontando, permitindo interagir com objetos. Ela indica o raio da palma da mão, para saber a abertura da mesma, além de também nos notificar de movimentos específicos, como quando o usuário faz um círculo com um dedo.

Agora que você já viu como é fácil trabalhar com o LEAP, dê uma olhada em como é viajar pela terra com o Google Earth usando o aparelho - Minority Report?

<iframe src="http://player.vimeo.com/video/73812638" align="center" width="500" height="313" frameborder="0" webkitallowfullscreen mozallowfullscreen="" allowfullscreen=""></iframe>

A biblioteca do LEAP para Java é um único jar que deve ser incluído que faz a comunicação com os arquivos .dylib em um sistema operacional OSX (a api java para windows e linux é a mesma, mas as bibliotecas de baixo nível que acessamos indiretamente podem variar). E você, o que gostaria de implementar com o LEAP?
