---
title: "VRaptor 3.0 final lançado!"
date: "2009-10-05"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![vraptor3 icon](http://vraptor.caelum.com.br/images/boxVraptor-trans.png)](http://www.vraptor.com.br/)

Depois de 8 meses de intenso desenvolvimento, e quase 2 meses depois do [primeiro beta público](https://blog.caelum.com.br/vraptor-3-disponivel-para-download/), o [framework web MVC VRaptor 3](http://www.vraptor.com.br/) final está disponível para donwload. O site oficial foi inteiramente reformulado, com uma [nova versão da palestra de apresentação do framework](http://vimeo.com/5961030) e uma [extensa documentação](http://vraptor.caelum.com.br/documentacao/vraptor3-guia-de-1-minuto/).

O princípio básico do VRaptor é que você pode expor os métodos do seu controlador de maneira **RESTFul**, através de simples anotações. No exemplo a seguir, acessando a URI `/usuarios/adiciona` por `POST`, teremos esse método `insere` invocado e um objeto `Usuario` populado através dos parâmetros `usuario.nome`, `usuario.endereco`, assim por diante:

\[java\] @Post @Path("usuarios/adiciona") void insere(Usuario usuario) { ... } \[/java\]

Se seu retorno não fosse `void`, o que é retornado é exposto ao seu view, através de um atributo de request. A partir desse simples modelo temos acesso aos mais variados recursos: a **injeção de dependências** é feita pelo construtor, e há total **integração com Spring**, permitindo a criação fácil de **testes** unitários. E há **suporte fácil a Hibernate e JPA**, através de `ComponentFactories` já embutidas no framework, basta você registrá-los e receber `Session`/`EntityManager` no construtor. Validação, conversores, redirecionamentos, URIs parametrizadas e todo mais prossegue da mesma maneira elegante.

Diversas **empresas já estão usando o VRaptor 3** desde suas versões beta: [Wine.com.br](http://www.wine.com.br/), a maior empresa online de vinhos do Brasil, através da [Giran.com.br](http://www.giran.com.br/), a [Locaweb](http://www.locaweb.com.br/), a [AgenciaClick](http://www.agenciaclick.com.br), a [Defferrari](http://www.defferrari.com.br/), entre outros. Há desenvolvedores rodando o [VRaptor 3 em um cluster com 32 máquinas](http://guj.com.br/posts/list/139238.java#752484) e outras no cloud do Google App Engine!

**Como começar já?**

Faça o download do [projeto vazio (blank project)](http://code.google.com/p/vraptor3/downloads/list) já preparado para o Eclipse, e siga as instruções do [guia de 1 minuto](http://vraptor.caelum.com.br/documentacao/vraptor3-guia-de-1-minuto/)! Você está pronto para **tirar suas dúvidas** [no fórum de discussão do GUJ](http://guj.com.br/forums/show/23.java) para frameworks brasileiros!

Agradecemos a todos os desenvolvedores do projeto, e em especial aos usuários, que contribuiram no fórum de maneira surpreendente: são mais de 1000 mensagens sobre o novo VRaptor desde sua versão beta 1!
