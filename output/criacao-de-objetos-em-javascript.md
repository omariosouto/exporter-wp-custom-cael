---
title: "Criação de objetos em Javascript"
date: "2014-04-15"
author: "leonardo.wolter"
authorEmail: "leonardo.wolter@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Utilizando JavaScript, existem varias formas de se construir um objeto. As duas formas mais conhecidas são:  utilizando a notação literal e utilizando funções construtoras. É uma questão que aparece bastante com alunos mais experientes no nosso [curso de JavaScript e jQuery](http://www.caelum.com.br/curso-javascript-jquery/).

Estas duas formas são abordadas [nesse excelente post](https://blog.caelum.com.br/conhecendo-o-operator-new-e-os-prototipos-no-javascript/ "excelente post") do Ricardo Valeriano, onde ele explica, além destas formas, uma boa maneira de adicionar atributos e métodos em todas as instâncias de determinada função: o `prototype`.

Suponha o seguinte exemplo de função construtora:

\[code language="js"\] var Pessoa = function(nome, email) { this.nome = nome; // verifica se o e-mail foi preenchido if (email) { this.email = email; } }; \[/code\]

Para adicionar um método nos objetos do tipo `Pessoa`, poderíamos simplesmente adicionar uma função à referência `this`:

\[code language="js"\] var Pessoa = function(nome, email) { this.nome = nome; // verifica se o e-mail foi preenchido if (email) { this.email = email; }

this.fala = function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); }; }; \[/code\]

Este padrão é chamado de **Constructor paradigm** por Nicholas Zakas em seu livro _"Professional JavaScript for Web Developers"_.

Mas, se conseguimos adicionar informações na própria função construtora, por que adicionaríamos no `prototype` da função?

Acontece que, se adicionarmos este novo método na função construtora, a cada vez que instanciarmos um objeto a partir desta função esse método será carregado novamente na memória.

Quando instanciamos um objeto, este já tem uma referência(`__proto__`) para o prototype da função que o construiu. Ou seja, [só haverá um método na memória](http://javascript.crockford.com/private.html): o definido no prototype da função construtora!

Mas então como eu adiciono um método a partir do protótipo de uma função?

\[code language="js"\] var Pessoa = function(nome, email) { this.nome = nome; // verifica se o e-mail foi preenchido if (email) { this.email = email; } };

Pessoa.prototype.fala = function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); }; \[/code\]

E se eu quiser mais um método "anda"? Você precisaria novamente modificar o prototipo de Pessoa:

\[code language="js"\] var Pessoa = function(nome, email) { this.nome = nome; // verifica se o e-mail foi preenchido if (email) { this.email = email; } };

Pessoa.prototype.fala = function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); };

Pessoa.prototype.anda = function(){ console.log("Estou andando"); }; \[/code\]

Um outro modo de se adicionar métodos aos protótipos das funções sem ter que chamar o prototype todas as vezes é sobrescrevê-lo por completo:

\[code language="js"\] var Pessoa = function(nome, email) { this.nome = nome; // verifica se o e-mail foi preenchido if (email) { this.email = email; } };

Pessoa.prototype ={ constructor: Pessoa,

fala : function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); }, anda : function(){ console.log("Estou andando"); } }; \[/code\]

Note que, como sobrescrevemos o protótipo por inteiro, precisamos setar também qual é a função construtora daquele protótipo (no caso, `Pessoa`).

Agora só falta instanciarmos uma pessoa. Para isso, existe uma palavra chave `new`:

\[code language="js"\] var leo = new Pessoa("Leonardo", "leonardo.wolter@caelum.com.br"); leo.fala(); // Olá, meu nome é Leonardo e meu email é leonardo.wolter@caelum.com.br \[/code\]

Este modo de se criar um objeto onde há uma mistura da função construtora (com os atributos) e o prototype desta (com os métodos) foi apelidado por Douglas Crockford de **Pseudo-classical pattern** por tentar ser parecido com as classes de linguagens como Java.

O Pseudo-classical pattern não é o único jeito de criar um objeto. Alguns diriam que, apesar do ganho de memória e performance, o código acaba ficando poluído: não é tão lógico, para quem vem de outras linguagens orientadas a objetos como o Java, olhar para algumas propriedades dentro de uma função e outras fora dela (no prototype) e inferir que tudo isso representa uma classe.

Observação importante: O uso da palavra _new_ quando utilizando o Pseudo-classical pattern **não é dispensável**. Uma vez que você utilizou a referência _this_ dentro de sua função, caso não utilize o _new_ para instanciá-la, o `this` apontará para _window_, **criando variáveis globais!**. Para evitar esquecer da palavra `new` (não, você não será avisado pelo interpretador), é uma boa prática **sempre utilizar o padrão camelcase no nome de funções construtoras**, isso te dará uma dica de que você precisa do new para utilizá-la.

Talvez os defensores desta ideia gostem mais de um modo com uma sintaxe mais limpa: construir objetos utilizando funções que retornam simplesmente objetos literais:

\[code language="js"\] var pessoa = function(nome, email){ return { nome : nome, email: email, fala: function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); } }; }; \[/code\]

Deste modo, para obter uma pessoa, basta chamar a função acima:

\[code language="js"\] var leo = pessoa("Leonardo", "leonardo.wolter@caelum.com.br"); leo.fala(); // Olá, meu nome é Leonardo e meu email é leonardo.wolter@caelum.com.br \[/code\]

Uma vez que nós estamos simplesmente retornando um objeto literal, o uso da palavra chave _new_ se torna dispensável.

### Conclusão

É importante notar que o resultado **não é** exatamente o mesmo:

Quando utilizando o _Pseudo-classical pattern_, os métodos estão nos prototypes das funções construtoras, que só são carregadas uma vez, ocasionando uma melhora de performance e menor consumo de memória. Apesar disso, ele não tem uma forma de criar atributos/métodos privados e acessá-los em métodos públicos (dentro de prototypes), [uma séria desvantagem](http://javascript.crockford.com/private.html).

Enquanto isso, no método não prototype-based, nós não temos problemas com atributos/métodos privados e públicos uma vez que não temos prototypes. Mas, por não termos prototypes, todos os métodos e atributos estão no objeto construído e serão carregados sempre que chamarmos a função pessoa.

Estamos trocando **performance** por **estética e qualidade de código**. Assim, é importante verificar e escolher o modo mais adequado ao seu caso.
