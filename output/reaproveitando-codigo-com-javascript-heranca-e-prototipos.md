---
title: "Reaproveitando código com JavaScript: herança e protótipos"
date: "2014-05-26"
author: "leonardo.wolter"
authorEmail: "leonardo.wolter@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando estamos desenvolvendo um sistema orientado a objetos, é comum termos uma certa preocupação com repetição de código.

Imagine que nós implementamos um sistema contendo uma entidade `Pessoa` (com nome e email), mas surgiu agora a necessidade temos um tipo mais específico de `Pessoa`: `PessoaFisica`, que contém todos os comportamentos de `Pessoa` e, além disso, sabe dizer seu CPF.

No mundo da orientação a objetos, seria comum utilizarmos herança para não precisar copiar os atributos e métodos da classe Pessoa. Mas e no JavaScript, como poderíamos implementar isso?

Existem diversos modos de se implementar herança em JavaScript. Nesse post, eu tentarei cobrir as seguintes maneiras:

- Prototype-chaining Inheritance
- Parasitic Combination Inheritance
- Functional Inheritance

### 1 - Prototype-chaining Inheritance

Antes de pensarmos na herança, vamos escrever uma função construtora utilizando o [Pseudo-classical pattern](https://blog.caelum.com.br/criacao-de-objetos-em-javascript/ "pseudo-classical pattern") para representar a `Pessoa` :

\[code language="js"\] var Pessoa = function(nome, email){ this.nome = nome; this.email = email };

Pessoa.prototype.fala = function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); }; \[/code\]

Mas note que, deste modo, nada impede que um desenvolvedor instancie uma pessoa com um email inválido:

\[code language="js"\] var leoInvalido = new Pessoa("Leonardo", "emailinvalido"); \[/code\]

Por estarmos utilizando uma função construtora, fica fácil resolver esse problema:

\[code language="js"\] var Pessoa = function(nome, email){ this.nome = nome; if(emailEhValido(email)) this.email = email }; \[/code\]

Bacana, já temos a nossa Pseudo-classe representando uma `Pessoa`. Mas, como disse anteriormente, nós precisamos de um outro tipo de pessoa: uma `PessoaFisica`, que possui CPF e sabe dizê-lo:

\[code language="js"\] var PessoaFisica = function(nome, email, cpf){ this.nome = nome; this.email = email; this.cpf = cpf; };

PessoaFisica.prototype.dizCpf = function(){ console.log(this.cpf); }; \[/code\]

Mas note que, do jeito que implementamos a função construtora `PessoaFisica`, ela não está verificando se o email foi preenchido corretamente como estávamos fazendo na `Pessoa`!

Vamos então reaproveitar a verificação que escrevemos anteriormente. Para isso, basta chamar a função Pessoa utilizando a nossa instância (`this`) como referência.

Essa técnica é chamada de **Constructor Stealing** :

\[code language="js"\] var PessoaFisica = function(nome, email, cpf){ Pessoa.call(this, nome, email); this.cpf = cpf; };

PessoaFisica.prototype.dizCpf = function(){ console.log(this.cpf); }; \[/code\]

Isso fará com que nossa `PessoaFisica` tenha todos os atributos que uma `Pessoa` teria!

