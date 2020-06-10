---
title: "Conhecendo o operator new e os protótipos no JavaScript"
date: "2012-05-17"
author: "ricardo.valeriano"
authorEmail: "ricardo.valeriano@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Como funciona exatamente o `new` no JavaScript? Podemos utilizá-lo na frente de uma invocação de uma função. Curiosamente, de qualquer função. Quando usado, o `new` cria um novo objeto e o atribui a palavra chave `this` de dentro do escopo da função invocada. Podemos então adicionar atributos a esse objeto: \[sourcecode lang="javascript"\] var Pessoa = function(nome, email) { console.log("criando nova pessoa"); console.log(typeof(this)); this.nome = nome; this.email = email; }

var joao = new Pessoa("João da Silva", "joao@da.silva"); // criando nova pessoa, object console.log(joao.nome); // João da Silva console.log(joao.email); // joao@da.silva \[/sourcecode\]

![](https://blog.caelum.com.br/wp-content/uploads/2013/05/javascript.png "javascript")

Apesar de não existir nenhuma instrução de retorno explícita na função `Pessoa`, é possível armazenar em `joao` uma referência para o objeto criado no momento da invocação. O operador `new` garante que o objeto referenciado pela palavra chave `this` seja retornado ao término da função.

Mas e se houver uma instrução de retorno explícita? Nesse caso o retorno é influenciado pelo operador `new`. Se o tipo do retorno for diferente de `object` então será retornado o objeto referenciado por this. Com isso em mente, veja no exemplo a seguir como o operador `new` é capaz de interferir no retorno de uma função. Vamos criar uma função que retorna um objeto do tipo `string`. Primeiro ela será invocada através do padrão simples e depois usando o `new`, veja que o objeto retornado muda de acordo com o padrão de invocação:

\[sourcecode lang="javascript"\] var Curso = function(nome) { this.nome = nome; return "curso "+ nome; } // Invocando como função var stringParaCS01 = Curso("CS01"); typeof(stringParaCS01); // "string" console.log(stringParaCS01); // curso CS01

// Invocando como construtor var objetoParaWD47 = new Curso("WD47"); typeof(objetoParaWD47); // object console.log(objetoParaWD47.nome); // WD47 \[/sourcecode\]

Quando a função referenciada por `Curso` foi criada, a intenção era usá-la sempre como uma função construtora. Mas nada nos impediu de usar o padrão simples de invocação com essa função. A única pista que temos de que ela deve ser usada como um construtor é a convenção de usar a primeira letra maiúscula no nome da referência. `Curso`, `Pessoa` indica que queremos que as funções sejam usadas para criar objetos, diferente de `alert`, `log`, etc.

Esse é um ponto que precisa de nossa atenção. Considere o caso da função `Pessoa` que adiciona atributos ao `this` quando invocada. Se por um acaso em algum momento esquecermos de que ela é na verdade uma construtora, e a invocarmos de forma simples: `var p = Pessoa()`, serão criadas as variáveis `nome` e `email` em `window`! Isso porque, como vimos no [post anterior](https://blog.caelum.com.br/as-multiplas-personalidades-do-this-em-javascript/), quando uma função é invocada de forma simples, a palavra chave `this` é associada com `window`.

Essa ambiguidade das funções criadas para serem construtoras é o alvo de uma das críticas quanto ao uso do operador `new` feita pelo [Douglas Crockford](http://www.crockford.com/), em seu excelente livro [JavaScript The Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742/ref=sr_1_1?ie=UTF8&qid=1336748220&sr=8-1). Mas e se a função retornar um objeto do tipo `object` explicitamente, usando a palavra chave `return`? Nesse caso um novo objeto será criado, atribuído ao `this`, mas não será retornando ao final da execução. No exemplo a seguir vemos que apesar de atributos serem criados no objeto referenciado por `this`, eles não são acessíveis fora da função, já que o retorno é um outro objeto: \[sourcecode lang="javascript"\] var Curso = function(nome, duracao) { this.duracao = duracao; var novoCurso = {"nome" : nome, "horario" : "8h00"}; return novoCurso; } var wd47 = new Curso("WD47", 20); typeof(wd47); // object typeof(wd47.duracao); // undefined console.log(wd47.nome); // WD47 \[/sourcecode\]

É um bom momento para lembrar que uma instância de `Array` no JavaScript, é na verdade do tipo `object`, podemos verificar isso através do seguinte código:

\[sourcecode lang="javascript"\] var novoArray = new Array(); typeof(novoArray); // object var outroArray = \["oi"\]; typeof(outroArray); // object \[/sourcecode\]

Portanto se sua função construtora retornar um `Array`, o uso do operador `new` na invocação não vai interferir no retorno. Mas por que se preocupar em usar uma função para criar objetos se é possível (e comum) cria-los com a notação literal? Na notação literal, não temos como garantir consistência do estado do objeto no momento de sua criação. Mesmo que todas as pessoas precisem ter um email válido, podemos escrever: \[sourcecode lang="javascript"\] var ricardo = { nome: "Ricardo Valeriano", email: "ricardovaleriano" // email invalido! }

\[/sourcecode\] Já com uma função construtora `Pessoa`, esses dados seriam necessariamente passados a ela: \[sourcecode lang="javascript"\] ricardo = new Pessoa("Ricardo", "ricardovaleriano"); \[/sourcecode\]

Sabemos que quando uma função é invocada através do operador `new`, um instância de `object` é criada e atribuída a `this`. Sendo assim podemos ter algum tipo de processamento envolvido na criação de um objeto e uma função construtora permite isolar essa lógica caso ela se torne complexa:

\[sourcecode lang="javascript"\] var Pessoa = function(nome, email) { this.nome = nome; if (notValid(email)) this.email = "invalido"; else this.email = email; } \[/sourcecode\]

Um outro aspecto importantíssimo sobre os objetos criados pelas funções construtoras tem um _link_ para o protótipo da função que o criou. Essa é uma característica marcante da linguagem, explorada por frameworks como o [jQuery](http://jquery.com/) para aumentar suas capacidades. A forma como o JavaScript lida com protótipos permite adicionar métodos em todos os objetos de determinado tipo, mesmo que esse tipo seja nativo da linguagem como é o caso de uma `string`.

Para entender melhor vamos alterar o protóptio da função `Pessoa` para garantir que, se um email não for passado como parâmetro no momento da criação, a pessoa seja criada com o email padrão contato@caelum.com.br. Para isso vamos alterar o protótipo de `Pessoa`, adicionando o atributo email com o valor padrão desejado no protótipo. Também vamos adicionar uma verificação em nossa função `Pessoa`: só iremos atribuir o parâmetro `email` para o atributo `email` em `this` caso esse atributo tenha sido passado como parâmetro:

\[sourcecode lang="javascript"\] var Pessoa = function(nome, email) { this.nome = nome;

// verifica se o e-mail foi preenchido if (email) { this.email = email; } }

Pessoa.prototype.email = "contato@caelum.com.br"

var ricardo = new Pessoa("Ricardo"); console.log(ricardo.email); // contato@caelum.com.br

var joao = new Pessoa("Joao da Silva", "joao@da.silva"); console.log(joao.email); // joao@da.silva \[/sourcecode\]

Perfeito! Usando essa característica da linguagem podemos alterar o protótipo das que constroem os tipos nativos do JavaScript, como as funções `String`, `Array`, `Number` ou outra qualquer. Qualquer atributo ou função adicionado ao protótipo de uma dessas funções ficará disponível em qualquer objeto do tipo gerado por elas. Vamos alterar o `prototype` da função construtora `String` e adicionar uma função a ele. A partir desse momento qualquer `string` criada tem disponível a função adicionada ao protótipo, veja: \[sourcecode lang="javascript"\] String.prototype.paraNumero = function() { if(this == "um") { return 1; } } console.log("um".paraNumero()); // 1 \[/sourcecode\]

Existem muitos outros detalhes envolvidos com o uso de protóptios em JavaScript, alguns deles abordados no curso [WD-47](http://www.caelum.com.br/curso/wd-47-programacao-front-end-avancada-javascript-jquery/) da Caelum. Nos vemos por lá!
