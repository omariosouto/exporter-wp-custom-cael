---
title: "Geradores no Rails e RSpec-Rails"
date: "2011-04-06"
author: "anderson.leite"
authorEmail: "anderson.leite@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O framework [Ruby on Rails](http://www.rubyonrails.pro.br/) é conhecido por facilitar e dar mais produtividade ao desenvolvimento web, estando preparado para uma série de necessidades comuns desse tipo de desenvolvimento. O vídeo que tornou Ruby on Rails famoso fazia uso de uma série de geradores, no caso os scaffolds, para produzir uma aplicação baseada em Posts e Comentários.

Existem diversos [geradores](http://guides.rubyonrails.org/generators.html) em Rails, sendo o mais conhecido o próprio scaffold, além de geradores para models, controllers e estrutura de plugins.

Um projeto bastante utilizado em aplicações Rails é o [RSpec](https://github.com/rspec/rspec). O RSpec é uma ferramenta de [BDD](http://pt.wikipedia.org/wiki/Behavior_Driven_Development) que possui, como tudo em Rails, algumas convenções de diretórios. Para trabalhar com o RSpec, precisamos colocar nossos testes em uma pasta chamada `spec` na raiz do projeto e então organizar nossos testes de **models, controllers, helpers** entre outros em pastas subsequentes. Além disso precisamos de um arquivo `.rspec` onde configuramos como serão visualizados os testes e outras opções.

Precisamos de uma estrutura pré definida para trabalhar com RSpec em uma aplicação Rails. É justamente dessa necessidade e da possibilidade de criar geradores que surge o projeto **[RSpec-Rails](https://github.com/rspec/rspec-rails)**. Utilizamos o RSpec-Rails para gerar os templates de nossos testes.

Para entender mais a fundo o funcionamento de tudo isso, nada como analisar o próprio fonte do RSpec-Rails, que é disponibilizado no [github](https://github.com/rspec/rspec-rails).

Todo código dos geradores fornecidos fica no diretório **lib/generators/rspec**. [![](https://blog.caelum.com.br/wp-content/uploads/2011/03/generators.png "generators")](https://blog.caelum.com.br/wp-content/uploads/2011/03/generators.png)  

O primeiro gerador utilizado normalmente é o `rails generate rspec:install`. Esse gerador cria o diretório `spec`, um arquivo de configurações chamado `spec/spec_helper.rb` e o `.rspec`. [![](https://blog.caelum.com.br/wp-content/uploads/2011/03/install.png "install")](https://blog.caelum.com.br/wp-content/uploads/2011/03/install.png)

Todo gerador rails segue uma convenção de arquivos. Todo gerador possui uma pasta chama **templates**, onde encontram-se os arquivos a serem criados na aplicação. Além disso, junto com a pasta templates deve existir o arquivo nome\_generator.rb (onde nome representa o nome do gerator em questão). Esse arquivo deve conter uma classe que herda de **Rails::Generators::Base**. [Não deixe de ver o fonde do gerador rspec:install no github.](https://github.com/rspec/rspec-rails/tree/master/lib/generators/rspec/install)

Seguindo a mesma linha, podemos criar geradores para várias partes da aplicação, como por exemplo para os testes de models do rspec. Os geradores aceitam parâmetros [como é usado no gerador dos models](https://github.com/rspec/rspec-rails/blob/master/lib/generators/rspec/model/templates/model_spec.rb) `rails generate rspec:model`.

Esse gerador recebe o nome do model para criar o arquivo de teste, como pode ser visto no fonte, ou mesmo um atributo a mais como a opção de criação de fixtures, que não é feita por default [como pode ser visto na classe Base.](https://github.com/rspec/rspec-rails/blob/master/lib/generators/rspec/model/model_generator.rb)

\[ruby\] module Rspec module Generators class ModelGenerator < Base argument :attributes, :type => :array, :default => \[\], :banner => "field:type field:type" class\_option :fixture, :type => :boolean

def create\_model\_spec template 'model\_spec.rb', File.join('spec/models', class\_path, "#{file\_name}\_spec.rb") end

hook\_for :fixture\_replacement

def create\_fixture\_file if options\[:fixture\] && options\[:fixture\_replacement\].nil? template 'fixtures.yml', File.join('spec/fixtures', "#{table\_name}.yml") end end end end end \[/ruby\]

Ao utilizar um projeto opensource você deve conhecer o que seu código faz, para assim, poder fazer as melhores escolhas e otimizações necessárias em cada situação. Analisar o código fonte de projetos no github ajuda muito na evolução de como programamos, analisando e entendendo como outros desenvolvedores pensaram para resolver problemas. Além disso, podemos contribuir com esses projetos e estar em contato com seus principais autores.

O Curso [RR-75 | Ruby e Rails avançados: lidando com problemas do dia a dia](http://www.caelum.com.br/curso/rr-75-ruby-rails-avancado/) mostra uma série de práticas avançadas no desenvolvimento de aplicações Ruby on Rails utilizando RSpec e outros frameworks.
