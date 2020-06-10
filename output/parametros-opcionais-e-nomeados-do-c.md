---
title: "Parâmetros opcionais e nomeados do C#"
date: "2013-08-07"
author: "victor.harada"
authorEmail: "victorkendy@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[![CSharp-Blood](https://blog.caelum.com.br/wp-content/uploads/2013/08/CSharp-Blood-300x194.png)](https://blog.caelum.com.br/wp-content/uploads/2013/08/CSharp-Blood.png)Quando estamos desenvolvendo uma aplicação muitas vezes precisamos passar diversos argumentos para um construtor ou método de uma classe. Em uma loja, por exemplo, gostaríamos de cadastrar um novo produto que terá as propriedades descrição, preço, nome, quantidade em estoque e fornecedor, para representar esse produto teríamos a seguinte classe:

\[code language="java"\] public class Produto { public String Descricao { get; set; } public String Nome { get; set; } public Decimal Preco { get; set; } public int Quantidade { get; set; } public String Fornecedor { get; set; } } \[/code\]

Para obrigar o preenchimento de todos os campos do produto, podemos criar um construtor que recebe essas informações:

\[code language="java"\] public Produto (String descricao, String nome, Decimal preco, int quantidade, String fornecedor) { this.Descricao = descricao; this.Nome = nome; this.Preco = preco; this.Quantidade = quantidade; this.Fornecedor = fornecedor; } \[/code\]

Mas algumas vezes não sabemos todas as informações necessárias para o cadastro do produto, nesses casos, queremos utilizar valores padrões para os campos:

\[code language="java"\] new Produto("sem descrição", "sem nome", 0.0m, 0, "sem fornecedor"); \[/code\]

O problema dessa abordagem é que teríamos que utilizar consistentemente esses valores padrão em todos os pontos do sistema. Nessa situação, podemos utilizar os parâmetros opcionais do C#.

Para definirmos um valor opcional no construtor, precisamos declarar o parâmetro e atribuir qual será o valor padrão:

\[code language="java"\] public Produto (String descricao = "sem descrição", String nome = "sem nome", Decimal preco = 0.0m, int quantidade = 0, String fornecedor = "sem fornecedor") { this.Descricao = descricao; this.Nome = nome; this.Preco = preco; this.Quantidade = quantidade; this.Fornecedor = fornecedor; } \[/code\]

Com isso, ao executarmos o código:

\[code language="java"\] Produto p = new Produto(); \[/code\]

Teremos um na variável p um produto preenchido com todos os valores padrão que foram definidos na declaração do construtor. Além disso, podemos definir, por exemplo, o nome e a descrição simplesmente enviando essas informações como parâmetros:

\[code language="java"\] // sobrescreve apenas o nome e a descrição Produto p = new Produto ("Apostila do curso de C# da Caelum", "Apostila de C#"); \[/code\]

Mas e se tivermos apenas o nome do produto? Como o primeiro argumento do construtor é a descrição, temos de fornecer a descrição para só depois passar o nome. Nessa situação, gostaríamos de chamar o construtor falando que o parâmetro passado representa o nome do produto, para isso, utilizamos os parâmetros nomeados do C#.

Quando queremos utilizar um parâmetro nomeado, precisamos falar o nome do parâmetro seguido de ':' e depois o valor que será colocado no parâmetro, no exemplo, poderíamos chamar o construtor do produto da seguinte forma:

\[code language="java"\] Produto p = new Produto(nome: "Apostila de C#"); \[/code\]

Com esse código, construímos um produto definindo apenas o nome, as outras informações ainda estão com os valores padrão definidos no código do construtor.

Para mais informações sobre os argumentos nomeados e opcionais, consulte o [guia de programação da msdn](http://msdn.microsoft.com/en-us/library/dd264739.aspx). Conheça também os [cursos de .NET](http://www.caelum.com.br/cursos-dotnet/) da Caelum!
