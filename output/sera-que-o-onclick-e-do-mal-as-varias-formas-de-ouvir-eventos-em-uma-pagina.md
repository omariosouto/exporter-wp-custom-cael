---
title: "Será que o onclick é do mal? As várias formas de escutar eventos em uma página"
date: "2016-03-11"
author: "Artur Adam"
authorEmail: "artur.adam@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nesse post, perguntaremos se o usuário realmente quer sair do nosso site quando ele clicar no botão de sair. Precisamos chegar num resultado igual a esta imagem:

[![onclickOuAEL–confirmSair](https://blog.caelum.com.br/wp-content/uploads/2016/02/onclickOuAEL–confirmSair.gif)](https://blog.caelum.com.br/wp-content/uploads/2016/02/onclickOuAEL–confirmSair.gif)

Como teremos que fechar a janela do site caso o usuário confirme que quer sair, usar apenas css e html não seria o suficiente pra essa funcionalidade. Principalmente porque queremos mexer no conteúdo da página depois que a página já carregou, mais especificamente, depois de um clique do usuário. Em casos como esse precisamos usar javascript e com certeza mexer com eventos. Vamos lá!

Nosso botão é esse aqui:

\[code language="html"\] <button id="botaoSair"> Sair </button> \[/code\]

O balão/popup com uma mensagem bonitinha e botões de "Ok" e "Cancelar" será criado com a função `confirm` lá no javascript. Precisamos que a função seja executada quando o usuário clicar no botão. É comum dizer que queremos **escutar** o evento de clique. Podemos fazer isso das seguintes maneiras:

## Atributos do HTML que lidam com eventos ([event handler attributes](https://www.w3.org/TR/html5/webappapis.html#event-handler-attributes))

### Atributo da tag no html

\[code language="html"\] <button id="botaoSair" onclick="confirmaSaida()"> Sair </button> <script> function confirmaSaida(){ confirm('Tem certeza de que quer sair?') } </script> \[/code\]

### Atributo da tag no javascript

\[code language="javascript"\] botaoSair.onclick = confirmaSaida

function confirmaSaida(){ confirm('Tem certeza de que quer sair?') } \[/code\]

## Usando o addEventListener do [DOM](https://www.w3.org/TR/domcore/#introduction-to-dom-events)

\[code language="javascript"\] botaoSair.addEventListener('click', confirmaSaida)

function confirmaSaida(){ confirm('Tem certeza de que quer sair?') } \[/code\]

## E a melhor maneira de escutar eventos é...

As duas maneiras tiveram o mesmo resultado pro usuário. Porém, é muito provável que você já tenha ouvido que usar os atributos do html é a maneira "errada" de escutar um evento. Tratamos de javascript e eventos nos [cursos de front da Caelum](https://www.caelum.com.br/cursos-web-front-end/). Tanto o curso [introdutório](https://www.caelum.com.br/curso-html-css-javascript/) quanto o [avançado](https://www.caelum.com.br/curso-javascript-jquery/) sempre geram boas discussões sobre esse assunto. Nas aulas e neste post não quero convencer você de nada. O que realmente quero é mostrar as perguntas que você sempre deve se fazer para escolher uma ou outra com plena consciência. Aqui vão as perguntas:

## 1\. O evento que eu preciso escutar existe em todas as abordagens?

Os atributos do html e os tipos de eventos do DOM vêm de especificações diferentes da W3C. Temos partes em diferentes especificações que falam quais eventos devem existir nos navegadores:

### [Parte na spec do HTML5](https://www.w3.org/TR/html5/webappapis.html#event-handlers-on-elements,-document-objects,-and-window-objects)

Ela fala basicamente que todas as tags devem ter certos atributos para lidar com eventos. Esses atributos podem ser colocados tanto via javascript quanto no html mesmo. Os eventos que cada tag deve conseguir lidar estão relacionados nessa seção. Um browser compilante com HTML5 deve implementar tudo o que consta nessa tabela.

### [Parte na spec "UI Events"](https://www.w3.org/TR/DOM-Level-3-Events/#event-types-list)

A lista dos tipos de eventos é um rascunho no qual eles ainda estão trabalhando (Working Draft). Isso quer dizer que não há muitas garantias de que algum evento não será adicionado, modificado ou removido.

### [Parte na spec "Pointer Events"](https://www.w3.org/TR/pointerevents/)

Eventos como o `mouseover` assumem que todos os usuários navegam apenas com mouse. Essa spec criou novos eventos um pouco mais abstratos que padronizam os eventos que qualquer dispositivo com propósito de apontar pra algum ponto da página podem emitir.

### [Parte na spec do DOM](https://www.w3.org/TR/dom/#events)

Essa parte trata de como os eventos devem ser ouvidos(addEventListener), disparados e manipulados. Porém, em nenhum momento definem quais tipos de eventos devem existir. O que ela diz é que qualquer um pode criar um tipo de evento que quiser, são o que chamamos de eventos customizados – no [nosso curso de web apps](https://www.caelum.com.br/curso-javascript-jquery/) nós vemos eles a fundo

### E o que essas especificações querem dizer?

Nem todos os atributos que você pode escutar com `addEventListener` terão um atributo `onalgumacoisa` no html. Um exemplo são os _Pointer Events_ que não têm nenhum atributo na spec do hmtl e também qualquer evento customizado que você tenha inventado no seu site. Todos esses eventos só poderão ser escutados com `addEventListener`

Para o nosso botão, podemos optar por qualquer uma das opções já que o evento de _click_ pode ser usado das duas formas.

## 2\. Eu estou separando as responsabilidades?

No front, o que eu quero dizer com separação de responsabilidades é, da maneira mais rasa possível, separar nosso código html do código javascript. O contrário do que fazemos quando utilizamos um atributo como o `onclick`.

Um argumento a favor de sempre separar os dois diz que código javascript dentro do html é um código mais difícil de compreender, e por consequência, de manter. Vou dar um exemplo.

No seguinte html, alguns botões quando clicados devem incrementar o valor de uma variável `numeroDeClicks`.

\[code language="html"\] <header> <button> Login </button> </header> <main> ... conteúdo da página <button onclick="numeroDeClicks++"> Like </button> ... conteúdo da página </main> <footer> <button onclick="numeroDeClicks++"> Share </button> </footer> <script src="analytics.js"></script> \[/code\]

Perceba que temos um script **analytics.js** no final. A função dele é enviar várias informações e estatísticas para nossos servidores. Uma dessas estatísticas é o valor que está na variável `numeroDeClicks`.

O desenvolvedor que só faz o código de **analytics.js** não faz ideia de quais botões da página mudam o valor de `numeroDeClicks`. Se ele precisar remover ou adicionar algum desses botões às estatísticas de clicks ele vai ter que abrir o html, caçar o botão e tirar ou colocar o onclick.

Se você não quer caçar código javascript dentro do html, vá de `addEventListener`.

### Cuidado, separar js de html nem sempre é separação de responsabilidades

Aqui na Caelum nós temos alguns casos em que gostamos bastante de usar atributos do html para lidar com eventos. O que mais gosto é o do logo do [nosso site](https://www.caelum.com.br/), lá no cabeçalho:

\[code language="html"\] <img src="/imagens/caelum-logo.svg" alt="Caelum - Ensino e Inovação" onerror="this.src=this.src.replace('.svg', '.png'); this.onerror=null"> \[/code\]

Nosso logo vem por padrão como um _SVG_. Browsers que não suportam SVG disparam um evento `error` na tag `<img>`. Ouvimos esse evento com um `onerror`, nele mudamos o `src` da imagem para um _PNG_.

Nosso `onerror` não mexe com nenhum outro código javascript. Apenas ajuda a definir o conteúdo do próprio elemento, o que com certeza é responsabilidade do html. Separar os dois não seria separação de responsabilidades e sim separação de linguagens (duas coisas diferentes).

#### E o nosso caso?

É bem provável que mais código, além do confirm(), seja executado dependendo da opção (“Cancelar” ou “Ok”) que o usuário escolher. Nesse caso, com o `onclick` poderíamos estar separando as responsabilidades e seria melhor usarmos o `addEventListener`

## 3\. Quantas vezes vou usar a função `confirmaSaida`?

Geralmente, quando damos nome a uma função que está solta no código, estamos dizendo que ela pode e deve ser chamada diversas vezes em vários pontos do código. Basta qualquer parte do codigo executar `confirmaSaida()` que teremos o mesmo resultado de um clique no botão.

Deixar `confirmaSaida` disponível para qualquer um usar não é bem o que preciso. Se eu só vou usar essa função quando o usuário clicar no botão, posso passar a função como um parâmetro – dentro dos parênteses – do addEventListener:

\[code language="javascript"\] botaoSair.addEventListener('click', function confirmaSaida(){ confirm('Tem certeza de que quer sair?') }) \[/code\]

A função será criada apenas para ser passada para o `addEventListener`, ninguém além dele consegue executar `confirmaSaida()`. Proteger nossa função de ser chamada por outros códigos só é possível com `addEventListener`!

### E por que não com o \`onclick\`?

Não é possível porque atributos como o onclick **recebem** um valor, não são uma função que você chama e passa parâmetros. Para exemplificar, podemos fazer qualquer um dos seguintes exemplos:

### Atributo da tag no html

\[code language="html"\] <button id="botaoSair" onclick="confirm('Tem certeza de que quer sair?')"> Sair </button> \[/code\]

### Atributo da tag no javascript

\[code language="javascript"\] botaoSair.onclick = function confirmaSaida(){ confirm('Tem certeza de que quer sair?') } \[/code\]

Nesses exemplos, nós estamos apenas dando um outro nome para a função `confirmaSaida`. Para chamar ela basta o seguinte em qualquer outro código:

\[code language="javascript"\] botaoSair.onclick() \[/code\]

Se toda tag da página é acessível, seus atributos também são. Assim, `confirmaSaida` ficou acessível pra qualquer um. Se você não quer isso, vá de `addEventListener`

## 4\. Alguma outra coisa precisa acontecer nesse evento?

Uma nova funcionalidade entrou no jogo. Agora, além de mostrar a mensagem uma outra equipe desenvolveu uma super função que vai mandar várias informações e estatísticas para nossos servidores. O código é grande e temos a função `superEstatistica` só pra ela.

\[code language="javascript"\] botaoSair.onclick = function confirmaSaida(){ confirm('Tem certeza de que quer sair?') }

botaoSair.onclick = function superEstatistica(){ //super código gigante } \[/code\]

Nesse momento nosso usuário não vê mais a mensagem de confirmação quando clica no botão

`botaoSair.onclick` é uma propriedade que só pode ter **um** valor de cada vez. No nosso caso, o valor que ela tem é a função `superEstatistica`.

Se você precisa colocar mais de um comportamento num evento, acabou o sonho, vá de `addEventListener`:

\[code language="javascript"\] botaoSair.addEventListener('click', function confirmaSaida(){ confirm('Tem certeza de que quer sair?') })

botaoSair.addEventListener('click', function superEstatistica(){ //super código gigante }) \[/code\]

## Moral da história

No fim, não existe uma solução absolutamente melhor. Em muitos casos as respostas dessas perguntas apoiarão o uso de `addEventListener`, só que isso não quer dizer que as respostas serão sempre as mesmas. No seu próximo projeto faça as 4 perguntas e com certeza você tomará uma decisão bem pensada. Se você lembrar de mais alguma pergunta/motivo pra escolher um dos métodos de escutar eventos, diga aí nos comentários!