[Neste outro post](https://blog.caelum.com.br/criacao-de-objetos-em-javascript/), mostrei que os prototypes servem justamente para adicionar métodos a todas as instâncias de determinada função. Note que é exatamente esse o comportamento que falta em nossa classe `PessoaFisica`! Queremos que ela tenha todos os métodos que uma instância da função `Pessoa` teria.

Então, para herdarmos os métodos de `Pessoa`, basta setarmos o prototype da `PessoaFisica` para uma instância de `Pessoa`:

\[code language="js"\] PessoaFisica.prototype = new Pessoa(); PessoaFisica.prototype.constructor = PessoaFisica; // corrige o ponteiro do construtor \[/code\]

Obs: Alguns diriam que é uma má pratica instanciar uma `Pessoa` sem argumentos para atribuir ao prototype de `PessoaFisica`, e eles estão certos! Deste modo, além de criarmos uma `Pessoa` inconsistente (com os atributos nome e email vazios), acabamos por instanciar um objeto somente para atribuir ao protótipo da PessoaFisica, consumindo tempo de execução e memória. Na estratégia _Parasitic Combination Inheritance_ veremos um jeito melhor de fazer isso!

Note que, a partir do momento que você sobrescreveu o prototype de `PessoaFisica`, você perdeu o método dizCpf!

Para resolvermos esse problema, basta declararmos o método _depois_ de sobrescrever o prototype:

\[code language="js"\] PessoaFisica.prototype = new Pessoa(); PessoaFisica.prototype.constructor = PessoaFisica; // corrige o ponteiro do construtor PessoaFisica.prototype.dizCpf = function(){ console.log(cpf); }; \[/code\]

Agora podemos, ao instanciar uma `PessoaFisica`, chamar o método `fala()` que foi declarado na função `Pessoa`:

\[code language="js"\] var leonardo = new PessoaFisica("Leonardo", "leonardo.wolter@caelum.com.br", "meucpf"); leonardo.fala(); // Olá, meu nome é Leonardo e meu email é leonardo.wolter@caelum.com.br leonardo.dizCpf(); //meucpf \[/code\]

Perceba que agora, para instanciar uma `PessoaFisica`, nós chamamos a função `Pessoa` duas vezes: ao setar o protótipo da função filha e ao instanciar a função filha.

Esta é a maneira mais comum de se implementar herança em javascript, chamada de **Prototype-chaining inheritance**.

Confira o resultado da nossa função `PessoaFisica` herdando `Pessoa`:

\[code language="js"\] var Pessoa = function(nome, email) { this.nome = nome;

if(emailEhValido(email)) this.email = email; }

Pessoa.prototype.fala = function(){ console.log("Olá, meu nome é "+this.nome+" e meu email é "+this.email); }

var PessoaFisica = function(nome, email, cpf){ Pessoa.call(this, nome, email); this.cpf = cpf; };

PessoaFisica.prototype = new Pessoa(); PessoaFisica.prototype.constructor = PessoaFisica; PessoaFisica.prototype.dizCpf = function(){ console.log(this.cpf); };

\[/code\]

Nós conseguimos o resultado esperado, mas há quem diga que nosso código acabou ficando poluído com o uso dos prototypes: ao utilizarmos a _Prototype-chaining Inheritance_ nós obtemos ganho de performance e diminuição da memória utilizada, mas como consequência nós acabamos danificando esteticamente o nosso código, como expliquei [aqui](https://blog.caelum.com.br/criacao-de-objetos-em-javascript/).

### 2 - Parasitic Combination Inheritance

Nós já vimos que, ao utilizar a _Prototype-chaining Inheritance_ pura, a cada vez que instanciamos uma `PessoaFisica`, chamamos duas vezes a função `Pessoa`. Será que não há um modo mais performático de se implementar herança?

Existe sim, mas antes de entendermos como ele é implementado, precisamos entender a função `Object.create`, [escrita por Douglas Crockford](http://javascript.crockford.com/prototypal.html), que usaremos em nossa implementação.

Essa é a função, que foi incluída ao ECMAScript 5:

\[code language="js"\] if (typeof Object.create !== 'function') { Object.create = function (o) { function F() {} F.prototype = o; return new F(); }; } \[/code\]

Basicamente o que ela faz é criar uma função construtora `F`, setar o protótipo desta para o objeto passado como parâmetro e retornar uma instância de `F`.

Ou seja, ela devolve um objeto **vazio** que herda (possui a propriedade `__proto__` igual ao) objeto passado!

\[code language="js"\] // Cria um objeto vazio com prototype igual ao de Pessoa var pessoa = Object.create(Pessoa.prototype); \[/code\]

Note que, dessa forma, deixamos de dar new em uma Pessoa sem argumentos para criar um objeto vazio com o protótipo de Pessoa, uma cópia!

Mas nós não queremos uma pessoaFisica que é igual a uma pessoa! Queremos que nosso objeto saiba dizer seu CPF, certo? Para isso, você poderia pensar em adicionar direto no objeto retornado:

\[code language="js"\] var pessoaFisica = Object.create(Pessoa.prototype); pessoaFisica.dizCpf = function(){ console.log("meuCPF"); } \[/code\]

Mas assim nós acabamos de perder a vantagem do prototype: vamos definir uma função para cada instância de `PessoaFisica`.

O que queremos, então, é definir os métodos da `PessoaFisica` e da `Pessoa` utilizando seus prototypes e **combiná-los** de um modo mais enxuto. Seria bacana uma função que encapsulasse todo esse comportamento, onde você passasse a função pai e a função filha e ela se responsabilizasse por fazer a função filha herdar a função pai, ou seja, combinar seus protótipos! \[code language="js"\] herda(Pessoa, PessoaFisica); // faz PessoaFisica herdar Pessoa \[/code\]

Legal! Então vamos implementar essa função:

\[code language="js"\] var herda = function(mae, filha){ // Faz uma cópia do prototipo da mãe var copiaDaMae = Object.create(mae.prototype);

// herda mãe filha.prototype = copiaDaMae;

//Ajusta construtor da filha filha.prototype.constructor = filha; } \[/code\]

Agora, para herdarmos `Pessoa`, basta chamar a função herda passando a `PessoaFisica` e a `Pessoa`:

\[code language="js"\] var PessoaFisica = function(nome, email, cpf){ Pessoa.call(this, nome, email); this.cpf = cpf; };

herda(Pessoa, PessoaFisica); \[/code\]

Note que mantemos a estrategia de _Constructor Stealling_ pois, sem ela, perderíamos as validações feitas no construtor do pai(como o _if(emailEhValido(email))_).

E, em seguida, definir os métodos da `PessoaFisica` em seu prototype:

\[code language="js"\] var PessoaFisica = function(nome, email, cpf){ Pessoa.call(this, nome, email); this.cpf = cpf; };

herda(Pessoa, PessoaFisica);

PessoaFisica.prototype.dizCpf = function(){ console.log(this.cpf); }; \[/code\]

Agora, para usar uma `PessoaFisica`, basta instanciá-la:

\[code language="js"\] var leonardo = new PessoaFisica("Leonardo", "leonardo.wolter@caelum.com.br", "meucpf"); leonardo.fala(); // Olá, meu nome é Leonardo e meu email é leonardo.wolter@caelum.com.br leonardo.dizCpf(); //meucpf \[/code\]

Perceba que agora, ao contrário da Prototype-chaining Inheritance, nós só chamamos a função `Pessoa` uma vez: ao instanciar a `PessoaFisica`.

Esta estratégia é descrita no livro [Professional JavaScript for Web Developers (de Nicholas C. Zakas)](http://www.wrox.com/WileyCDA/WroxTitle/Professional-JavaScript-for-Web-Developers-3rd-Edition.productCd-1118026691.html) como **Parasitic Combination**, um modo de implementar herança que o próprio Zakas descreveu como "the most optimal inheritance paradigm".

### 3 - Functional Inheritance

Imagine que, em vez de termos uma função construtora e darmos `new` nela para obtermos um objeto, nós simplesmente retornássemos um objeto utilizando notação literal (esse padrão é explicado [aqui](https://blog.caelum.com.br/criacao-de-objetos-em-javascript/ "functional pattern")):

Mas como vamos implementar herança sem utilizarmos prototypes?

Para isso, vamos utilizar uma estratégia um pouco diferente: em vez de adicionarmos os atributos e métodos nas instâncias de `pessoaFisica`, vamos fazer a função `pessoaFisica()` devolver uma `pessoa()` modificada:

\[code language="js"\] var pessoaFisica = function(nome, email, cpf){ var pessoa = pessoa(nome, email); pessoa.dizCpf = function(){ console.log(cpf); }; return pessoa; } \[/code\]

Uma observação importante: quando utilizada a Funcional Inheritance, em nenhum momento você vai utilizar o operador `new`, sendo assim, suas funções **não** deverão ter nada atribuido à referência _this_. Caso você utilize o `this` dentro de uma função e a chame sem o uso da palavra chave `new`, o `this` apontará para _window_, criando variáveis globais!

Essa é a estratégia chamada de **Functional inheritance**.

## Para saber mais

### Benchmark

Para comparar a performance destes três modos, eu escrevi um benchmark verificando 4 situações para cada um deles.

Os resultados podem ser observados neste [gist](https://gist.github.com/leocwolter/4f7299cfd7e7cb071e01)

Grande parte das vezes que eu executei o benchmark, realmente a Parasitic Combination Inheritance foi a mais performática!

Além disso, nos benchmarks onde eu testei a velocidade de instanciação, a Functional Inheritance demorou mais do que o dobro do tempo, comprovando o ganho de performance do uso de prototypes.

O codigo fonte dos benchmarks podem ser encontrados no meu [github](https://github.com/leocwolter/javascriptInheritance)
