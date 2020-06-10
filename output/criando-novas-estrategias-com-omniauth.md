---
title: "Criando novas estratégias com OmniAuth"
date: "2011-07-15"
author: "anderson.leite"
authorEmail: "anderson.leite@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Diversos sistemas espalhados pela web hoje tem seu login facilitado através de um outro sistema que o usúario utilize, como Facebook e Twitter. O [Agendatech](http://www.agendatech.com.br) é um exemplo onde fazemos o login através da conta de Twitter do usuário.

O protocolo utilizado para isso é o [OAuth](http://oauth.net/), que possui uma extensa documentação. Para facilitar o trabalho, em Ruby temos a gem [OmniAuth](https://github.com/intridea/omniauth), que torna muito simples o procedimento de login através de uma dessas contas do usuário, que são normalmente chamadas de providers.

> OmniAuth

O **OmniAuth** assume que o processo de autorização possui duas fases principais: **request** e **callback**. A fase de request é a etapa onde o provider é chamado. Esse provider autentica o usuário e pede a permissão do usuário para compartilhar os dados na aplicação. O provider então executa o callback com as informações necessárias.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/07/OAuth10A3.gif "OAuth10A3")](https://blog.caelum.com.br/wp-content/uploads/2011/07/OAuth10A3.gif)

> Configurando uma nova estratégia

O OmniAuth é flexivel e permite adicionar novos providers que não sejam os suportados por padrão na biblioteca. Com isso, podemos criar um esquema de logins pra qualquer provider ou mesmo criar uma estratégia para utilização off-line do sistema, quando não queremos de fato fazer a conexão em ambientes de testes.

O primeiro passo é indicar nossa nova estratégia e seus parâmetros de acesso, normalmente chaves geradas pelo próprio provider.

\[ruby\] Rails.application.config.middleware.use OmniAuth::Builder do # Parametros para autenticação e callback provider :caelum, "secret", "redirect URL" end \[/ruby\]

Até aqui, a biblioteca não conhece o provider oferecido, mas ensinamos ela através do módulo OmniAuth::Strategies, com o código abaixo:

\[ruby\] module OmniAuth module Strategies class Caelum include OmniAuth::Strategy

def initialize(app, secret, auth\_redirect, options = {}) @secret = secret @auth\_redirect = auth\_redirect super(app, :caelum, options) end

def request\_phase # redirecionamento para o provider end

def callback\_phase # dados recebidos end

end end end \[/ruby\] Criar uma nova estratégia para seu provider é um processo bem simples utilizando [OmniAuth](https://github.com/intridea/omniauth). Existem diversas estratégias criadas e você pode conferir a lista direto no [github do projeto](https://github.com/intridea/omniauth/tree/master/oa-oauth/lib/omniauth/strategies/oauth).

Conheça também diversos outros tópicos avançados de Ruby na [Formação Ruby da Caelum](http://www.caelum.com.br/cursos/rails/).
