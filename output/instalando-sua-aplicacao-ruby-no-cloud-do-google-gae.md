---
title: "Instalando sua aplicação Ruby no cloud do Google (GAE)"
date: "2010-11-03"
author: "david.paniz"
authorEmail: "davidpaniz@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

É cada vez mais comum ouvirmos sobre instalação de nossas aplicações em um servidor nas nuvens. Boa parte da comunidade Ruby já está acostumada a utilizar o Amazon EC2 através do [Heroku](http://www.heroku.com/), mas pouco se fala sobre a instalação de nossos aplicações no cloud do Google, o [Google App Engine (GAE)](http://code.google.com/appengine/).

Embora o GAE não tenha uma opção oficial para instalarmos aplicações escritas em ruby, podemos roda-las em cima de uma instalação Java através do interpretador JRuby. Infelizmente, devido à versão modificada de java que é utilizado, o JRuby padrão também não funciona perfeitamente, mas já existe o projeto [appengine-jruby](http://code.google.com/p/appengine-jruby/) que nos provê o runtime tanto para produção que vai funcionar no GAE quanto para desenvolvimento permitindo simular o ambiente do Google nas nossas máquinas. Para instalar o `appengine-jruby` basta instalar a gem '`google-appengine`', mas tome cuidado pois curiosamente para instalar a gem temos que utilizar o ruby "comum" (interpretador MRI) e não o JRuby como pode-se imaginar.

Agora que já estamos com a ambiente configurado para rodar nosso código ruby, chegamos ao momento de escolher nossos frameworks. O `appengine-jruby` dá suporte total do [rack](http://rack.rubyforge.org/) para o GAE, então teoricamente qualquer framework ruby que utilize o rack como o [Ruby on Rails](http://rubyonrails.org/) ou o [Sinatra](http://www.sinatrarb.com/) deveria funcionar, mas infelizmente devido a outras limitações do GAE isso nem sempre é verdade, como é o caso do Rails 3 que ainda não está 100% compatível, mas em desenvolvimento. Por isso vamos fazer um exemplo utilizando o Rails 2.3.9.

Outro desafio que temos para fazer o nosso deploy é a camada de persistência pois o Google disponibiliza apenas o [BigTable](http://labs.google.com/papers/bigtable.html), um banco de dados não relacional orientado a coluna. Por isso não conseguimos usar o ActiveRecord, o ORM padrão utilizado pelo Rails. Mas podemos superar este desafio utilizando o [DataMapper](http://datamapper.org/), outra opção de ORM do mundo Ruby que também é compatível com o Rails. Vamos fazer um pouco do famoso Blog de 15 minutos e rodando no GAE:

### Instalando gems

`gem install google-appengine gem install rails -v "2.3.9" gem install rails_dm_datastore gem install activerecord-nulldb-adapter`

### Criando o projeto

Por ser uma estrutura de projeto um pouco diferente do que o padrão do rails, além do tradicional 'rails projeto' temos que executar um script de setup criado pelos mantenedores do `appengine-jruby`. `rails blog cd blog curl -O http://appengine-jruby.googlecode.com/hg/demos/rails2/rails239_appengine.rb ruby rails239_appengine.rb`

### Rodando os geradores

Vamos usar o gerador 'scaffold', assim como no exemplo famoso, mas dessa vez passando o parâmetro `--skip-migration` para não criar a migration, lembrando que não vamos usar um banco de dados relacional. E para completar todas as camadas rodamos o gerador do `DataMapper` depois. `./script/generate scaffold post titulo:string corpo:text --skip-migration ./script/generate dd_model post titulo:string corpo:text -f`

Após essas execuções você deve ter o arquivo `app/models/post.rb` como esse: \[ruby\] class Post include DataMapper::Resource

storage\_names\[:default\] = "Post" property :id, Serial property :titulo, String, :required => true, :length => 500 property :corpo, Text, :required => true, :lazy => false timestamps :at end \[/ruby\]

O último passo antes de testarmos a aplicação é adicionar o método **`to_param`** ao nosso modelo. \[ruby\] class Post include DataMapper::Resource #Mesmo código do anterior. Apenas adicionar o método abaixo def to\_param @id.to\_s end end \[/ruby\]

### Testando em desenvolvimento

Agora que estamos com o código 'pronto' podemos iniciar o servidor para testarmos, lembrando que não é preciso rodar migrations porque estamos usando o BigTable, basta executar: `./script/server.sh`

### Colocando em produção

O primeiro passo é acessar [https://appengine.google.com/](https://appengine.google.com/), entrar com seu usuário e criar uma aplicação nova lá. Feito isso, você precisa pegar o nome da aplicação e substituir na primeira linha do arquivo `WEB-INF/app.yml`. No nosso exemplo a aplicação criada se chama `jrubyblog`.

\[ruby\] application: jrubyblog version: 1 runtime: jruby #... continuação do arquivo \[/ruby\]

Após a configuração, para efetivamente instalar a aplicação em produção temos que executar o comando: `./script/publish.sh # Durante a instalação ele pedirá seu email e senha`

Agora sua aplicação Ruby on Rails já está instalada no GAE. Para acessá-la você precisa descobrir a URL. Será algo como `http://1.latest.NOME_DA_APLICACÃO.appspot.com/`. Para descobrir a url da sua aplicação volte em [https://appengine.google.com/](https://appengine.google.com/), clique na sua aplicação e entre no link 'Versions' onde terá os links das versões que você já instalou.

Para testar o código que fizemos neste post você pode acessar: [http://1.latest.jrubyblog.appspot.com/posts](http://1.latest.jrubyblog.appspot.com/posts)

UPDATE: 19/04/2011 Atualizamos a maneira de criar o projeto que agora precisa ser um projeto rails, deferente da versão antiga que exigia que fosse um diretório vazio.
