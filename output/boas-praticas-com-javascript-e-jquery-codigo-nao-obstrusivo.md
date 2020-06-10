---
title: "Boas práticas com JavaScript e jQuery: código não-obstrusivo"
date: "2012-06-04"
author: "gabriel.oliveira"
authorEmail: "gabriel.oliveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já há bastante tempo, por conta do amadurecimento da Web como plataforma de aplicações, a programação front-end de aplicações para Web vem adotando alguns [padrões e boas práticas](http://coding.smashingmagazine.com/2008/09/16/jquery-examples-and-best-practices/ "padrões e boas práticas"). Os benefícios disso são claros, principalmente quando consideramos o fato de que até as mais simples páginas Web não são tão triviais: são compostas de camadas distintas de marcação, apresentação e interatividade, normalmente tendo HTML, CSS e JavaScript como responsáveis.

Uma das práticas mais importantes quando pensamos na tríade do front-end é o desacoplamento dessas camadas. Ou seja, **não devemos adicionar informações visuais, nem sobre interatividade com o usuário, na camada de marcação**. Dessa maneira, a manutenção de cada componente é muito mais fácil pois cada um tem seu lugar distinto. Infelizmente, a mistura ainda ocorre, como este frequente código:

\[html\] <a style="font-size:16px" href="#" onclick="adicionaItem()">Adicionar item</a> \[/html\]

No exemplo acima, posso distinguir claramente quem não pertence ao HTML, se levarmos em consideração as camadas visuais (`style`) e de interatividade (`onclick`). Elas não deveriam estar ali. Para mantermos nossa marcação limpa, podemos utilizar um seletor do CSS para externalizar as informações visuais. Para deixar o exemplo simples, vamos trocar os atributos alienígenas (pertencentes a outras camadas) por um atributo `id`:

\[html\] <a id="additem" href="#">Adicionar item</a> \[/html\]

Agora, podemos criar o seguinte seletor no CSS e adicioná-lo ao HTML de maneira adequada (tag `<style>` ou `<link>` para arquivo CSS externo):

\[css\] #additem { font-size: 16px; } \[/css\]

De acordo com o demonstrado no primeiro exemplo, o navegador deve disparar a execução da função JavaScript `adicionaItem()`, quando o usuário clicar na área da página ocupada pelo elemento `<a>`. A implementação dessa função não é de nosso interesse no exemplo, só precisamos executá-la. Como fazer isso sem adicionar essa função diretamente na marcação HTML?

Uma das características mais importantes da execução de JavaScript em uma página Web é que o navegador disponibiliza acesso a todo e qualquer elemento declarado no HTML através da DOM API (Document Object Model API). Isso significa que podemos, em nosso código JavaScript, criar objetos que fazem referência direta a tags do HTML.

Alguns desses objetos refletem a alteração de seus atributos imediatamente no navegador. Para implementar o comportamento necessário, devemos informar ao navegador que, em determinado elemento, há uma função a ser executada caso ele seja o _target_ de um evento. Para obtermos esse resutado, precisamos interagir com o _EventListener_ desse elemento. Em JavaScript puro, teríamos a seguinte abordagem:

\[sourcecode lang="javascript"\] // Primeiro é necessário criar um objeto que faz // referência ao elemento no HTML: var linkAddItem = document.getElementById('additem');

// Depois adicionamos a função "adicionaItem" à lista de // funções que devem ser executadas quando o usuário clica // na área do elemento no navegador: linkAddItem.addEventListener('click', adicionaItem, false); \[/sourcecode\]

Esse padrão é o que chamamos de **JavaScript não-obstrusivo** (não intrusivo). Estamos adicionando interatividade à página através do JavaScript, sem a adição de atributos e informações desnecessárias na marcação. Note que passamos como segundo argumento o nome da função somente, sem os parênteses, necessários para executá-la. Isso porque não queremos executá-la de fato, apenas delegar sua execução à ocorrência do evento.

Fato é que os navegadores (principalmente o IE antigo) não são exatamente consistentes na implementação desses objetos em suas APIs. A própria função `addEventListener` não existe no IE até sua versão 9. No IE8 e anteriores, devemos chamar a função `attachEvent`, que implementa o mesmo comportamento.

