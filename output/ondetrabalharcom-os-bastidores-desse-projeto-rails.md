---
title: "OndeTrabalhar.com - os bastidores desse projeto Rails"
date: "2009-08-06"
author: "caueguerra"
authorEmail: "caueguerra@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Desde que [lançamos](https://blog.caelum.com.br/ondetrabalharcom-uma-iniciativa-da-caelum/) o [OndeTrabalhar.com](http://ondetrabalhar.com) [muitas pessoas tem nos perguntado](http://www.guj.com.br/java/133661-ondetrabalharcom-qualquer-coisa-de-ti-novo-portal-de-empregos) sobre quais tecnologias foram utilizadas para construí-lo, e aqui vamos falar um pouco sobre o projeto, seu ambiente e suas gems.

O [OndeTrabalhar.com](http://ondetrabalhar.com) está implantado em um servidor rodando [Passenger](http://www.modrails.com/) com [Ruby Enterprise Edition](http://www.rubyenterpriseedition.com/), utilizando o [httpd](http://httpd.apache.org/). Ainda utilizamos o plugin [ExceptionNotifier](http://github.com/rails/exception_notification/tree/master), para sermos avisados por email sempre que um erro 500 ocorre, e a gem [request-log-analyzer](http://github.com/wvanbergen/request-log-analyzer/tree/master), que analiza o arquivo de log e consegue mostrar estatísticas bem interessantes sobre o uso do sistema.

Utilizamos também as bibliotecas javascript [prototype](http://www.prototypejs.org/) e [script.aculo.us](http://script.aculo.us/), criamos muitas [rake tasks](http://rake.rubyforge.org/) para automatizar diversas tarefas e criamos algumas [regex](http://en.wikipedia.org/wiki/Regular_expression) bem malucas (com a ajuda da ferramenta [Rubular](http://www.rubular.com/), que permite que testemos uma regex muito facilmente).

Abaixo, segue a lista das principais gems e suas funcionalidades:

- [authlogic](http://github.com/binarylogic/authlogic/tree/master): autenticação e autorização
- [brazilian-rails](http://improveit.com.br/software_livre/brazilian_rails): validação de CNPJ, internacionalização de mensagens
- [will-paginate](http://wiki.github.com/mislav/will_paginate): paginação
- [acts-as-taggable-on-steroids](http://github.com/mattetti/acts_as_taggable_on_steroids/tree/master): criação e manipulação de tags
- [simple-captcha](http://expressica.com/simple_captcha/): geração de captcha
- [validates-unchangeable](http://locusfoc.us/2007/2/1/validates_unchangeable-plugin): gem que adiciona uma validação nos modelos, permitindo fazer a verificação de que determinado atributo não irá mudar nunca
- [rufus-scheduler](http://rufus.rubyforge.org/rufus-scheduler/): agendamento de tarefas
- [rmagick](http://rmagick.rubyforge.org/): geração de imagens (dependência do simple-captcha)
- [memcached](http://blog.evanweaver.com/files/doc/fauna/memcached/files/README.html): servidor de cache
- [acts-as-solr](http://github.com/railsfreaks/acts_as_solr/tree/master): busca textual

Além deles, ainda utilizamos algumas gems para conseguirmos fazer nossos testes, que cobrem exatos 95% do nosso código, uma excelente medida:

- [rspec-rails](http://rspec.info/rails/)
- [cucumber](http://cukes.info/)
- [webrat](http://github.com/brynary/webrat/tree/master)
- [selenium-client](http://seleniumhq.org/projects/ruby/): para testes com cucumber que envolviam javascript
- [rcov](http://github.com/spicycode/rcov/tree/master): geração de relatório de cobertura

Como todo projeto, aprendemos novos detalhes e problemas do dia a dia que enfrentamos com Rails. Adicionamos o resultado dessa experiência no [nosso curso de rails](http://www.caelum.com.br/curso/rr-71-ruby-on-rails/), e os detalhes mais avançados estão em um novo, que será lançado em breve.
