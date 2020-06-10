---
title: "As múltiplas personalidades do this em JavaScript"
date: "2012-05-07"
author: "ricardo.valeriano"
authorEmail: "ricardo.valeriano@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Se você já escreveu algum código JavaScript, não deve mais achar estranho quando alguém atribui uma função a uma variável. Mas o que exatamente acontece quando você faz isso? Em JavaScript, as funções podem ser passadas como parâmetros para outras funções, retornadas como valor e, como já dito, referenciadas por variáveis. A linguagem trata as funções como [cidadãs de primeira classe](http://en.wikipedia.org/wiki/First-class_function).

[![](https://blog.caelum.com.br/wp-content/uploads/2012/12/javascript-300x225.jpg "javascript")](https://blog.caelum.com.br/wp-content/uploads/2012/12/javascript.jpg)

O JavaScript representa funções como objetos. Quando atribuímos uma função a uma variável, estamos criando uma referência para um objeto do tipo `function`. Podemos ver isso de forma clara utilizando o operador `typeof`. O exemplo abaixo cria uma função e a atribui a variável chamada `apresentar`, que recebe como parâmetro um objeto com os atributos _nome_ e _email_. Vamos invocar a função e a seguir usar o operador `typeof` para inspecionar o tipo que é referenciado pela variável usada para guardar a função: \[sourcecode lang="javascript"\] var apresentar = function(pessoa) { console.log("Eu sou "+ pessoa.nome +" e meu email é: "+ pessoa.email); } apresentar({nome: "Ricardo", email: "ricardo.valeriano@caelum.com.br"}); // Eu sou Ricardo e meu email é: ricardo.valeriano@caelum.com.br typeof(apresentar); // "function" \[/sourcecode\] Quando invocamos uma função com o operador `()`, estamos utilizando uma forma de invocação chamada por alguns de [invocação simples](https://developer.mozilla.org/en/JavaScript/Reference/Operators/this#Simple_call). A maneira como uma função é invocada tem efeito direto sobre como a palavra chave `this` é "atribuída" no corpo da função. Quando seu código JavaScript é executado em um navegador, uma variável global chamada `window` é criada para representar a própria janela do navegador. E quando a forma simples de invocação é usada, o objeto referenciado por `this` é equivalente ao `window`, veja: \[sourcecode lang="javascript"\] typeof(window); // "object"

console.log(window); // DOWWindow

var verificandoWindow = function() { console.log(this); console.log(this === window); } verificandoWindow(); // DOWWindow, true \[/sourcecode\] Notou que o tipo referenciado por `window` é `object`? Podemos criar nossa própria instância de `object` usando a chamada [notação literal](https://developer.mozilla.org/en/Core_JavaScript_1.5_Guide/Core_Language_Features#Object_literals), que é a que foi usada para criar o parametro passado na invocação da função `apresentar` no nosso primeiro exemplo.

A variável `window` é uma referência para um objeto e podemos adicionar atributos a ele! Quando criamos uma variável fora de uma função (ou seja no escopo- global
), automaticamente um atributo com o mesmo nome da variável é criado em `window` para referenciar o objeto atribuído a nova variável. Quando tentamos acessar o valor dessas variáveis, estamos, na verdade, recuperando o valor de um atributo de `window`. Veja o exemplo a seguir: \[sourcecode lang="javascript"\] var todosPodemMeVer = "porque sou um atributo de window"; console.log(window.todosPodemMeVer); // porque sou um atributo de window

var pessoa = { nome: "Ricardo", email: "ricardo.valeriano@caelum.com.br" } console.log(pessoa.nome); // Ricardo

console.log(window.pessoa === pessoa); // true \[/sourcecode\] Toda função tem acesso à palavra chave `this`. Vamos usar isso para obter informações sobre esse objeto. Vamos criar as propriedades _nome_ e _email_ em `window` e alterar a função para buscar os valores usando atributos presentes em `this`, e não mais do objeto recebido como argumento: \[sourcecode lang="javascript"\] var nome = "João da Silva"; window.email = "joao@da.silva"; var apresentar = function() { console.log(this === window); console.log("Eu sou "+ this.nome +" e meu email é "+ this.email); }

apresentar(); // true // Eu sou João da Silva e meu email é joao@da.silva \[/sourcecode\] Veja que criamos um atributo novo chamado `email` para o objeto referenciado por `window`. Um atributo pode referenciar qualquer objeto, inclusive funções. Para ilustrar, vamos criar um novo atributo chamado `quemSouEu` em `pessoa`, que é uma referência para a função `apresentar` do exemplo anterior. Dessa forma, será possível invocar essa função diretamente a partir do objeto `pessoa`.

\[sourcecode lang="javascript"\] var pessoa = { nome: "Ricardo", email: "ricardo.valeriano@caelum.com.br" } \[/sourcecode\]

Mas a função usa internamente a variável `this` para fazer seu trabalho, certo? Será que isso vai continuar funcionando? Repare a saída:

\[sourcecode lang="javascript"\] pessoa.quemSouEu = apresentar; pessoa.quemSouEu(); // false // Eu sou Ricardo e meu email é ricardo.valeriano@caelum.com.br apresentar();// Agora novamente invocando a função com o operador () // true // Eu sou João da Silva e meu email é joao@da.silva \[/sourcecode\]

Uma função referenciada por um atributo de um objeto pode ser invocada como um método daquele objeto, a palavra chave `this` é atribuída ao objeto onde a função foi invocada. Uma outra forma de executar uma função no contexto de um objeto específico é invocando o método `call` disponível em todo objeto do tipo `function`. Esse método possibilita definir o objeto referenciado por `this` no momento da invocação: \[sourcecode lang="javascript"\] apresentar.call(pessoa); // false // Eu sou Ricardo e meu email é ricardo.valeriano@caelum.com.br \[/sourcecode\] Mas não pense que acabou! Existem ainda outras formas de invocar funções que interferem em como a palavra `this` é atribuída. Por exemplo, o operador `new` pode ser utilizado em associação com o operador `()` para invocar qualquer função, isso vai fazer com que o `this` seja atribuído a um novo objeto. O retorno da função também pode ser alterado por essa forma de invocação. Você lerá sobre esses detalhes na segunda parte desse artigo.
