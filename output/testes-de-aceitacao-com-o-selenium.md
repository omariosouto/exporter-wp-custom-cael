---
title: "Testes de aceitação com o Selenium"
date: "2007-03-01"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[Selenium](http://www.openqa.org/selenium/) é uma ferramenta para testar aplicações web pelo browser de forma automatizada. Selenium se refere ao [Acceptance Testing]( http://en.wikipedia.org/wiki/Acceptance_testing) (ou functional testing) que involve rodar testes num sistema finalizado. Os testes rodam diretamente num browser, exatamente como o usuário faria.

**Por que Selenium?** Temos que testar o nosso código, mas em uma aplicação web fica dificil de testar a camada de apresentação, o resultado final. Como testar os custom tags (por exemplo erro.tag ou select.tag)? Como testar a compatibilidade entre browser diferentes de forma automatizada? Em geral, como testar que a página renderizada tem o resultado desejado?

Para isso serve Selenium, que se preocupa basicamente com duas tarefas

- testes de funcionalidades da aplicação web
- testes de compatibilidade entre browser e plataformas diferentes

**Componentes do Selenium:** Dois componentes são importante para gerar e rodar testes com Selenium:

1\. [Selenium RC](http://www.openqa.org/selenium-rc/)

É um servidor escrito em java. Ele recebe chamadas http e executa os testes. As chamadas vem dos testes unitários (com junit, por exemplo). Este blog tem foco nesse componente.

2\. [Selenium IDE]( http://www.openqa.org/selenium-ide/)

É uma extensão do firefox. Com ela podemos criar testes. Ela funciona com um recorder e grava as ações do usuário. As ações podem ser transformadas em código em várias linguagens entre elas java. **Integração com JUnit**

Vamos criar um teste de funcionalidade com Selenium e JUnit. Precisamos:

\- uma aplicação web - o selenium server - o selenium test client com junit

Objetivo é testar um combo box numa página jsp. Vamos deixar o selenium abrir um browser, chamar a página e testar a combo box.

**A página do aplicação web**

Aqui tem uma página ( index.jsp) simples para testar a opção selecionada num combo box: \[html\] <html>

<head> <title>Selenium e JUnit HowTo</title> </head>

<body> Size: <select name=”size”> <option id=”x-large”>X-Large</option> <option id=”large”>Large</option> <option id=”medium” selected>Medium</option> <option id=”small”>Small</option> <option id=”tiny”>Tiny</option> </select> </body>

</html> \[/html\]

Vamos supor que esta página é acessível pela url: http://localhost:8080/test/index.jsp

**O Selenium Server**

O servidor Selenium tem obviamente estar rodando antes de executar os testes de funcionalidade. Para iniciar o server precisamos o selenium-server.jar que está dentro do [Selenium-RC download]( http://www.openqa.org/selenium-rc/download.action) . Executamos na linha de comando: `$java -jar selenium-server.jar`

Existe um modo interativo do server para passar testes diretamente na linha de comando. Basta adicionar a opção -interactiv.

`$java -jar selenium-server.jar -interactiv`

Mais sobre o modo interativo [aqui](http://seleniumhq.org/projects/remote-control/ ).

**O selenium client**

Com a aplicação web e o servidor selenium rodando podemos escrever o teste com junit. Os métodos setUp e tearDown da classe de teste vão abrir e fechar a conexão com o servidor selenium. A classe DefaultSelenium é utilizado para a conexão. Aqui o selenium-java-client-driver.jar que vem com o Selenium RC é necessário:

\[java\] new DefaultSelenium("localhost", 4444, "\*firefox /usr/lib/firefox/firefox-bin", "http://localhost:8080"); \[/java\]

A porta 4444 é a padrão do Selenium, o terceiro parâmetro é o perfil do browser junto com o caminho executável (tem que ser binario mesmo), por último a url do servidor web. Existem perfis disponíveis para os browser mais usados do mercado.

Aqui temos o começo código do nosso teste unitário:

\[java\] private static DefaultSelenium selenium; @BeforeClass public static void setup() { String url = "http://localhost:8080"; selenium = new DefaultSelenium("localhost", 4444, "\*firefox /usr/lib/firefox/firefox-bin", url); selenium.start(); }

@AfterClass public static void tearDown() { selenium.stop(); } } \[/java\]

Falta agora o método de teste que abre a página index.jsp e verifica a opção selecionada no combobox:

\[java\] @Test public void testSelectedIdOfSizeComboBox() { selenium.open("/test/index.jsp"); assertEquals("medium", selenium.getSelectedId("size")); } \[/java\]

Ao rodar o teste o Selenium abrirá o firefox e chamará a página. Existem muitos métodos para que o Selenium preencha campos, submeta formulários, navegue entre as páginas e muito mais. Você pode utilizar o [Selenium IDE]( http://www.openqa.org/selenium-ide/) e realizar as operações que deseja testar, e o Selenium IDE vai gerar um código com tudo o que você fez durante aquela sessão no browser, depois basta você adicionar as _assertions_ desejadas, gastando pouco esforço para codificar a simulação de cliques e preenchimento de formulários.

**Problemas com Firefox 2.0**

Usando Firefox 2.0.1 eu tive problemas na versão atual do Selenium. Lendo o [forum](http://forums.openqa.org/index.jspa ) li que isso já foi resolvido no snapshot do selenium (que não está disponível atualmente) e será resolvido na próxima versão.

**Versões usadas**

- selenium-remote-control-0.9.1-SNAPSHOT
- selenium client 0.9.0
- junit 4.1
