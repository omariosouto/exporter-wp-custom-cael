---
title: "Segurança da sua aplicação e os frameworks: o ataque ao GitHub"
date: "2012-03-05"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quanto é necessário conhecer as suas ferramentas para garantia a segurança de sua aplicação?

Egor Homakov fez barulho durante o fim de semana. O russo [realizou um commit](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation) no branch master do repositório do Rails, passando parâmetros a mais no formulário de registro de chaves públicas do Github, algo conhecido como injeção por parâmetros, dado o [recurso de mass assignement](http://enlightsolutions.com/articles/whats-new-in-edge-scoped-mass-assignment-in-rails-3-1) do Rails. [Homakov fez o ataque de forma muito simples](http://homakov.blogspot.com/2012/03/how-to.html), uma vez que ele inferiu que existia um modelo na aplicação chamado `PublicKey` e que o mesmo possuía relacionamento com a classe `User`. Com isso, ele adicionou um campo novo no formulário, para indicar quem é o dono de tal chave pública. Isso foi feito através de um `input type="hidden" value="USER_ID" name="public_key[user_id]"`.

\[caption id="attachment\_4887" align="aligncenter" width="300" caption="Formulário de cadastro de chave pública no Github"\][![Formulário de cadastro de chave pública no Github](https://blog.caelum.com.br/wp-content/uploads/2012/03/cadastro_sshkey_github-300x185.png "cadastro_sshkey_github")](https://blog.caelum.com.br/wp-content/uploads/2012/03/cadastro_sshkey_github.png)\[/caption\]

Bastou submeter o formulário com o novo campo e o valor adequado, para que o usuário possuísse a chave pública que estava sendo cadastrada.

\[caption id="attachment\_4886" align="aligncenter" width="500" caption="Imagem que mostra o resultado do ataque ao Github"\][![Ataque ao Github](https://blog.caelum.com.br/wp-content/uploads/2012/03/github_hack.jpg "github_hack")](https://blog.caelum.com.br/wp-content/uploads/2012/03/github_hack.jpg)\[/caption\]

A resposta do GitHub, assim como a atitude de [suspender](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation) o usuário, foi [duramente criticada](http://chrisacky.posterous.com/github-you-have-let-us-all-down) por alguns desenvolvedores, sendo [revertida](https://github.com/blog/1069-responsible-disclosure-policy) poucas horas depois.

Mas não é só o [Rails que possui essa](https://github.com/rails/rails/issues/5228), digamos, facilidade-vulnerabilidade. O Spring já tinha uma [issue registrada para isso](http://www.springsource.com/security/cve-2010-1622) e o Sergio Lopes [já alertou sobre esse problema de injeção de parâmetros no modelo](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-injecao-de-novos-parametros/).

A solução no Rails é simples, mas pode ser facilmente esquecida e ignorada. Basta declararmos que não queremos que um determinado atributo seja populado pelo request, como numa lista negra:

\[code language="ruby"\] attr\_protected :user\_id \[/code\]

Apesar dessa forma funcionar, você pode simplesmente esquecer de marcar como protected um atributo novo, adicionado ao modelo. A [forma ideal](http://edgeguides.rubyonrails.org/security.html#whitelists-versus-blacklists) seria declarar todos os atributos que podem ser massivamente modificados, uma white list, caso o atributo não esteja marcado dessa forma, ele não será atualizado:

\[code language="ruby"\] attr\_accessible :name \[/code\]

Uma outra questão: de quem fica a responsabilidade nesse caso? O framework deveria permitir apenas white lists para popular objetos via request? Ou o desenvolvedor deveria sempre ficar atento a esses casos? De qualquer maneira, é mais um alerta de que sempre precisamos dominar o framework que utilizaremos.
