---
title: "Seja um artesão da web com Laravel, o framework MVC do PHP"
date: "2015-07-28"
author: "joao.santana"
authorEmail: "joao.santana@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Independente da linguagem ou tecnologia que estamos usando, um conceito global é: **não queremos ficar nos preocupando com infraestrutura**. É aí que os frameworks entram. Eles nos ajudam e muito a agilizar o processo de desenvolvimento, de forma organizada, evitando repetições de código e muito mais. O Laravel é um fremework PHP extremamente produtivo, com um ecossistema bem grande, além de ser [cada vez mais popular e utilizado pelo mercado](http://www.sitepoint.com/best-php-frameworks-2014/).

### Instalação e primeiro projeto

Para começar, você pode fazer o download e seguir o passo a passo de instalação do framework, que [está bem detalhado em seu site](http://laravel.com/docs/5.0/installation). O Laravel utiliza o [Composer](https://getcomposer.org/) como gerenciador de dependências interno, e por esse motivo, você também precisará instalá-lo.

Uma vez que estiver instalado, podemos criar novos projetos utilizando o comando **laravel new nome-do-projeto**. Toda uma estrutura de diretórios será criada, com o Laravel e suas dependências necessárias já instaladas. Legal, não é? Esse é um dos pontos que gostamos bastante do framework, você precisa se preocupar pouquíssimo com infraestrutura e configurações, já que ele adota uma serie de convenções pra te ajudar com esse trabalho. Para criar um projeto chamado **loja**, basta executar o comando:

\[code\] laravel new loja \[/code\]

O texto _Application ready! Build something amazing_ será exibido e pronto, tudo está configurado e preparado pra uso. Ao acessar o pasta loja, você verá que já existem diversos diretórios e arquivos de configuração. [Você pode ler mais sobre essa estrutura inicial nesse link da documentação](http://laravel.com/docs/5.0/structure), mas em resumo grande parte do trabalho acontecerá na pasta app, que concentra seus controllers, modelos, validatores e etc.

### Executando o Laravel com Artisan

Para testar nossa instalação, podemos usar o comando **php artisan serve**, que executará a aplicação no servidor de desenvolvimento do PHP. O Artisan é uma ferramenta de linha de comando que já vem com Laravel. No decorrer do post veremos que ele faz bem mais do que apenas subir o servidor. Após executar esse comando, você poderá acessar sua aplicação pelo endereço http://localhos:8000. Se tudo correu bem, uma página com o logo do Laravel e uma frase motivadora ficará disponível:

![tela inicial laravel 5](https://blog.caelum.com.br/wp-content/uploads/2055/05/tela-inicial-laravel-5.png)

### Configurando as rotas e verbos HTTP

Configurar as rotas com o Laravel é extremamente simples. Há um arquivo chamado **routes.php** no diretório `app\Http`, responsável por fazer esse trabaho. Um exemplo de mapeamento seria:

\[code language="php"\] Route::get(‘produtos’, ‘ProdutosController@index’); \[/code\]

Dessa forma estamos ensinando ao Laravel que quando alguém fizer uma requisição do tipo GET para acessar a URI `/produtos`, o método **index** de nosso controller deverá ser executado. O padrão é:

\[code language="php"\] Route::methodHttp(‘URI’, ‘NomeDoController@function’). \[/code\]

Também podemos registrar uma nova rota do tipo POST que apontará para o método **create** de nosso controller. Veja que a URI será a mesma.

\[code language="php"\] Route::post(‘produtos’, ‘ProdutosController@create’);. \[/code\]

O mesmo pode ser feito para outros verbos como PUT, PATCH e DELETE, mas nesses casos vocês precisará passar um parâmetro **\_method** com o método HTTP que severá ser utilizado. Um exemplo seria:

\[code language="php"\] <input type="hidden" name="\_method" value="DELETE">. \[/code\]

### Path param e validação com regex

Para o caso de um método de busca, que deve nos responder com os detalhes de um produto específico, será necessário enviar via request um parâmetro com o id do produto. É natural fazer isso direto pela URI, como no exemplo: `/produtos/1`. Neste caso, o produto com ID 1 deverá ser exibido. Para fazer isso, ou seja, passar parâmetros pela URI, faremos:

\[code language="php"\] Route::get(‘produtos/{id}’, ‘ProdutosController@show’); \[/code\]

Podemos ainda utilizar o método **where**, passsando uma expressão regular para validar o tipo e formato desse parâmetro `{id}`.

\[code language="php"\] Route::get(‘produtos/{id}', ‘ProdutosController@show')->where('id', '\[0-9\]+'); \[/code\]

### Criando o primeiro controller

Podemos criar esse `ProdutoController` manualmente, ou utilizando o **php artisan**, que elimina bastante do código de _boilerplate_ como imports, estrutura da classe e etc. Veja como é simples:

\[code language="php"\] php artisan make:controller ProdutoController --plain. \[/code\]

O parâmetro **\--plain** indica que queremos um controller vazio, caso contrário ele criará uma serie de métodos comumente utilizados em controllers -- basicamente um CRUD. Como resultado do comando, a classe `ProdutoController` será criada em `app\Http\Controllers`, já com o seguinte conteúdo:

\[code language="php"\] <?php namespace App\\Http\\Controllers;

use App\\Http\\Requests; use App\\Http\\Controllers\\Controller;

use Illuminate\\Http\\Request;

class ProdutoController extends Controller { } \[/code\]

### Implementando uma listagem de produtos

Nosso primeiro método será o index, com a listagem de produtos. Por enquanto esse método retornará apenas um `h1`, com o texto “Lista de produtos".

\[code language="php"\] class ProdutoController extends Controller {

public function index() { return "<h1>Lista de Produtos</h1>"; } } \[/code\]

Vela lembrar que esse método já foi mapeado no arquivo de rodas, com a URI /produtos, portanto ao acessá-la teremos o html com resultado esperado:

![h1 lista de produto](https://blog.caelum.com.br/wp-content/uploads/2055/05/h1-lista-de-produto.png)

Isso já é legal, mas na verdade não queremos visualizar só um título, mas sim uma página com todos os produtos carregados de nosso banco de dados. É aí que entra a camada de modelo.

### Criando um novo modelo com artisan

Precisamos criar um modelo `Produto`, que representará nossa tabela de `produtos` do banco de dados. Para criar um controller usamos o `artisan make:controller`, não é? Veja como é simples criar um modelo:

\[code language="php"\] php artisan make:model Produto \[/code\]

A classe produto e toda sua estrutura inicial será criada:

\[code language="php"\] <?php namespace App;

use Illuminate\\Database\\Eloquent\\Model;

class Produto extends Model { } \[/code\]

Rápido é fácil. Todo modelo (classes que herdam de `Model`, como nesse caso) reflete em uma tabela de nosso banco de dados, que por padrão terá o mesmo nome da classe, mas com letras minúsculas e no plural. Ou seja, a classe **Produto** acessa dados da tabela **produtos**. Você pode sim configurar outro nome de tabela, adicionando uma propriedade `$table` na classe.

\[code language="php"\] protected $table = 'nomeDaTabela'; \[/code\]

Além de criar o modelo, precisaremos de um mínimo de configurações para permitir que nossa app em Laravel se comunique com o banco de dados. Para isso, basta acessar o arquivo `database.php` e mudar as configurações [como nesse exemplo](https://github.com/Turini/estoque-laravel/blob/master/config/database.php#L55-L65).

### Conheça o poderoso Eloquent ORM

Tudo pronto, já podemos usar e abusar dos métodos de acesso ao banco da classe `Model`. Por sinal, você deve ter percebido que essa classe vem de um namespace com a palavra **Eloquent**, que é o nome do framework ORM utilizado no Laravel. Ele torna nossa vida bem simples, oferecendo uma serie de métodos declarativos para as mais diferentes operações do banco de dados. Por exemplo, para listar todos os produtos em nosso método `index`, faremos:

\[code language="php"\] public function index() { $produtos = Produto::all(); return $produtos; } \[/code\]

Veja que o método **all** quem faz toda a mágica, transformando essa chamada em instruções SQL que serão executadas no banco. Para testar, repare que estamos retornando agora a lista de produtos. Se acessarmos o path `/produtos` novamente, veremos um JSON com todas as informações dos produtos persistidos no banco de dados.

![json_de_produtos](https://blog.caelum.com.br/wp-content/uploads/2055/05/json_de_produtos.png)

Além do all, existem diversos métodos fornecidos pelo Eloquent para as mais diversas operações com o banco de dados. Métodos como save, update e find com certeza farão parte de seu dia a dia. Você pode ver esses e outros métodos em:

http://laravel.com/docs/5.0/eloquent

### Trabalhando com views

No lugar de um JSON, queremos que seja apresentada uma view com o html da listagem de produtos. Para isso basta mudar o retorno do método para `view('nomeDaSuaView')`, como no exemplo:

\[code language="php"\] public function index() { $produtos = Produto::all(); return view('produto.lista'); } \[/code\]

Mas além disso, queremos deixar essa lista de produtos acessível para nosso HTML da view, portanto usaremos o método `with`:

\[code language="php"\] public function index() { $produtos = Produto::all(); return view('produto.lista')->with("produtos", $produtos); } \[/code\]

Note que o retorno é dado como `‘produto.lista’`, isso significa que o arquivo **lista.php** estará presente dentro de uma pasta chamada produto. O caminho completo será `resources\views\produto\lista.php`. Esse arquivo fará um `foreach` simples, iterando pelo arrays de produtos para montar as linhas de uma tabela:

\[code language="php"\] <h1>Lista de Produtos</h1>

<table class="table"> <?php foreach ($produtos as $produto) {?> <tr> <td><?= $produto->nome ?></td> <td><?= $produto->descricao ?></td> <td><?= $produto->valor ?></td> <td><?= $produto->quantidade ?></td> </tr> <?php } ?> </table> \[/code\]

### Views mais poderosas com blade

A maneira que o php disponibiliza informações na view pode ser um pouco confusa, bastante verbosa e temos que poluir o html com abertura e fechamento de chaves. É muito fácil se confundir, não é? E que tal fazer assim:

\[code language="php"\] @foreach ($produtos as $produto) <tr> <td>{{$produto->nome}}</td> <td>{{$produto->descricao}}</td> <td>{{$produto->valor}}</td> <td>{{$produto->quantidade}}</td> </tr> @endforeach \[/code\]

Veja que a mudança foi pouca, mas bem significativa. Ler um `@endforeach` é bem mais explicativo e de fácil compreensão do que um `<\?php } ?>`, não é? Para usar essa sintaxe em suas views, basta adicionar a extensão **blade**, que é a tecnologia de template padrão desse framework. O arquivo, que se chamava `lista.php`, passará a se chamar `lista.blade.php`. Basta renomear, sem nenhuma configuração adicional. E o blade vai muito além do que simplificar a sintaxe, ele abre caminho pra diversas outras possíbilidades, como por exemplo utilizar herança entre views. Para herdar da view padrão, que é criada automaticamente junto com o projeto, basta fazer um `@extends('app')` e adicionar o conteúdo como a seguir:

\[code language="php"\] @extends('app')

@section('content') <h1>Lista de Produtos</h1>

<table class="table"> @foreach ($produtos as $produto) <tr> <td>{{$produto->nome}}</td> <td>{{$produto->descricao}}</td> <td>{{$produto->valor}}</td> <td>{{$produto->quantidade}}</td> </tr> @endforeach </table> @stop \[/code\]

O conteúdo deve estar entre `@section(‘nome’)` e `@stop`. Você pode ver mais sobre blade, herança de layout e suas extruturas de controle em:

http://laravel.com/docs/5.0/templates#blade-templating

Ao executar esse código, teremos como resultado uma view parecida com:

![listagem de produtos final](https://blog.caelum.com.br/wp-content/uploads/2055/05/listagem-de-produtos-final.png)

### Uma infinidade de recursos

Você possívelmente percebeu que no menu da view padrão, que herdamos em nossa listagem, já existem links para login e registro de usuários. Não é só o link, **essas funcionalidades já foram implementadas pra você!** Além disso, todo um mecanismo de autenticação também já está implementado, pronto para uso. Legal, não é? No máximo você precisará mudar um ou outro detalhe, dependendo da necessidade do seu projeto, mas com toda certeza essa base lhe será bastante útil. Esses são apenas alguns dos muitos detalhes em que o Laravel te ajuda, tornando mais produtivo e eficaz.

Quer conhecer mais sobre Laravel? Escrevemos [um livro](http://www.casadocodigo.com.br/products/livro-laravel-php) e também [cursos online](https://www.alura.com.br/cursos-online-php) sobre o assunto. Além disso, esse é o framework utilizado em [nosso curso de PHP e Orientação a Objetos](https://www.caelum.com.br/curso-php-mysql/). E você, já tem usado Laravel? Que outros frameworks tem usado em seus projetos PHP?