Para termos um código compatível entre navegadores, seria necessário realizar uma série de verificações para identificar qual abordagem tomar em cada caso. A boa notícia é que algumas bibliotecas de JavaScript fazem isso para nós. Elas abstraem essas diferenças em funções relativamente mais simples e compatíveis com os principais navegadores, versões e plataformas, se não todas.

[![Eventos com jQuery](https://blog.caelum.com.br/wp-content/uploads/2012/05/jq.jpg "jQuery")](https://blog.caelum.com.br/wp-content/uploads/2012/05/jq.jpg)

O [jQuery](http://jquery.com/), hoje a biblioteca JavaScript que é quase onipresente, interage com os `EventListeners` de maneira bem direta:

\[sourcecode lang="javascript"\] $('#additem').on('click', adicionaItem); \[/sourcecode\]

Caso você precise usar uma versão anterior a 1.7 do jQuery, substitua a função "on()" pela "bind()".

No código acima, a função `$('#additem')` nos retorna um objeto que representa o elemento com id `additem` em nossa marcação. Esse é um objeto do jQuery e podemos chamar sua função `on()` que precisa de dois argumentos: o nome do evento a ser observado e o nome da função que deve ser executada ao ocorrer esse evento na área que o elemento ocupa na página. Essa abordagem nos permite descartar o atributo alienígena `onclick` no HTML, sendo que o próprio JavaScript identifica qual função deve ser executada quando ocorre um evento em determinado elemento da página.

Nos casos em que a função a ser executada precise de argumentos, não podemos passar os argumentos com esse padrão, é necessário passar como argumento para a função `on()` uma função anônima e, dentro dessa, chamar a função `adicionaItem()` com argumentos.

Essa função anônima (bem como a função `adicionaItem` nos exemplos anteriores) recebe, por padrão, um objeto que representa o evento ocorrido no caso de sua execução. Esse objeto contém diversas informações interessantes como um timestamp de quando ocorreu o evento, no caso do evento `click`, quais eram as coordenadas do mouse na janela do navegador no momento do clique entre outras.

Vamos supor um número como argumento por exemplo:

\[sourcecode lang="javascript"\] $('#additem').on('click', function(event) { // Essa função anônima pode conter uma lógica mais complexa. adicionaItem(1); }); \[/sourcecode\]

Por estarmos adicionando um evento a um link nesse exemplo, o comportamento padrão do navegador é, após a execução da função JavaScript, o evento retornar ao seu ciclo normal e o usuário ser levado ao endereço declarado no link, nesse caso o topo da página (`href="#"`). Normalmente, esse comportamento é indesejado, então vamos informar nossa função que queremos anular o restante do ciclo do evento:

\[sourcecode lang="javascript"\] $('#additem').on('click', function(event) { // Essa função anônima pode conter uma lógica mais complexa. adicionaItem(1);

// Anular a continuação do ciclo do evento no navegador: event.preventDefault(); }); \[/sourcecode\]

Nos primeiros exemplos, quando passamos o nome da função diretamente à funcão `on()`, seria necessário adicionar a linha `event.preventDefault()` dentro da função `adicionaItem()` para obtermos o comportamento correto. Alguns desenvolvedores também utilizam `return false;` no lugar de `event.preventDefault();` o que, em nosso simples exemplo, terá o mesmo resultado.

Esse tratamento direto do objeto _event_ permite inclusive a adoção de um outro padrão recomendado: nenhum link deve levar ao destino "#" (topo da página), a não ser que seja essa sua finalidade. O ideal é que no atributo `href` seja utilizado um URL que realizará o mesmo comportamento que esperamos com o JavaScript, só que sem JavaScript. Esse _fallback_ é importante para acessibilidade e atende dispositivos que não suportam JavaScript.

Podemos atribuir uma função ao `EventListener` de qualquer elemento, não só dos links. Nesses casos, não precisamos anular o comportamento padrão do evento com "`event.preventDefault()`" visto que clicar em uma `<img>` ou `<div>` qualquer não tem efeito colateral na interação do usuário com a página.

Esses e outros assuntos avançados de JavaScript, a gente discute em mais detalhes na [Formação Web](http://www.caelum.com.br/cursos/web/) da Caelum.
