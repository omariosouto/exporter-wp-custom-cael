---
title: "Protegendo sua aplicação web contra Cross-Site Request Forgery"
date: "2015-09-15"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Construir uma aplicação web **segura** é uma tarefa árdua hoje em dia. Afinal, existem dezenas de tipos de ataques que podem ser realizados contra ela, sendo que a cada dia que passa novos tipos de ataques vão surgindo.

Boa parte dos ataques está relacionada com vulnerabilidades presentes na _infra estrutura_ da aplicação. É bem comum encontrarmos nosso ambiente de produção com **softwares desatualizados**, como por exemplo o Sistema Operacional, o Servidor de Aplicações e o SGBD. Outra vulnerabilidade comum ocorre quando esquecemos de remover ou alterar as **configurações default** de servidores de aplicação, como as páginas de administração e os usuários pré-definidos.

Mas a maior parte dos ataques está relacionada com vulnerabilidades presentes na própria aplicação, sendo responsabilidade do desenvolvedor ou arquiteto conhecer tais vulnerabilidades e principalmente como evitá-las.

Dentre os principais ataques podemos citar:

- SQL Injection
- Parameter Injection
- Cross Site Scripting(XSS)
- Cross Site Request Forgery(CSRF)
- Session Hijacking
- Man In The Middle
- Unvalidated Redirects/Forwards

Aqui no blog já escrevemos anteriormente sobre alguns desses ataques e como se previnir deles, além de outros tópicos relacionados com segurança. Caso você não tenha lido algum deles, vou listar aqui seus respectivos links:

