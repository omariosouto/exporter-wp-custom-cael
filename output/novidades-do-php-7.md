---
title: "Novidades do PHP 7"
date: "2016-03-01"
author: "renan.saggio"
authorEmail: "renan.saggio@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Atualmente o PHP é uma linguagem de script muito conhecida e usada principalmente para o desenvolvimento de aplicações web. Como toda linguagem de programação, com os anos o PHP foi evoluindo e hoje suporta a grande maioria dos conceitos de orientação a objetos.

No começo de dezembro de 2015 foi lançada a versão 7 do PHP que trouxe diversas novidades. Nesse post vamos abordar algumas delas.

## Null coalescing operator

Temos o seguinte código, queremos fazer a paginação de alguns posts do blog:

\[code language="php"\] <?php use \\Caelum\\Dao\\PostDao;

$pagina = $\_GET\[‘pagina’\];

$postDao = new PostDao();

$posts = $postDao->listaPostsDaPagina($pagina);

\[/code\] Repare que estamos pegando número da página através de um parâmetro do tipo GET portanto teoricamente a URL _https://blog.caelum.com.br?pagina=1_ irá trazer o conteúdo da primeira página. Mas quando acessamos o blog da Caelum, você não digita o parâmetro pagina=1 na mão. Geralmente acessamos apenas URL e quando fazemos isso qual é o valor da variável **$pagina**? Ele vai receber um _null_ além de lançar um warning reclamando que o índice “página” não existe. E o que vai acabar sendo passado para o método listaPostsDaPagina? _Null_! Queremos garantir que se não existe um valor para a variável **$pagina** ele pegue os posts da primeira página, como podemos resolver isso? Simples! Vamos fazer um if:

\[code language="php"\] <?php use \\Caelum\\Dao\\PostDao;

$pagina = 1;

if(isset($\_GET\[‘pagina’\])) { $pagina = $\_GET\[‘pagina’\]; }

$postDao = new PostDao();

$posts = $postDao->listaPostsDaPagina($pagina);

\[/code\] Para escrever um pouco menos podemos usar um if ternário:

\[code language="php"\] <?php use \\Caelum\\Dao\\PostDao;

$pagina = isset($\_GET\[‘pagina’\]) ? $\_GET\[‘pagina’\] : 1;

$postDao = new PostDao();

$posts = $postDao->listaPostsDaPagina($pagina);

\[/code\] Ok escrevemos um pouco menos, mas quantas vezes você já precisou escrever esse tipo de if na sua vida como desenvolvedor? Se existe um valor para a variável use, se não assuma um valor padrão. É uma condição que acontece com bastante frequência no nosso dia a dia. Por isso agora na versão 7 do PHP temos o **null coalescing operator** que nada mais é do que um açúcar sintático para esse famoso if. A única alteração é no lugar de usar apenas um ponto de interrogação, vamos passar dois pontos de interrogação e o valor padrão:

\[code language="php"\] <?php use \\Caelum\\Dao\\PostDao;

$pagina = $\_GET\[‘pagina’\] ?? 1;

$postDao = new PostDao();

$posts = $postDao->listaPostsDaPagina($pagina);

\[/code\] Legal! Agora escrevemos menos para colocar valores padrões nas nossas variáveis. Você já teve esse problema no seu dia a dia?

## Definir tipo de retorno dos métodos

Outra feature interessante que temos no PHP 7 é **definir o tipo de retorno para os nossos métodos**. Por exemplo o que é método listaPostsDaPagina retorna? Vamos dar uma olhada na classe PostDao;

\[code language="php"\] <?php namespace Caelum\\Dao;

class ProdutoDao { private PDO $con;

public function listaPostsDaPagina($pagina) {

$offset = ($pagina - 1) \* 10; $select = “select \* from posts order by id limit {$offset},10”;

// cria o array que vai conter os posts encontrados $posts = \[\];

// lógica de buscar no banco

return $posts; } } \[/code\] Repare que o nosso método vai calcular a partir de qual registro ele vai buscar (offset) e ai sim pegar os próximos 10 registros. Mas qual é o retorno desse método? Olhando o código percebemos que ele vai devolver um array com vários produtos. Mas o que me impede de simplesmente trocar o return $posts; por return 1; por exemplo ? Nós não definimos qual é o tipo de retorno do nosso método para isso precisamos apenas adicionar dois pontos (:) e o tipo que deverá ser o retornado.

\[code language="php"\] public function listaPostsDaPagina($pagina) : array {

$offset = ($pagina - 1) \* 10; $select = “select \* from posts order by id limit {$offset},10”;

// cria o array que vai conter os posts encontrados $posts = \[\];

// lógica de buscar no banco

return $posts;

} \[/code\] Agora sim se tentarmos adicionar um return 1; ele vai lançar uma exceção do tipo TypeError \[code\] Uncaught TypeError: Return value of listaPostsDaPagina() must be of the type array, integer returned in /home/renan/Desktop/Caelum/Dao/ProdutoDao.php:37 Stack trace: #0 /home/renan/Desktop/Caelum/index.php(6): listaPostsDaPagina() #1 {main} thrown in /home/renan/Desktop/Caelum/Dao/ProdutoDao.php on line 6

\[/code\] Então agora no PHP 7 conseguimos garantir o tipo de retorno dos nossos métodos.

## Definir tipo para os parâmetros

Vamos dar uma olhada no parâmetro $pagina, o que garante que não vamos simplesmente executar o código abaixo:

\[code language="php"\] <?php use \\Caelum\\Dao\\PostDao;

$pagina = $\_GET\[‘pagina’\] ?? 1;

$postDao = new PostDao();

$posts = $postDao->listaPostsDaPagina(“primeira”);

\[/code\] Nada garante que o meu parâmetro $pagina deve ser do tipo inteiro, portanto vamos forçar esse tipo no parâmetro:

\[code language="php"\] public function listaPostsDaPagina(int $pagina) : array {

$offset = ($pagina - 1) \* 10; $select = “select \* from posts order by id limit {$offset},10”;

// cria o array que vai conter os posts encontrados $posts = \[\];

// lógica de buscar no banco

return $posts;

} \[/code\]

Agora além de conseguir definir o tipo de retorno dos nossos métodos também conseguimos definir o tipo dos parâmetros para tipos primitivos como int,float,boolean,string . Lembrando que já conseguíamos definir o tipo de parâmetros para ser uma instância de uma classe ou um objeto que respeite uma interface desde a versão 5 do PHP. Se você quer aprender um pouco mais sobre PHP, eu tenho alguns [curso de PHP no Alura](https://www.alura.com.br/cursos-online-php). Também existem outras novidades no PHP 7 que vou deixar para outro post.

Você utiliza alguma outra novidade do PHP 7 que gosta bastante? Tem alguma coisa que sente falta na linguagem que gostaria que tivesse na próxima verão?
