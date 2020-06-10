---
title: "Logging, debugging e profiling de JavaScript com o Firebug"
date: "2012-07-17"
author: "gabriel.oliveira"
authorEmail: "gabriel.oliveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O [Firebug](http://www.getfirebug.com "Firebug") -- extensão para o Firefox também disponível em versão "Lite" para outros navegadores -- é uma das ferramentas essenciais para todo programador Web, principalmente para quem tem o trabalho focado no HTML e CSS. Além disso, o **console** do Firebug fornece um ambiente amigável para inspecionarmos nosso código JavaScript.

Aqui na Caelum, usamos bastante o Firebug nos projetos do dia a dia e também no nosso [curso de JavaScript](http://www.caelum.com.br/curso/wd47).

[![Firebug](https://blog.caelum.com.br/wp-content/uploads/2012/06/firebug-logo.png "Firebug")](http://getfirebug.com)

Muitos de nós costumamos utilizar o console do Firebug simplesmente para substituir as funções "`alert();`", quando queremos verificar se uma função foi chamada, qual é o valor de determinada variável em determinado ponto do nosso código ou coisas do gênero. A verdade é que o console do Firebug tem muito mais a nos oferecer. Nesse post vamos conhecer algumas funcionalidades interessantes do console.

## Mensagens no console

Apesar de já bem difundida, a função `console.log()` (ou `console.debug()`) pode receber uma série de argumentos diferentes para mostrar valores no console:

\[sourcecode lang="javascript"\] var nome = "Douglas Adams"; var resposta = 42;

console.log(nome); console.log(resposta); \[/sourcecode\]

Como resultado, temos duas linhas impressas no console do firebug:

![Saída simples no console](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex1.png "Saída simples no console")

Nota: os screenshots são todos parciais, **faça você mesmo** para interagir com o resultado dos exemplos!

Para tornar a mensagem mais compreensível, podemos compor a resposta com as variáveis:

\[sourcecode lang="javascript"\] console.log("A resposta é " + resposta); \[/sourcecode\]

![Concatenação](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex2.png "Concatenação")

Quando concatenamos valores para saída no console, o resultado sempre será uma String, mas podemos imprimir diversos tipos no console que, em alguns casos, nos permite uma inspeção mais detalhada do valor. Para aproveitar essa funcionalidade precisamos trocar a concatenação de valores por, simplesmente, passar múltiplos argumentos para a função:

\[sourcecode lang="javascript"\] console.log("Quem já leu", nome, "sabe a resposta:", resposta); \[/sourcecode\]

![Join](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex3.png "Join")

O Firebug compõe nossa mensagem de modo a exibir de maneira adequada os valores ao invés de converter tudo em `string`. Veja um exemplo com um objeto:

\[sourcecode lang="javascript"\] var livro = { titulo: "O Guia Do Mochileiro Das Galáxias", autor : nome };

console.log("Podemos exibir um objeto", livro, "para inspecionar!"); \[/sourcecode\]

![Inspeção de objeto](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex4.png "Inspeção de objeto")

Ou ainda podemos utilizar o estilo `printf`:

\[sourcecode lang="javascript"\] console.log("Quem já leu %s sabe a resposta: %d", nome, resposta); \[/sourcecode\]

![Printf](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex5.png "Printf")

No estilo `printf` devemos informar qual tipo de informação estamos imprimindo no console para que o mesmo saiba como exibí-la, podemos utilizar os seguintes _placeholders_ na mensagem:

- %s -> String
- %d, %i -> Number (int)
- %f -> Number (float)
- %o -> Object

Podemos ainda aplicar um estilo chamativo para indicar uma mensagem muito importante:

\[sourcecode lang="javascript"\] var estilo = "color:red;background:yellow;"; console.log("%cMensagem bem chamativa!", estilo); \[/sourcecode\]

![CSS no console](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex6.png "CSS no console")

Se quisermos podemos utilizar um dos estilos prontos do Firebug:

\[sourcecode lang="javascript"\] console.info("Informação simples"); console.warn("Cuidado, tem algo estranho aqui!"); console.error("Ops, ocorreu um erro."); \[/sourcecode\]

![Estilos prontos](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex7.png "Estilos prontos")

Quando temos casos muito complexos, quando queremos inspecionar um argumento a cada chamada de função por exemplo, podemos agrupar mensagens para deixar mais fácil de saber qual chamada gerou determiado conjunto de mensagens:

\[sourcecode lang="javascript"\] function descrevePersonagem(personagem) { console.group("Descrevendo", personagem.nome); // lógica da sua função console.log(personagem.nomeVerdadeiro); console.log(personagem.planetaOrigem); console.groupEnd(); }

var arthur = { nome: "Arthur Dent", nomeVerdadeiro: "Arthur Dent", planetaOrigem: "Terra" };

var ford = { nome: "Ford Perfect", nomeVerdadeiro: "Praxibetel Ix", planetaOrigem: "Betelgeuse V" };

descrevePersonagem(arthur); descrevePersonagem(ford); \[/sourcecode\]

![Group](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex8.png "Group")

## Inspeção de objetos

Quando imprimimos um objeto do console e clicamos sobre sua descrição, somos levados à aba **DOM** para obter uma descrição mais detalhada. Na verdade podemos imprimir essa descrição direto no console:

\[sourcecode lang="javascript"\] console.dir(ford); \[/sourcecode\]

![Dir](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex9.png "Dir")

Podemos utilizar o console também para podermos exibir uma estrutura XML (como um fragmento de sua página por exemplo):

\[sourcecode lang="javascript"\] var twitter = document.querySelector("#tfc-followers\_count");

console.dirxml(twitter); \[/sourcecode\]

![DirXML](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex10.png "DirXML")

## Trace

Muitas vezes quando temos uma lógica muito complexa e começamos a extrair partes de nossas funções para outras funções, na intenção de reaproveitar melhor nosso código, podemos acabar nos perdendo. Principalmente quando a execução de uma função depende de uma condição.

Uma das utilidades mais interessantes do Firebug é que ele é capaz de imprimir uma lista descrevendo a função atualmente em execução, quais são seus argumentos e quais funções foram chamadas antes dela com a função `console.trace();`:

\[sourcecode lang="javascript"\] function primeira(str) { return segunda(str + " primeira"); }

function segunda(str) { console.trace(); return str + " segunda" }

primeira("Trace"); \[/sourcecode\]

![Trace](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex12.png "Trace")

## Teste de JavaScript

Outra funcionalidade interessante que o console nos fornece é a comparação para testes. Podemos, em determinado momento do nosso código comprarar valores com a função `console.assert();`. Caso a comparação retorne um valor `false`, um erro é mostrado no console:

\[sourcecode lang="javascript"\] function somaDez(num) { return num + 10; }

console.assert(somaDez(10) == 20); console.assert(somaDez(20) == 25); \[/sourcecode\]

![Assert](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex13.png "Assert")

## Performance de código

Além de poder verificar um resultado para ver se nosso código está funcionando corretamente, ainda é possível saber se não estamos escrevendo um código que resulte em um processamento lento. A funcão `console.time();` nos permite fazer uma medição de tempo de proecessamento de determinado trecho de código:

\[sourcecode lang="javascript"\] var ex1 = "Exemplo 1 - while 100"; var ex2 = "Exemplo 2 - while 1000000";

console.time(ex1); var valorFinalEx1 = 0; for (var i = 0; i < 100; i++) { valorFinalEx1++; } console.timeEnd(ex1);

console.time(ex2); var valorFinalEx2 = 0; for (var i = 0; i < 1000000; i++) { valorFinalEx2++; } console.timeEnd(ex2); \[/sourcecode\]

![Time](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex14.png "Time")

Também é possível analisar a performance de nosso código enquanto um usuário interage com nossa página, para demonstrar esse exemplo, preparamos um ambiente controlado. [Nessa página](http://jsfiddle.net/gabrielso/6QMD4/ "JSFiddle") você pode habilitar o console, clicar em "_Run_" (no menu superior da ferramenta) e depois habilitar o Firebug.

Na aba **Console** do Firebug, clique em **Profile** e clique algumas vezes nos botões "_Exemplo 1_" e "_Exemplo 2_" exibidos no painel à direita. Clique novamente em "_Profile_" para parar a inspeção da página. Após alguns segundos um relatório de todas as funções executadas durante sua interação será exibido.

Como o [JSFiddle](http://jsfiddle.net "JSFiddle") é uma ferramenta bem complexa que permite que você crie pequenos exemplos de como um código JavaScript funciona em determinado cenário, perceba a quantidade de funções que são chamadas durante nossa interação, que não fazem parte do nosso código!

![Profile](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex15.png "Profile")

Repare que, como em muitos exemplos apresentados aqui, clicar no nome da função vai nos levar ao painel **Script** do Firebug, onde será exibido o código da implementação daquela função.

## Debugging interativo

Aproveite que você encontrou o painel de **Script** do Firebug e brinque um pouco com ele. Ao clicar na barra em que é exibido o número da linha do JavaScript, podemos adicionar um _breakpoint_, o comportamento é muito similar ao painel de debug de IDEs como o Eclipse, Netbeans, VisualStudio e outras.

![Debug](https://blog.caelum.com.br/wp-content/uploads/2012/06/ex16.png "Debug")

Agora que você já pode aproveitar melhor as funcionalidades dos painéis **Console** e **Script** do Firebug, já sabe o porquê da extensão estar em todas as listas de "ferramentas indispensáveis" de todo desenvolvedor Web.

Conheça também o curso da Caelum de [programação front end avançada com JavaScript e jQuery](http://www.caelum.com.br/curso/wd47).