- [Shared Key Authentication](https://blog.caelum.com.br/protegendo-sua-api-rest-via-shared-key-authentication)
- [XSS](https://blog.caelum.com.br/prevenindo-ataques-de-html-injection)
- [Parameter Injection](https://blog.caelum.com.br/seguranca-de-sua-aplicacao-e-os-frameworks-ataque-ao-github)
- [Criptografia](https://blog.caelum.com.br/guardando-senhas-criptografadas-em-java)
- [Parameter Injection 2](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-injecao-de-novos-parametros)
- [XSS 2](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-xss)

E neste post o foco será o ataque conhecido como [Cross-Site Request Forgery(CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), um ataque que ultimamente está bastante comum, sendo inclusive citado pela OWASP como um dos [Top 10](https://www.owasp.org/index.php/Top_10_2013-Top_10) ataques/falhas mais comuns em aplicações web, e por conta disso alguns frameworks como [Spring Security](http://docs.spring.io/autorepo/docs/spring-security/current/reference/html/csrf.html) e [Laravel](http://laravel.com/docs/4.2/html#csrf-protection) já possuem mecanismos para se proteger contra ele.

Para entender melhor como funciona o ataque CSRF vou utilizar um exemplo que mostrará como estamos vulneráveis a ele. Imagine que você desenvolveu uma aplicação web que possui uma funcionalidade para cancelar um processo. Para que um determinado processo seja cancelado o usuário deve acessar a página do formulário de cancelamento e informar o motivo e a data do cancelamento. O código desse formulário seria algo como:

\[code language="html"\] <form action="sistema/processos/cancelar" method="post"> <input type="hidden" name="processo.id" value="38"> <input type="date" name="processo.dataCancelamento"> <textarea name="processo.motivoCancelamento"></textarea>

<input type="submit" value="Cancelar Processo"> </form> \[/code\]

Ou seja, cancelar um processo significa disparar uma requisição _HTTP_ para a URL _sistema/processos/cancelar_, via método _POST_, levando três parâmetros: _processo.id_, _processo.dataCancelamento_ e _processo.motivoCancelamento_.

Agora vamos imaginar que um hacker conseguiu, de alguma maneira, descobrir sobre esse mecanismo de cancelamento de um processo em nosso sistema. Sabendo dessas informações, ele poderia criar uma página HTML idêntica à mostrada anteriormente ou usar algum aplicativo que saiba disparar requisições HTTP, como o cURL, e com isso conseguiria disparar uma requisição válida para o nosso sistema, podendo assim cancelar processos aleatórios, ou até mesmo um processo que ele conheça e seja de seu interesse que o mesmo fosse cancelado. Como nos proteger dessa vulnerabilidade?

A solução é simples: basta implementar no sistema um mecanismo de **autenticação**. Com isso, os usuários precisariam se autenticar no sistema antes de cancelar um processo. Mas agora como que o servidor saberá se uma determinada requisição foi originada de um usuário autenticado?

Aqui entra um detalhe técnico. Geralmente quando implementamos uma funcionalidade de autenticação trabalhamos com o conceito de **Sessões** no lado do servidor. Sempre que um usuário se autentica no sistema criamos para ele uma sessão, que nada mais é do que um pequeno espaço na memória do servidor. Cada usuário tem a sua sessão exclusiva, e para diferenciar uma sessão das outras o servidor atribui a ela um identificador único que, geralmente, é devolvido para o usuário em um **Cookie** que será armazenado em seu navegador, e será enviado automaticamente nas próximas requisições.

Agora se o hacker tentar disparar uma requisição será barrado pelo servidor, pois ele não possui uma sessão ativa, e além disso sua requisição não incluiu o cookie com o identificador da sessão. O único jeito dele efetuar o ataque seria fazendo com que algum usuário autenticado no sistema disparasse a requisição em seu lugar, sem que soubesse disso, ou seja, por meio de um usuário legítimo ele estaria _forjando_ uma requisição válida. Nesse caso como a requisição seria disparada a partir do navegador de um usuário legítimo que está autenticado no sistema, o cookie com o identificador da sessão seria enviado e o servidor aceitaria a requisição.

Esse é o famoso ataque **Cross-Site Request Forgery**. Para que um hacker consiga realizar esse ataque ele precisa induzir algum usuário a disparar uma requisição para o sistema a ser atacado. Isso pode ser feito com o envio de um email para a vítima contendo um link ou formulário que ao ser clicado/submetido dispara a requisição ao sistema. Aqui o hacker poderia utilizar [Engenharia Social](https://pt.wikipedia.org/wiki/Engenharia_social_(seguran%C3%A7a)) para induzir a vítima a realizar a ação desejada.

E agora? Como se proteger desse tipo de ataque?

A solução consiste em criar um mecanismo onde o servidor consiga identificar de alguma maneira se as requisições, mesmo sendo originadas de usuários legítimos e autenticados, foram de fato disparadas de maneira intencional e se foram realizadas dentro do próprio sistema.

O que os frameworks geralmente fazem para implementar tal mecanismo é gerar **Tokens** de segurança aleatórios, que são válidos apenas para a próxima requisição, e embuti-los nas páginas que possuem formulários, para que quando tais formulários sejam submetidos o servidor possa validar a requisição.

Assim, no nosso exemplo, quando um usuário entrar na tela de cancelamento de processo, o servidor gera um token aleatório e o devolve junto com a resposta, geralmente como um input hidden. Logo, o HTML final exibido no navegador do usuário seria algo como:

\[code language="html" highlight="4"\] <form action="sistema/processos/cancelar" method="post"> <input type="hidden" name="processo.id" value="38">

<input type="hidden" name="csrf\_token" value="09ktI70ogh1ah0A0S4961IE4QRE871ACGD73OVxuza6yeAInUG0aOYEqfc91nnvhuq7Tdv8"> <input type="date" name="processo.dataCancelamento"> <textarea name="processo.motivoCancelamento"></textarea>

<input type="submit" value="Cancelar Processo"> </form> \[/code\]

Repare que agora o formulário possui um novo input hidden chamado **csrf\_token**, contendo um valor gerado de maneira aleatória pelo servidor. Quando submetermos o formulário o servidor deve recuperar esse token e verificar se ele é válido, devendo rejeitar a requisição caso não seja.

No Spring Security conseguimos adicionar esse token em nossas páginas JSP com o seguinte código:

\[code language="html"\] <input type="hidden" name="${\_csrf.parameterName}" value="${\_csrf.token}"/> \[/code\]

Caso no seu JSP você esteja utilizando a taglib do próprio Spring, ao utilizar a tag _form:form_ o input hidden com o token será adicionado automaticamente.

Já no Laravel existem várias maneiras de se adicionar o token nas páginas, inclusive de modo automático. Caso você esteja trabalhando com o Blade como template engine, por exemplo, pode adicionar o token manualmente em suas páginas da seguinte maneira:

\[code language="html"\] <input type="hidden" name="\_token" value="{{ csrf\_token() }}"/> \[/code\]

Agora mesmo que o hacker consiga induzir o usuário a clicar em algum link ou formulário falso, a requisição será rejeitada pelo servidor, pois o formulário do hacker não vai possuir o token de segurança. O hacker até poderia tentar adicionar o token no formulário falso, mas para isso ele precisaria descobrir um valor válido para o token, algo que seria praticamente impossível, uma vez que cada token é gerado de maneira aleatória e somente é válido para uma única requisição.

Pronto! Agora estamos protegidos. :) Pelo menos para esse tipo de ataque, mas lembre-se de que existem dezenas de outros possíveis ataques.

E você, já precisou se proteger desse tipo de ataque alguma vez? Conte-nos como foi a experiência e se utilizou algum framework ou biblioteca.
