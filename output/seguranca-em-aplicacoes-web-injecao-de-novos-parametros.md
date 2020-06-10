---
title: "Segurança em aplicações Web: Injeção de novos parâmetros"
date: "2009-04-07"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Seguindo na série sobre S**egurança em aplicações Web**, vamos falar agora de **Injeção de Parâmetros**. O outro artigo da série é:

- [Segurança em aplicações Web: XSS](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-xss/)

* * *

![Segurança](https://blog.caelum.com.br/wp-content/uploads/2009/04/security.jpg "Segurança")Todo mundo sabe que um dos maiores problemas de segurança é não validar os inputs do usuário. O próprio [XSS](https://blog.caelum.com.br/seguranca-em-aplicacoes-web-xss/) de certa forma, está ligado a isso, como também o famoso SQL Injection e muitos outros ataques.

Mas sempre fica a dúvida de saber como tratar esses inputs. É um problema realmente complicado determinar que tipos de entradas são válidas em cada request então as pessoas acabam não fazendo nada e aí temos as falhas de segurança. E não adianta deixar tudo inseguro e depois acionar um advogado quando as coisas derem errado, temos que programar direito nossos sites!

Nesse artigo, vou mostrar um exemplo de injeção de parâmetros perigosos e que é muito comum de acontecer por causa da Web e dos frameworks modernos.

### Injeção de atributos

Se você usa qualquer framework Java para Web (VRaptor, Struts, Mentawai, Waffle, ...) deve estar acostumado com a facilidade de não se preocupar com _request.getParameter()_. Escrevemos um JavaBean e [o framework popula automaticamente os dados](http://vraptor.caelum.com.br/documentacao/vraptor3-o-guia-inicial-de-10-minutos/) pra gente seguindo os nomes do parâmetro.

Imagine um sistema onde as pessoas se cadastram em algum serviço online. Escrevemos uma classe _Usuario_:

\[java\] public class Usuario { private String email; private String senha; } \[/java\]

E, no formulário de cadastro, basta enviar os parâmetros **usuario.email** e **usuario.senha**.

/usuario/cadastra?usuario.email=example@example.com&usuario.senha=1234

Facilidade! Mas imagine que queremos ter também uma área administrativa e certos usuários com permissão para acessá-la:

\[java\] public class Usuario { private String email; private String senha; private boolean admin; } \[/java\]

No formulário de cadastro geral, a pessoa só cadastra email e senha, e no cadastro dentro da área de admin temos um checkbox a mais.

/admin/usuario/cadastra?usuario.email=example@example.com&usuario.senha=1234&usuario.admin=true

Fácil, certo? E extremamente inseguro. E se no cadastro dos usuários normais um usuário mal intencionado passa **&usuario.admin=true**? Bam! \[espero que nenhum leitor desse blog ainda ache que só porque o form do html não tem o campo, estamos seguros\]

O problema não existiria se ainda programássemos com _request.getParameter()_, já que na lógica dos usuários normais obviamente só pegaríamos os parâmetros _usuario_ e _email_. A questão toda aparece quando usamos as facilidades dos frameworks que saem pegando qualquer parâmetro e jogando nos beans.

### Resolvendo

Mas o que fazer? Não usar os frameworks e voltar para as Servlets? Óbvio que não. O primeiro passo é você rever todos os seus códigos atrás desse tipo de falha e perceber o quão grave ele é. Depois partimos para algumas medidas.

A solução mais elgante seria não ter nenhum atributo no seu bean que não pode ser populado pelo usuário. Crie várias classes, se são coisas realmente diferentes (_UsuarioNormal_ e _UsuarioAdmin_). É a abordagem "**white-list**", onde dizemos explicitamente quais são os campos aceitos e qualquer coisa fora disso é ignorada.

Às vezes, não é possível usar o mesmo bean para a pegar os parâmetros e para o Hibernate, por exemplo. Não tente forçar usar o mesmo bean sempre ou você pode cair em casos inseguros que custarão mais dinheiro que as duas classes que você escreveu a mais.

Uma outra solução, se você não pode criar várias classes, é resetar os campos sabidamente exploráveis antes de usá-la:

\[java\] class UsuarioLogic { // framework popula o parâmetro public void cadastra (Usuario u) { // reseto campos sensíveis u.setAdmin(false);

// ... uso o objeto } } \[/java\]

O problema sobre essa última abordagem é que você está trabalhando no esquema de "**black-list**", ou seja, você coloca os atributos problemáticos na lista negra e reseta-os um a um. A dificuldade com isso é saber se, no dia que aparecer um novo atributo problemático, você vai lembrar de resetar seu valor.

Em geral, quando se fala de segurança, soluções white-list são consideradas _mais seguras e future-proof_ que soluções black-list.

### Conclusão

Que devemos tratar inputs do usuário, todos sabemos. Mas devemos estar atentos a muitas possibilidades diferentes de injeções maliciosas. As injeções de novos parâmetros são um problema muito comum em especial nos frameworks web que usamos hoje em dia.

Vimos aqui a injeção por meio de beans em frameworks Web Java. Mas esteja certo de não ser vulnerável a injeção de parâmetros em outras frentes, mesmo usando _getParameter_ explicitamente ou usando outras plataformas que não Java.
