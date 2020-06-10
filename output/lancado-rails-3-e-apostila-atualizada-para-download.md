---
title: "Lançado Rails 3 - e apostila atualizada para download"
date: "2010-09-02"
author: "david.paniz"
authorEmail: "davidpaniz@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Depois de dois anos de trabalho da junção do Merb com o Rails, [saiu nesse fim de semana a versão final do Rails 3](http://weblog.rubyonrails.org/2010/8/29/rails-3-0-it-s-done). E, para comemorar, a Caelum acaba de liberar a apostila atualizada do [curso RR-71 Ruby On Rails 3](http://www.caelum.com.br/curso/rr71) para download.

![Rails 3](https://blog.caelum.com.br/wp-content/uploads/2010/09/Schermata-2010-09-01-a-6.11.02-PM-300x164.png "Rails 3")

Entre as principais novidades, temos a nova API de query do `ActiveRecord` (ARel), a nova sintaxe para definição de rotas, o unobstrusive JavaScript para desacoplamento do prototype como biblioteca padrão, além de melhorias no `ActionController` e `ActionMailer`. Mas a principal mudança nessa nova versão é seu _agnosticismo_, agora é possível substituir partes do Rails por outros frameworks de sua preferencia sem precisar sofrer como antigamente e quem escreve essas outras opções não precisa mais fazer hacks e código de difícil manutenção, tornando o Rails um verdadeiro ecossistema.

Por exemplo, o **ARel** nos provê uma nova maneira de montarmos as queries através de uma DSL mais elegante que os finders do Rails 2, e também possibilita adicionarmos filtros extras a uma query já existente como no exemplo abaixo:

\[ruby\] juridicas = Cliente.where(:tipo => "PJ").order("nome") inativos = juridicas.where(:ativo => false) \[/ruby\]

Para a nova definição de rotas, o Rails 3 traz diversas melhorias de sintaxe, entre elas, na declaração das rotas nomeadas:

\[ruby\] match 'cadastro', :controller => 'usuarios', :action => 'new' # se acessar /cadastro vai para a action 'new' no usuarios\_controller, mas não cria os helpers

match 'cadastro', :controller => 'usuarios', :action => 'new', :as => 'rota\_cadastro' # se acessar /cadastro vai para a action 'new' no usuarios\_controller, mas agora ganhamos os # helper\_methods rota\_cadastro\_path e rota\_cadastro\_url \[/ruby\]

Se você ainda não conhece Rails, e quer começar já por essa nova versão, baixe agora a apostila do curso [RR-71 de Ruby on Rails](http://www.caelum.com.br/curso/rr71), que foi reformulada durante esses últimos meses acompanhando todas as mudanças no framework. Está liberada a versão da apostila beta. Envie seus comentários e sugestões!
