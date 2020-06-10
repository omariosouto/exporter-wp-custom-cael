---
title: "Encontrando melhores formas de trabalhar com o Trello"
date: "2015-05-21"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Gerenciar um projeto ou produto não é uma tarefa fácil. Garantir uma boa visibilidade do que está acontecendo, priorizar as histórias certas e transmitir mensagens claras para toda equipe é sempre um grande desafio. Por esse motivo, cada vez mais os métodos ágeis e ferramentas de organização tem feito parte de nossa rotina. [O Trello é uma dessas ferramentas](https://trello.com/), utilizada por diversas empresas como Google, Adobe e Paypal. Neste post vou compartilhar algumas das estratégias que podem otimizar ainda mais a sua experiência e produtividade com o Trello, baseado em algumas experiências aqui na Caelum. Vale lembrar que mesmo aqui na empresas essas técnicas não são unânimes. Algumas equipes usam o bom e velho cartão, outras usam o trello de forma diferente, etc. O ideal é que você encontre as técnicas que funcionam melhor no seu cenário.

### Visão rápida sobre o Trello

Essa ferramenta é gratuita e o processo de criação de uma conta é extremamente simples, com certeza não levará mais do que alguns segundos. Aos interessados, há ainda uma versão GOLD, que [oferece algumas customizações visuais adicionais e um limite maior para upload de imagens e arquivos](https://trello.com/gold). O Trello se baseia no sistema de kanban (em Japonês, kan = “visual” e ban = “cartão”), bastante utilizado no [desenvolvimento com Scrum](https://www.caelum.com.br/curso-agile-scrum/). A seguinte imagem ilustra sua estrutura e componentes:

![visao-geral-trello](https://blog.caelum.com.br/wp-content/uploads/2050/05/visao-geral-trello.png)

Veja que ele é dividido em quadros (boards), listas e cartões. Um **board** é um projeto, produto, recurso ou basicamente qualquer outra coisa que você quiser, já que seu uso não se limita a projetos de software/tecnologia. [Você pode muito bem criar um board para organizar suas próximas férias, a reforma de sua casa e etc](https://trello.com/tour), esse [post americano ilustra bem isso](http://lifehacker.com/how-to-use-trello-to-organize-your-entire-life-1683821040). Dentro de cada board, temos **listas**, que são colunas para te ajudar a organizar as tarefas, que por sua vez são representadas por **cartões**. Cartões podem ter checklists, anexos, imagens, votos, data de expiração e muito mais. E ao bom estilo drag-and-drop, você pode move-los entre as diferentes listas. Bem simples, não é?

Uma das grandes vantagens do Trello é que ele trabalha de forma horizontal. Isso significa que você pode criar quantas listas forem necessárias, sem ficar preso ao tradicional TODO, DOING e DONE (mas que é eficaz em muitos casos). Outro ponto importante é que, se quiser, você poderá receber e-mails de notificação a cada ação realizada, respondê-los para executar alguma outra ação e etc. As possibilidades são muitas.

Porém, como qualquer outra ferramenta ou metodologia, você precisa tomar certos cuidados e adotar estratégias que tornam seu uso sólido e eficaz. Aqui vão algumas delas.

### Defina bem as responsabilidades de um board

Evite ter um board com muita ou toda a responsabilidade, isso pode complicar o trabalho e acaba se tornando bastante improdutivo. No Alura, por exemplo, usamos o Trello para organizar o desenvolvimento da plataforma, a edição de vídeos, criação de novos cursos, correção de conteúdo e etc. Imagine se tudo isso ficasse em um único quadro do Trello?! Um caos. Entre outros problemas, um dos mais graves seria a perda de um dos maiores benefícios de utilizar uma ferramenta como essa, que é a **visibilidade ampla do projeto e andamento de suas atividades**. Veja que no exemplo a seguir, do Trello de desenvolvimento do Alura, em no máximo 10 segundos você saberá exatamente quais tarefas estão pendentes, em andamento e em revisão de código:

![board alura sem membro e label](https://blog.caelum.com.br/wp-content/uploads/2015/05/board-alura-sem-membro-e-label.png)

### Separe os boards de sprint atual e backlog

Porém repare que isso não aconteceria caso esse board tivesse um número muito grande de cartões. Quanto maior o número de cartões, maior será o tempo que você levará para processar o que está acontecendo no projeto ao olhar para esta tela. Por isso é fundamental dividir as responsabilidades de um projeto/board ainda mais, em suas pequenas interações -- os famosos sprints do mundo ágil. A grande idéia é que, principalmente em projetos maiores, você tenha um board apenas com as atividades do sprint atual (de uma semana, por exemplo), e outro board com o backlog de histórias futuras. Neste segundo, que possívelmente terá um volume maior de cartões, podemos organizar por assunto, prioridade, ou o que melhor se encaixar no escopo do seu projeto. Para o caso do Alura, temos 3 colunas: **histórias futuras**, **histórias próximas** (que serão movidas para o board de Sprint atual na próxima iteração) e por fim **refatorações**, que pegamos uma por semana.

![board alura futuro](https://blog.caelum.com.br/wp-content/uploads/2015/05/board-alura-futuro.png)

### Navegue com facilidade

Você pode e deve usar o atalho B para navegar entre boards. Ele exibirá um menu de navegação onde você filtrará um board existente, ou digitará o nome de um novo para que a opção "create board" fique disponível:

![filtro-board](https://blog.caelum.com.br/wp-content/uploads/2050/05/filtro-board.png)

### Movendo cartões entre boards

Uma particularidade para quando estamos trabalhando com boards de histórias futuras e atuais é que, eventualmente, precisaremos mover os cartões de um board para o outro. Existem diversas formas de fazer isso, mas minha favorita é mover o foco para o cartão (utilizando as setas do teclado) e pressionar a tecla E, que é o atalho de edição. Feito isso, basta selecionar a opção "move" e escolher o board de destino, que no exemplo a seguir será o Alura.

![movendo_de_board](https://blog.caelum.com.br/wp-content/uploads/2015/05/movendo_de_board.png)

Você pode, inclusive, mover uma lista inteira de uma board para outra. Basta clicar no ícone do canto da lista que você quer mover e selecionar a opção "Move List...". Outra necessidade será arquivar os cartões concluídos do até então sprint atual. Você pode fazer isso clicando no cartão e logo em seguida na opção "archive". Ou melhor ainda, utilizando a tecla de atalho C. Todos os cartões arquivados podem ser consultados e restaurados pela opção "archived items" do menu lateral.

### Identifique os responsáveis e trabalhe com cores

Ao falar sobre a divisão de responsabilidades, eu mencionei que ao olhar para a imagem a seguir você levaria no máximo 10 segundos para saber exatamente o que está acontecendo no board:

![board alura sem membro e label](https://blog.caelum.com.br/wp-content/uploads/2015/05/board-alura-sem-membro-e-label.png)

Mas, além disso, você pode e deve enriquecer ainda mais o feedback visual desse quadro deixando explicito quem fez, está fazendo ou deverá fazer determinada tarefa. Quer ver com o é simples? Basta selecionar o cartão que pretende alterar e digitar o **atalho M**, de membro. Você pode adicionar um ou mais membros para cada cartão.

![board alura atual](https://blog.caelum.com.br/wp-content/uploads/2015/05/board-alura-atual.png)

Outro atalho bastante conveniente é a tacla de espaço, que automaticamente te adiciona como membro de um cartão. Legal, não é? Veja que um detalhe marcante, e recomendado, é que você use cores diferentes nas boards. Neste caso usamos um vermelho, para evitar confusão. Você pode mudar a cor da board selecionando a opção "change background" no menu lateral.

Por fim, repare que usei um esquema de cores para definir a ordem de prioridade das tarefas. Isso pode ser feito com auxílio das labels, pelo atalho L, ou digitando um número de 1 a 9 (1 para verde e daí pra frente cores mais quentes). O uso de labels pode variar de acordo com a realidade de seu projeto. Você pode usá-las para definir prioridade, categoria, entre outros.

### Use e abuse dos atalhos

Você não precisa conhecer nenhum destes atalhos para utilizar o Trello, mas com toda certeza conhecê-los pode tornar o seu dia a dia muito mais produtivo e agradável. Pra mim, não iterar com o mouse a todo momento e evitar dezenas de clicks faz muita diferença. Fora os atalhos que eu mencionei durante o post, você possívelmente gostará de usar a tecla F, para filtrar os cartões, Q para exibir apenas os cartões em que você é um membro, W para exibir ou ocultar o menu lateral, e principalmente o ?, que exibe um [mapa com todos os atalhos disponíveis](https://trello.com/shortcuts). Não deixe também de conferir esse post do [blog do trello]( http://br.blog.trello.com/blog/2013/02/06/usando-trello-como-um-profissional/), que apresenta diversas features e possibilidades interessantes da ferramenta.

### Hangout: Produtividade com o Trello

Recentemente também fiz um hangout no Alura sobre produtividade com Trello:

\[embed\]https://www.youtube.com/watch?v=saLyiokac4M\[/embed\]

Se quiser, você pode ver mais sobre Gerenciamento de projetos, Kanban, Scrum, Sprints e outros termos que mencionei aqui [em nossa formação Ágil](https://www.caelum.com.br/cursos-agile/).

Ah, e também [criamos um post sobre Slack](https://blog.caelum.com.br/encontrando-melhores-formas-de-trabalhar-com-slack/), que é outra ferramenta fundamental para gerenciamento de times e processos. Se gostou desse post e do Trello, certamente vai se interessar por ele também.

E você, que ferramentas e práticas tem utilizado para o gerenciamento de produtos e projetos de sua empresa?
