---
title: "Enriquecendo seus sistemas com micro-interactions"
date: "2016-01-19"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

No nosso dia a dia realizamos tarefas como aumentar o volume do rádio, trocar o canal da TV, abrir um torneira e fechar a porta. Todas essas pequenas tarefas são o que chamamos de micro interações ou, mais comumente, **micro-interactions**. Pensando da mesma forma no mundo web, um usuário que visita um _e-commerce_ adiciona o produto em um carrinho, navega por um _carousel_ de fotos do produto, clica em botões, e por aí vai - essas são as **micro-interactions** dentro da web.

Assim que você identificar as **micro-interações** dentro do seu produto, o próximo passo é criar um "feedback" (que é uma das 10 heurísticas de Nielsen) mais "próximo do mundo real" (outra heurística de Nielsen). Dessa forma, sem criar uma nova funcionalidade, você torna o seu produto mais divertido que o do seu concorrente. :-)

Vamos dar uma olhada em alguns exemplos de micro interações:

[![artisan4](https://blog.caelum.com.br/wp-content/uploads/2015/09/artisan4.gif)](https://blog.caelum.com.br/wp-content/uploads/2015/09/artisan4.gif)

[![plus_button](https://blog.caelum.com.br/wp-content/uploads/2015/10/plus_button.gif)](https://blog.caelum.com.br/wp-content/uploads/2015/10/plus_button.gif)

[![xninstant](https://blog.caelum.com.br/wp-content/uploads/2015/10/xninstant.gif)](https://blog.caelum.com.br/wp-content/uploads/2015/10/xninstant.gif)[![wf_checklist_sketch_02](https://blog.caelum.com.br/wp-content/uploads/2015/10/wf_checklist_sketch_02.gif)](https://blog.caelum.com.br/wp-content/uploads/2015/10/wf_checklist_sketch_02.gif)

Outra referência que eu gosto bastante de usar como exemplo é a animação criada pelo [Smart Design](http://smartdesignworldwide.com "Smart Design"):

[Smart Thinking: Micro-interactions Animation](https://vimeo.com/91559869) from [Smart Design](https://vimeo.com/smartdesign) on [Vimeo](https://vimeo.com).

## Micro-interactions são focadas em melhorar a experiência do usuário sem a necessidade de criar uma nova funcionalidade para o produto.

Micro interações parecem ser um negócio legal, mas não é tão simples colocá-las na prática. Exatamente por este motivo, **Dan Saffer**, o cara que criou o termo, também criou um fluxo pra facilitar o desenvolvimento das nossas micro interações:

[![diagram](https://blog.caelum.com.br/wp-content/uploads/2015/09/diagram.png)](https://blog.caelum.com.br/wp-content/uploads/2015/09/diagram.png)

Vamos entender essa proposta do **Dan Saffer**.

**Trigger**, como o usuário dá o start na _micro-interaction_. Pode ser um _mouse over_, um _click_, um toque na tela do celular, e por aí vai.

**Rules**, esse é o momento de levantar todas as regras relacionadas à micro interação. Por exemplo, dentro do jogo ou plataforma de ensino de muitos idiomas [Duolingo](https://www.duolingo.com/ "Jogo ou plataforma de ensino pra muitos idiomas"), você não pode errar a frase que está realizando a tradução.

[![Tradução de texto dentro do Duolingo](https://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-04-at-6.37.25-PM.png)](https://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-04-at-6.37.25-PM.png)

**Feedback**. Com todas as _rules_ que você levantou, esse é o momento de definir como o _feedback_ será representado pro usuário quando ele acionar uma das _rules_. Por exemplo, quando o usuário preencher a tradução errada e em seguida **clicar no botão (trigger) verificar** a plataforma tem que mostrar pro usuário qual foi a palavra que ele errou:

[![Mostrando erro pro usuário](https://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-04-at-6.33.49-PM-1024x570.png)](https://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-04-at-6.33.49-PM.png)

Vale a pena lembrar que o som de erro também é um feedback!

**Loops** são utilizados pra definir por quanto tempo a _micro-interaction_ vai acontecer ou se ela se repete com alguma interação do usuário ou conjunto de regras.

**Modes** são modos diferentes da mesma _micro-interaction_ que podem ser disparados por uma variação da interação do usuário. Por exemplo, dentro do Duolingo pra você conseguir fazer um módulo 2 é necessário fazer o módulo 1. Mas há um exercício que permite que você pule vários módulos, com base no seu conhecimento.

[![Exemplo de mode](https://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-04-at-7.48.23-PM.png)](https://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-04-at-7.48.23-PM.png)

Com as **micro-interactions** estamos melhorando a experiência do usuário no Alura, na Casa do Código e no site da Caelum. E você, faz alguma coisa diferente pra criar as micro interações do produto que você está trabalhando, ou tem qualquer outra dica que possa melhorar a experiência do usuário?
