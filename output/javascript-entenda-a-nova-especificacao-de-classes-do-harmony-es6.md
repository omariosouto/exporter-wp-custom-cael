---
title: "Entenda a nova especificação de classes do JavaScript Harmony ES6"
date: "2014-06-04"
author: "leonardo.wolter"
authorEmail: "leonardo.wolter@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Há tempos a criação de classes e objetos em JavaScript tem sido complexa e difícil de entender, isso por conta de não existir uma linha clara dividindo classes de funções e objetos e, com isso, [existirem diversas maneiras de se criar estes](https://blog.caelum.com.br/criacao-de-objetos-em-javascript/) bem como diversas formas de utilizar praticas de P.O.O. como a [herança](https://blog.caelum.com.br/reaproveitando-codigo-com-js-heranca-e-prototipos).

Por estes motivos, o ECMAScript6 (também conhecido como Harmony, ES.next ou ES6) prevê uma nova especificação chamada [maximally\_minimal\_classes](http://wiki.ecmascript.org/doku.php?id=strawman:maximally_minimal_classes), que tem como objetivo uniformizar e simplificar a definição de classes do modo mais minimalista possível.

Atualmente, o modo mais usado para se definir algo parecido com uma classe em JavaScript é utilizando uma função construtora e adicionando funções ao seu protótipo. Confira abaixo como ficaria a representação de uma `Pessoa` deste modo:

\[code language="js"\] var Pessoa = function(nome, email) { this.nome = nome; // verifica se o e-mail foi preenchido if (email) { this.email = email; } }; Pessoa.prototype.fala = function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); }; \[/code\]

Note que a sintaxe não é exatamente intuitiva, deste modo você precisaria saber o que é um prototype e que, em JavaScript, uma classe é uma função no final das contas, o que tende a confundir bastante a cabeça de desenvolvedores acostumados com P.O.O.

Agora vamos ver como ficaria uma `Pessoa` utilizando a nova especificação:

\[code language="js"\] class Pessoa{ constructor(nome, email){ this.nome = nome; // verifica se o e-mail foi preenchido if (email) { this.email = email; } }

fala(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); } } var leo = new Pessoa("Leonardo", "leonardo.wolter@caelum.com.br"); leo.fala(); // Olá, meu nome é Leonardo e meu email é leonardo.wolter@caelum.com.br \[/code\]

Caso você já programe em uma linguagem orientada a objetos como o Java, essa sintaxe provavelmente será muito mais intuitiva que a anterior.

Um detalhe interessante dessa abordagem é que, apesar de a função `fala` ser declarada dentro da classe `Pessoa`, ela será adicionada no **prototype** da `Pessoa`, o que pode ser visivelmente mais performático do que adicioná-la diretamente na função.

Apesar de ser muito parecido com Java, essa especificação não adicionará modificadores de acesso, o que significa que todos os atributos ou métodos adicionados à classe serão públicos! Como a própria especificação diz: "private is simply achieved via [private name objects](http://wiki.ecmascript.org/doku.php?id=harmony:private_name_objects)".

Além da definição padrão de classes citada acima, também estará disponível uma maneira de criar pseudo-propriedades simplesmente adicionando a palavra get ou set antes do nome da função, [assim como você pode fazer em objetos literais](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/get). Exemplo:

\[code language="js"\] class Pessoa { constructor(nome, email) { this.nome = nome; this.comidas = \[\]; // verifica se o e-mail foi preenchido if (email) { this.email = email; } }

fala() { console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); }

get primeiroNome() { return this.nome.split(" ")\[0\]; }

set gostaDe(comida) { this.comidas.push(comida); } }

var leo = new Pessoa("Leonardo Wolter", "leonardo.wolter@caelum.com.br"); leo.gostaDe = "bolo"; console.log(leo.comidas); // \["bolo"\] console.log(leo.primeiroNome); // Leonardo \[/code\]

Agora conseguimos criar uma classe com uma sintaxe melhor, legal, mas e as heranças, como ficam?

Atualmente, o modo mais utilizado de se implementar herança é utilizando o [Prototype-Chainning](https://blog.caelum.com.br/reaproveitando-codigo-com-js-heranca-e-prototipos). Imagine que temos uma classe `PessoaFisica` que herda `Pessoa`. O código seria parecido com este: \[code language="js"\]

var PessoaFisica = function(nome, email, cpf){ Pessoa.call(this, nome, email); this.cpf = cpf; }; PessoaFisica.prototype = new Pessoa(); PessoaFisica.prototype.constructor = PessoaFisica; PessoaFisica.prototype.dizCpf = function(){ console.log(this.cpf); };

\[/code\]

Resumindo você precisaria criar uma função construtora com o conteudo adequado, setar o prototype de `PessoaFisica` com uma instancia de `Pessoa`, consertar a propriedade construtor e, por ultimo mas nao menos importante, adicionar as funções específicas sua `PessoaFisica`.

Confira agora como implementaríamos a mesma herança utilizando a nova especificação:

\[code language="js"\]

class PessoaFisica extends Pessoa{

constructor(nome, email, cpf){ super(nome, email); this.cpf = cpf; } dizCpf(){ console.log(this.cpf); } }

var leo = new PessoaFisica("Leonardo", "leonardo.wolter@caelum.com.br", "meucpf" ); leo.gostaDe = "bolo"; console.log(leo.comidas); // \["bolo"\] console.log(leo.idade); // 63 leo.dizCpf(); // "meucpf" leo.fala(); // Olá, meu nome é Leonardo e meu email é leonardo.wolter@caelum.com.br

\[/code\]

Temos como resultado um código mais expressivo e enxuto, apenas utilizamos a palavra-chave `extends` para dizer que a `PessoaFisica` herda `Pessoa` e substituimos a estratégia de _Constructor Stealing_ pelo código `super(nome, email)`

Legal, e quando eu vou poder usar isso? Atualmente, apenas o [traceur-compiler](https://github.com/google/traceur-compiler) possui suporte a essa nova sintaxe, uma demonstração do código desse post funcionando pode ser vista [aqui](http://bit.ly/1gbL4Vm).

Caso esteja interessado nas outras funcionalidades que serão adicionadas ao Harmony, você pode conferir a tabela de compatibilidade com os browsers, compilers e node [aqui](http://kangax.github.io/es5-compat-table/es6).
