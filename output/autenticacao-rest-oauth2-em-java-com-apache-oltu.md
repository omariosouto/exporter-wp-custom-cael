---
title: "Autenticação REST OAuth2 em Java com Apache Oltu"
date: "2017-01-10"
author: "jopss.sossoloti"
authorEmail: "jopss.sossoloti@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Atualmente existem diversos projetos que estão sendo desenhados no formato SinglePage ou Microservices, com uma camada REST no servidor respondendo algum formato, geralmente JSON. Quando falamos em aplicações web, necessitamos de uma forma segura e usual de autenticar os usuários e atrelá-los a perfis (autorização). Normalmente pensaríamos em um objeto de sessão gerenciado por algum framework (SpringSecurity ou JAAS), mas isso quebra os conceitos de Restful (servidor sem estado).

Então, como implementar segurança sem ferir os conceitos e design da arquitetura?

A ideia central é que a identificação do usuário seja trafegado durante as requests sem mantê-las no servidor, e dessa maneira, podemos recuperá-la para tratar a segurança. A equipe do projeto pode solucionar isso desenvolvendo seu próprio código e formato de autenticação, trafegando um usuário e senha encriptada ou algo do tipo. No entanto, podemos usar um padrão já estabelecido chamado OAuth. Ele não é um protocolo de segurança, e sim um padrão de código em cima do HTTP, sendo uma arquitetura de referência desse tipo de solução. No entanto, existem outros conceitos bacanas como o Shared Key Authentication abordado [nesse artigo](https://blog.caelum.com.br/protegendo-sua-api-rest-via-shared-key-authentication/) do Rodrigo.

O padrão OAuth 1 foi criado em 2006 por Blaine Cook em uma solução open source ao Twitter, quando em 2007 foi utilizada pela Google e expandida ao mercado. Muitos acham que o OAuth 1 é muito burocrático de implementar, com regras fixas e sem muitos conceitos para web. Por isso em 2012 criou-se uma atualização chamada OAuth 2, a que iremos abordar nesse artigo. Na referência [RFC 6749](https://tools.ietf.org/html/rfc6749) temos todos os conceitos do OAuth2 e vamos resumi-los em dois tipos:

- **Tipo de Acesso por Password**: O servidor espera que o aplicativo (ou navegador) que necessite de acesso passe no corpo da request um usuario e senha previamente cadastrado no servidor. Com posse dessas credenciais, gera-se um número aleatório que identifica-o nas próximas requisições (o Token). Alguns sistemas não aproveitam o login e senha do usuário para gerar o token, e assim, entra-se no site para solicitar uma outra credencial, aumentando a sensação de segurança. Esse tipo de acesso é bacana para quando temos uma aplicação Rest distribuída ou Microserviços sem acessos a outros sistemas externos.
- **Tipo de Acesso por Código de Autorização**: Neste caso, o aplicativo (ou navegador) ao tentar acessar o recurso no servidor, recebe como response um redirect (HTTP 302) para uma URL previamente cadastrada. O redirect contem dados na request para poder voltar ao servidor após concluído o login com um código de autorização. A partir dos demais acessos gera-se um número aleatório que identifica-o nas próximas requisições (o Token). Esse tipo de acesso é o mais comum entre aplicativos distintos, por exemplo, quando usamos login pelo Facebook, Google ou Twitter.

Como visto, o OAuth2 é mais um conceito do que um formato de arquitetura, e dessa forma, não diz sobre como será a implementação. Um código OAuth2 pode ser implementado diferente se comparado com outro código OAuth2, mesmo que os dois sigam todos os preceitos.

Vamos simular aqui um servidor que recebe dados de uma mesma fonte usando o navegador, mas poderíamos fazer o mesmo para um outro aplicativo do mesmo site (como uma versão mobile). Sendo assim, exemplificaremos o tipo de acesso _password_, e para isso, iremos usar um framework Java chamado [Apache Oltu](https://oltu.apache.org/). Já usei-o em duas aplicações que estão em produção, e funciona bem para o que ele foi proposto.

A ideia do Apache Oltu é manipular a requisição e a geração do Token. O restante, sobre como armazenar esse Token, gerenciar o tempo de expiração ou bloqueio de requisições a recursos restritos, ficam a cargo do programador. Olhando apenas para a referência sobre o OAuth2, ele realmente não diz nada sobre essas outras partes. Cada aplicação pode fazê-las da forma que achar melhor. Por exemplo, onde armazenar o Token para recuperação entre requisições e identificação do usuário logado, você poderia optar por:

1. Armazená-lo em uma tabela no banco de dados.
2. Guardá-lo em uma persistência NoSQL.
3. Adicioná-lo em um mapa em memória.

O OAuth2 não vai impedir ou indicar a melhor solução. Em nosso projeto de exemplo, optamos por seguir alguns dos caminhos seguros, como armazenar o Token em uma tabela no banco de dados relacional e criar um Interceptador do Spring para gerenciar as requisições. Mas, novamente, escolha o que acredita ser o ideal para a sua aplicação. O exemplo pode ser conferido no github.

- Clone o projeto: git clone https://github.com/jopss/exemplo-oauth2.git
- Execute o maven: mvn clean install.
- Configure a base de dados PostgreSQL (“bd\_exemplo\_oauth” com o schema “main”).
- Importe na sua IDE e configure no Tomcat.
- Acesse pelo navegador: http://localhost:8080/exemplo-oauth2/

Para um cliente efetuar o login de uma aplicação que utiliza OAuth2, deve criar um POST contendo dentro do formulário alguns dados padrões. Se o nome das propriedades do form não for o esperado na especificação, o Apache Oltu retornará erros.

- client\_id: indica o id do APLICATIVO cliente.
- client\_secret: indica a senha doAPLICATIVO cliente.
- grant\_type: indica qual o tipo de autenticação. No exempo iremos de "password".
- username: login do USUÁRIO.
- password: senha do USUÁRIO.

Veja que além do tipo de autenticação e dados do usuário, temos a identificação do aplicativo cliente. Isso é bacana para gerenciar quem está acessando nosso servidor, podendo criar um formulário para tal, criando, inativando ou excluindo acessos.

### Código Java

Em código no servidor a recuperação dos dados do login é automatica dentro do framework, onde pedimos as propriedades sem mexermos na Request:

\[code language="java"\] OAuthTokenRequest oauthRequest = new OAuthTokenRequest(request); String appClientId = oauthRequest.getClientId(); String appClientSecret = oauthRequest.getClientSecret(); String senha = oauthRequest.getPassword(); String login = oauthRequest.getUsername(); \[/code\]

 

Após verificado em regra dados de usuários e permissão do aplicativo, se tudo ok, podemos gerar o Token:

\[code language="java"\] String accessToken = new OAuthIssuerImpl(new MD5Generator()).accessToken(); \[/code\]

 

Por fim retornamos esse Token e qualquer outro dado por uma resposta JSON.

\[code language="java"\] return OAuthASResponse.tokenResponse(HttpServletResponse.SC\_OK).setAccessToken(accessToken).setExpiresIn(10000) .setParam("nome", usuario.getNome()) .setParam("login", usuario.getLogin()) .setParam("perfil", usuario.getPerfil().getNome()) .buildJSONMessage(); \[/code\]

 

Isso é o suficiente para identificar usuários, aplicativos e retornar o Token para quem chamou o login com OAuth2. Como mencionado anteriormente, esse Token deve ser armazenado em algum local e faremos isso em um banco de dados relacional.

Para os clientes, nas próximas requisições, é necessário colocar como parâmetro do Header um atributo de autenticação:

- Authorization: Bearer TOKEN

Onde o TOKEN é o valor retornado pelo login. Agora, novamente quanto ao servidor, como verificar nessas próximas requisições se o Token passado está valido e a qual usuário está associado? Usamos o framework de novo, sem acessarmos diretamente o Request:

\[code language="java"\] OAuthAccessResourceRequest oauthRequest = new OAuthAccessResourceRequest(request, ParameterStyle.HEADER); String token = oauthRequest.getAccessToken(); \[/code\]

 

Se não houver os parâmetros corretos, retornará um erro. Com o Token em mãos, validamos se existe o mesmo no banco de dados e demais regras. Por fim, basta colocarmos esse código em um filtro HTTP ou Incerceptor do Spring e teremos uma verificação a cada requisição ao recursos.

Um recurso importante, que não iremos abordar aqui, é a revalidação de uma requisição. O Token possui um tempo de expiração e o cliente pode, por uma URL, solicitar a recriação da chave para ele continuar usando nas suas requisições. O Apache Oltu tem métodos para recriar o Token.

Em nosso exemplo demos uma inteligência um pouco maior associando também os perfis com uma anotação @Publico ou @Privado nos métodos do controle, indicando qual perfil acessá-lo. Isso está além do escopo do OAuth2 e fica a cargo da aplicação.

Veja ainda no [Github do projeto de exemplo](https://github.com/jopss/exemplo-oauth2) como testar os acessos simulando requisições cliente usando cURL, de forma simples e rápida.

### Segurança dos Dados

Muita gente que entende e estuda a solução acaba perguntando duas coisas:

1. Mas não é inseguro trafegar a cada requisição um Token que identifica um usuário?
2. Com posse de um Token gerado previamente, eu não poderia entrar na aplicação simulando outro usuário?

Para as duas respostas, é sim. Só que estranhamente as pessoas esquecem que a tão segura Sessão Web funciona exatamente dessa mesma forma, mas a geração do token (SessionID) e adição nos cookies é automática entre navegador e servidor. Com qualquer navegador podemos mexer nos cookies e pegar os dados de sessão simulando logins com outros usuários. Então, quanto a essas duas perguntas, funciona exatamente como é hoje.

E não esqueçam de configurar o HTTPS nos servidores. Isso sim faz muita diferença em relação a segurança :)

E você, como você pretege seus recursos Restful?
