---
title: "Possibilitando o overload de métodos em Javascript"
date: "2012-03-19"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em muitas linguagens, como Java, é comum fazer o overload de métodos e que uns invoquem os outros:

\[java\] public class NotaFiscal { public void add(Produto produto) { add(produto, 1); } public void add(Produto produto, int quantidade) { // ... } } \[/java\]

Para identificar qual dos dois métodos será invocado depende do número de parâmetros passados (e os tipos). Já em Javascript, definir uma função com o mesmo nome a sobrescreve:

\[javascript\] function adiciona(produto) { adiciona(produto, 1); } function adiciona(produto, categoria) { // essa funcao "sobrescreve" a anterior } \[/javascript\]

Como trabalhar com variações de métodos com o mesmo nome, mas com número de argumentos ([aridade](http://en.wikipedia.org/wiki/Arity)) distintos? John Resig, criador do jQuery, [sugeriu uma implementação simples e rápida](http://ejohn.org/blog/javascript-method-overloading/):

\[javascript\] function addMethod(object, name, fn) { var old = object\[name\]; object\[name\] = function() { if (fn.length == arguments.length) return fn.apply(this, arguments); else if (typeof old == 'function') return old.apply(this, arguments); }; } \[/javascript\]

Ela é uma implementação de [chain of responsibility](https://blog.caelum.com.br/compondo-seu-comportamento-heranca-chain-of-responsibility-e-interceptors/) trabalhando com funções ao invés de objetos. Nesse caso, cada chamada de `addMethod` armazena a última função criada e define uma nova função que, quando invocada, invocará a nova ou a anterior, de acordo com o número de parâmetros. Chain of responsibility se encaixou perfeitamente aqui. O uso seria:

\[javascript\] function Users() { addMethod(this, "add", function() {}); addMethod(this, "add", function(name) {}); addMethod(this, "add", function(first, last) {}); }

new Users().add(); new Users().add("1"); new Users().add("1", "2"); \[/javascript\]

Dessa forma também podemos implementar argumentos opcionais, adicionando funções que recebem menos parâmetros e delegam para a versão que recebe mais, passando os valores default.

Scott Olson sugeriu, no mesmo post, um código 3 vezes mais rápido que a implementação de Resig:

\[javascript\] function addMethod2(object, name, fn) { object.\_store = object.\_store || {}; object.\_store\[name\] = object.\_store\[name\] || {}; object.\_store\[name\]\[fn.length\] = fn; object\[name\] = function() { if (this.\_store\[name\]\[arguments.length\]) return this.\_store\[name\]\[arguments.length\].apply(this, arguments); }; } \[/javascript\]

A vantagem da primeira abordagem é a de não adicionar um novo atributo ao objeto. Mas existem outras opções, em uma das madrugradas de programação do pessoal da Caelum, escrevemos uma variação que recebe uma array de funções, cada uma com um número diferente de argumentos. Iterando por essa array podemos transformá-la em um map, com a aridade como chave. Devolvemos uma função final que acessa a função desejada:

\[javascript\] function functions(fs) { var self = {}; fs.forEach(function(f) { self\[f.length\] = f }); return function() { self\[arguments.length\].apply(this, arguments); }; } \[/javascript\]

Para utilizar, fazemos:

\[javascript\] function Users() { this.add = functions(\[ function() {}); function(name) {}); function(first, last) {} \]); } \[/javascript\]

Uma solução que parece bem mais elegante, já que não há necessidade de uma chain of responsability aqui, pois não há prioridade: o mapeamento é direto aridade->função. Além disso, há um ganho colateral de performance.

Ainda há a pergunta: vale a pena definir métodos dessa forma diferente apenas para utilizar esse recurso? O mesmo pode ser igualmente aplicado a Ruby, com parecidas vantagens e desvantagens.
