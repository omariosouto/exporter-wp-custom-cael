---
title: "Organização de testes de aceitação com PageObjects"
date: "2012-04-09"
author: "leonardo.wolter"
authorEmail: "leonardo.wolter@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Testes de aceitação são extremamente úteis quando se trata de verificar se as funcionalidades de um sistema estão se comportando corretamente sem que tenhamos de testar manualmente a aplicação, abrindo um navegador, navegando por ela e visualizar os resultados. Como poderíamos automatizar esses testes que envolvem realizar a navegação na aplicação?

Para isso ser possível, existem algumas ferramentas que permitem tal trabalho, simulando a interação de um usuário com a aplicação. Esse é o caso do [Selenium](https://blog.caelum.com.br/testes-de-aceitacao-com-o-selenium/) que, com a assistência do [JUnit](http://junit.org/), pode facilitar o trabalho de escrever tais testes.

Para vermos como o Selenium pode nos ajudar, vamos considerar uma página contendo simples HTML, que ao clicarmos em um link dela, seremos redirecionados para outra página.

\[xml\]

<a class="botao" href="outra\_pagina.html">Clique Aqui</a>

\[/xml\]

Quando clicado, o link deve redirecionar para a `outra_pagina.html`, que tem o conteúdo:

\[xml\]</pre> <h2>Estou na outra página!</h2> <pre> \[/xml\]

Com essas páginas, podemos escrever um teste, usando o Selenium, para que ele clique no link e em seguida verifique se estamos na outra página. Para isso, podemos escrever um pequeno teste com o JUnit usando a API do Selenium:

\[java\] @Test public void deveIrParaAOutraPagina() { WebDriver driver = new FirefoxDriver(); driver.navigate().to("http://localhost:8080/pagina.html");

driver.findElement(By.className("botao")).click();

String texto = driver.findElement(By.tagName("h2")).getText(); assertEquals("Estou na outra página!", texto); } \[/java\]

Com a API do Selenium, esse código parece simples, não é? Mas imagine se precisássemos fazer várias ações e navegar por diversas páginas para conseguirmos testar uma funcionalidade. Um possível exemplo seria o login aplicação, onde uma possibilidade de teste é:

\[java\] @Test public void aoSeLogarNaAplicacaoDeveMostrarQueEstaLogado(){ WebDriver driver = new FirefoxDriver(); driver.navigate().to("http://localhost:8080/login.html");

// pega uma referencia para o formulário de login que estará na tela WebElement formulario = driver.findElement(By.tagName("form"));

// preenche usuário e senha driver.findElement(By.id("usuario")).sendKeys("joao"); driver.findElement(By.id("senha")).sendKeys("123456");

// submete o formulário formulario.submit();

// na próxima tela, o login desse usuário deveria estar aparecendo String usuarioLogado = driver.findElement(By.id("usuario-logado")).getText();

// verifica se o joao conseguiu fazer o login assertEquals("joao", usuarioLogado); } \[/java\]

Apesar de funcionar perfeitamente, o código acima possui um grave problema: lê-lo já está extremamente mais complexo do que o teste que havíamos feito antes! Agora, imagine se estivéssemos escrevendo testes para uma funcionalidade que envolva interação com várias outras telas?

Para simplificar e organizar esse código de teste, vamos separar cada uma das telas em classes diferentes, ou seja, para a tela de login vamos criar uma classe chamada `TelaDeLogin` e para a página interna uma classe chamada `PaginaInterna`, dessa forma poderíamos ter o método `loga` com seguinte código para a `TelaDeLogin`:

\[java\] public class TelaDeLogin {

public void abre() { driver.navigate().to("http://localhost:8080/login.html"); }

public void loga(String usuario, String senha) { // pega uma referencia para o formulário na tela WebElement formulario = driver.findElement(By.tagName("form"));

// preenche usuário e senha driver.findElement(By.id("usuario")).sendKeys(usuario); driver.findElement(By.id("senha")).sendKeys(senha);

// submete o formulário formulario.submit(); } } \[/java\]

Note que o código acima ainda não compila, pois precisamos de uma referência para o `WebDriver`. Podemos passar a recebê-la via construtor:

\[java\] public class TelaDeLogin { private WebDriver driver;

public TelaDeLogin(WebDriver driver) { this.driver = driver; }

public void abre() { // navega para a tela de login }

public void loga(String usuario, String senha) { // código que usa o Selenium para fazer o login } } \[/java\]

Dessa forma, podemos usar a nova classe `TelaDeLogin` no lugar do código do Selenium em nossas classes de teste. Com isso, o teste ficará parecido com:

\[java\] public class TestesDeAceitacao extends SeleniumTest {

private TelaDeLogin telaDeLogin;

// Instanciamos a base de nossa aplicacao @Before public void setup() { WebDriver driver = new FirefoxDriver(); this.telaDeLogin = new TelaDeLogin(driver); }

// Efetuamos o teste @Test public void aoSeLogarNaAplicacaoDeveMostrarQueEstaLogado(){ telaDeLogin.abre(); telaDeLogin.loga("joao","123456");

// na próxima tela, o login desse usuário deveria estar aparecendo String usuarioLogado = driver.findElement(By.id("usuario-logado")).getText();

// verifica se o joao conseguiu fazer o login assertEquals("joao", usuarioLogado); } } \[/java\]

O código do teste melhorou, mas ainda continua com problemas, um deles é que ainda usamos a API do Selenium diretamente dentro do teste e novamente, se ele fosse um pouco mais complexo, continuaria difícil de ler seu código. Como o trecho que ainda está usando o código do Selenium direto no teste diz respeito à página interna do sistema, podemos levar esse código para a classe `PaginaInterna`, dessa forma teremos:

\[java\] public class PaginaInterna { private WebDriver driver;

public PaginaInterna(WebDriver driver) { this.driver = driver; }

public boolean estaLogado(String usuario) { String usuarioLogado = driver.findElement(By.id("usuario-logado")).getText();

return usuarioLogado.equals(usuario); } } \[/java\]

Dessa maneira, podemos fazer uma simples refatoração no nosso teste, para usar a nova classe implementada:

\[java\] @Test public void aoSeLogarNaAplicacaoDeveMostrarQueEstaLogado() { String usuario = "joao"; telaDeLogin.abre(); telaDeLogin.loga(usuario, "123456");

boolean estaLogado = new PaginaInterna(driver).estaLogado(usuario); assertTrue(estaLogado); } \[/java\]

Agora estamos num nível melhor, mas continua tendo pontos para melhorar. O principal deles agora, é que toda troca de tela que existir em nosso teste, precisamos instanciar um objeto para a nova tela, passar o `driver` como parâmetro, para aí sim, podermos invocar o método. Note que ao fazermos o login com sucesso, nesse caso, sempre seremos redirecionados para a mesma página, sendo assim, poderíamos fazer o método `loga` já retornar uma instância de `PaginaInterna` para nós e assim, só precisaríamos chamar o método `verificaSeEstaLogado`:

\[java\] public class TelaDeLogin {

// construtor e atributo driver

public PaginaInterna loga(String usuario, String senha) { // pega uma referencia para o formulário na tela WebElement formulario = driver.findElement(By.tagName("form"));

// preenche usuário e senha driver.findElement(By.id("usuario")).sendKeys(usuario); driver.findElement(By.id("senha")).sendKeys(senha);

// submete o formulário formulario.submit();

return new PaginaInterna(driver); } } \[/java\]

Essa alteração na classe `TelaDeLogin` nos permite encadear as chamadas dos métodos `loga` e `verificaSeEstaLogado` no nosso teste de aceitação:

\[java\] @Test public void aoSeLogarNaAplicacaoDeveMostrarQueEstaLogado() { String usuario = "joao"; telaDeLogin.abre();

boolean estaLogado = telaDeLogin.loga(usuario, "123456").estaLogado(usuario); assertTrue(estaLogado); } \[/java\]

Agora nosso teste está bem mais legível, e o código do Selenium todo encapsulado dentro de classes, que chamamos de `[PageObject](http://code.google.com/p/selenium/wiki/PageObjects)`s. Ainda há brechas que precisam de melhoras. Podemos fazer o método `abre()` retornar a própria instância de `TelaDeLogin` e, com isso, poderíamos ter todos os métodos encadeados, o que faria o teste ficar como:

\[java\] @Test public void aoSeLogarNaAplicacaoDeveMostrarQueEstaLogado(){ String usuario = "joao";

boolean estaLogado = telaDeLogin.abre().loga(usuario, "123456").estaLogado(usuario); assertTrue(estaLogado); } \[/java\]

Com isso, temos um código de teste bem mais legível, onde toda a API do Selenium está encapsulado dentro de classes que representam as páginas, formando os Page Objects.

Que outras ferramentas e práticas você utiliza para facilitar seus testes de aceitação?
