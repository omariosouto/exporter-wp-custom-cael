---
title: "Prevenindo ataques de html injection"
date: "2013-05-28"
author: "francisco.sokol"
authorEmail: "francisco.sokol@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em qualquer aplicação web que desenvolvemos é comum receber dados dos usuários, armazenar no servidor para depois exibi-los a outros usuários. Um CRUD simples, certo? Apesar de simples, uma implementação ingênua pode apresentar uma vulnerabilidade grave e muito explorada por usuários mal intencionados na web: o HTML injection.

Vamos supor que nossa aplicação é um fórum onde os usuários criam posts, cada post tem um título e um texto com a descrição. De maneira simplificada, nosso formulário poderia ser assim:

\[code language="html"\] <form action="/posts" method="post"> <input type="text" name="titulo"> <textarea name="descricao"></textarea> </form> \[/code\]

No servidor, poderíamos simplesmente receber o post e salva-lo no banco:

\[code language="java"\] @Post("/posts") public void salva(String titulo, String descricao) { postDAO.salva(new Post(titulo, descricao); } \[/code\]

E depois exibir um post para outros usuários que visitassem o site, com a seguinte jsp:

\[code language="html"\] <h1>${post.titulo}</h1> <div>${post.descricao}</div> \[/code\]

Uma aplicação simples como essa já está sujeito a ataques de HTML injection. Se o usuário colocasse na descrição de seu post qualquer texto contendo código html como `<script>alert("olá");</script>`, esse código seria executado no navegador de todos os visitantes do seu post, possibilitando ataques de [XSS](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-xss/). Por esse motivo, você nunca deve **confiar em dados inseridos pelo seu usuário**.

**Primeira tentativa: escapando todos os dados**

A primeira solução é simples. Usando jsp e a jstl, podemos simplesmente escapar os dados inseridos pelo usuário. Nossa jsp com o seguinte código:

\[code language="html"\] <h1><c:out escapeXml="true" value="${post.titulo}" /></h1> <div><c:out escapeXml="true" value="${post.descricao}" /></div> \[/code\]

Resolvido, certo? Caso o usuário inserisse a tag `<script>` ela seria enviada ao navegador como `&lt;script&gt;`, e portanto não seria interpretada como uma tag html.

Essa solução é suficiente para o título da pergunta, mas suponha agora que gostaríamos de permitir que o usuário pudesse formatar a descrição de seu post. Seria interessante que ele pudesse dar destaque a certos trechos de seu post deixando trechos em negrito ou monospaced, por exemplo (uma feature bastante comum em qualquer aplicação que apresenta conteúdo do usuário).

Nesse caso, apenas escapar todo o html não basta, precisamos de uma solução mais esperta, permitindo que certas tags sejam interpretadas pelo navegador.

**Sanitizando o conteúdo**

Poderíamos resolver o caso específico do `<script>alert("olá")</script>` em java fazendo simplesmente: `descricao = descricao.replaceAll("<script>", "").replaceAll("</script>", "")`. Porém, veja que essa solução é bastante inocente e o problema é mais complexo do que parece. Um usuário mal intencionado pode facilmente entender como sua aplicação está sanitizando os dados testando algumas vezes o seu formulário. Suponha então que ele insira:

\[code language="html"\] <<script>script</script>>alert("olá de novo")</<script>script</script>> \[/code\]

Ao fazer as duas chamadas do `replaceAll`, o resultado é o seguinte: `<script>alert("olá");</script>`. Caímos de novo no mesmo problema. Podemos aprimorar nosso algoritmo ingênuo para solucionar esse caso, mas existe uma infinidade de corner cases que podemos esquecer. Além disso, uma solução robusta definitivamente não faz parte das prioridades da aplicação que estamos desenvolvendo.

Considerando a complexidade do problema, diversas bibliotecas foram desenvolvidas para sanitizar conteúdo a ser exibido na web. Uma das bibliotecas mais famosas e robustas é o [OWASP-Java](https://www.owasp.org/index.php/OWASP_Java_HTML_Sanitizer_Project). O Sergio Lopes já havia escrito sobre ela [nesse post](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-xss/), mas a API mudou bastante. Para utilizar é simples, você especifica quais tags você deseja liberar na entrada e ele constrói o sanitizador:

\[code language="java"\] PolicyFactory policy = new HtmlPolicyBuilder() .allowElements("strong", "i") .disallowElements("script") .toFactory(); \[/code\]

Assim, no código da nossa aplicação podemos utilizar o sanitizador na entrada do usuário:

\[code language="java"\] @Post("/posts") public void salva(String titulo, String descricao) { postDAO.salva(new Post(titulo, policy.sanitize(descricao)); } \[/code\]

Agora, na jsp podemos continuar escapando o título do post, já na descrição podemos exibir a descrição da forma como foi salva pelo nosso dao:

\[code language="html"\] <h1><c:out escapeXml="true" value="${post.titulo}"/></h1> <div>${post.descricao}</div> \[/code\]

Repare que podem haver diversos outros casos, outros pontos onde a injeção pode ser feita. XSS é um assunto que merece sua atenção.
