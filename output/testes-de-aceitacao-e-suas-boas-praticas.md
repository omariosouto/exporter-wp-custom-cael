---
title: "Testes de Aceitação e suas boas práticas"
date: "2015-01-20"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Testar aplicações web não é lá tão simples. A maioria de nós apela para testes de unidade. Afinal, eles são fáceis e rápidos de serem escritos, e quando o sistema está bem modelado, eles te dão grande segurança no momento de alterar qualquer regra de negócio. Mas na prática, sabemos que algumas partes do sistema merecem um teste mais real, mais próximo do que o usuário faz. Precisamos garantir que toda a requisição e resposta, passando por todas as integrações do sistema, como banco de dados, serviços web e etc, funcionam.

Para escrever esses testes, muitas vezes chamados de _testes de sistema_ ou _testes funcionais_, fazemos uso de Selenium. A API do Selenium evoluiu bastante ao longo do tempo, e o framework é hoje muito maduro. Com poucas linhas de código, você consegue abrir um browser e fazê-lo navegar na sua página.

Um teste de sistema é parecido com um teste de unidade. Você deve montar o cenário que quer testar, executar a ação, e conferir se ela comportou-se do jeito esperado. Veja, por exemplo, o teste abaixo, cuja ideia é verificar que os cursos que o aluno comprou realmente aparecem no dashboard do [Alura](http://www.alura.com.br):

\[code language="java"\] @Test public void deveVerCursosDoAluno() { WebDriver driver = new FirefoxDriver(); driver.get("http://www.alura.com.br/dashboard");

List<WebElement> cursos = driver.findElements(By.cssClass("cursos"));

Assert.assertEquals(2, cursos.size()); Assert.assertTrue(cursos.get(0).getText().contains("TDD")); Assert.assertEquals(cursos.get(1).getText().contains("Java")); }

\[/code\]

Mas, para que esse teste funcione, precisamos fazer muitas coisas antes: o usuário precisa estar logado, o usuário precisa ter somente os cursos de TDD e Java liberados.

Estar logado é um problema. Precisamos fazer isso antes de qualquer operação, afinal todas elas precisam do login antes. Ter o curso de TDD e Java liberado também é um problema, porque no teste de sistema, os dados ficam salvos no banco, então precisamos de uma maneira de limpar os dados. Veja só como ficaria nosso teste, em pseudo-código:

\[code language="java"\] @Test public void deveVerCursosDoAluno() { WebDriver driver = new FirefoxDriver();

// limpa o banco inteiro // insere o usuario "mauricio" no banco

driver.get("http://www.alura.com.br/login"); driver.findElement(By.name("login")).sendKeys("Mauricio"); driver.findElement(By.name("senha")).sendKeys("Aniche"); driver.findElement(By.id("loginBtn")).submit(); // espera o login funcionar

driver.get("http://www.alura.com.br/dashboard");

// insere matricula de TDD e Java para o aluno

List<WebElement> cursos = driver.findElements(By.cssClass("cursos"));

Assert.assertEquals(2, cursos.size()); Assert.assertTrue(cursos.get(0).getText().contains("TDD")); Assert.assertEquals(cursos.get(1).getText().contains("Java")); }

\[/code\]

Veja que deixamos um monte de comentário no código, pois gastaríamos ainda mais linhas de código para fazer tudo aquilo. Conseguiu perceber como testes de aceitação podem ficar complicados? **Pensar em como escrever esse tipo de teste com qualidade e facilidade de manutenção é então fundamental.**

Nossa experiência acabou nos mostrando algumas boas práticas. Entre elas:

- **_Tenha uma API para criação de cenários._** Montar cenários é a parte mais chata de todo o teste. Tenha alguma API, disponível via serviço web, por exemplo, na qual seus testes conseguem criar e manipular esses dados de maneira fácil, sem precisar navegar pela interface para criar tudo (às vezes seu cenário é muito complexo, e ir tela a tela, criando tudo é demorado).
- **_Tenha seu banco de dados sempre em um estado inicial._** Dependência entre testes é complicado. Ter dados sujos no banco antes da execução do teste pode fazer com que seu teste falhe mesmo que a funcionalidade esteja funcionando. Para isso, antes de cada teste, resete seu banco para um estado inicial. Isso pode ser feito pela API de cenários, por exemplo.
- **_Encontre elementos no HTML por meio de algum identificador estável._** Elementos numa página HTML costumam mudar com frequência. Sempre que você for capturar algum elemento, opte pelo que seja mais estável (ou seja, tenda a mudar menos). IDs e Names em particular costumam mudar pouco. Já classes CSS e XPath mudam com mais frequência.
- **_Use PageObjects_.** Faça uso de [PageObjects](https://code.google.com/p/selenium/wiki/PageObjects) em todas as páginas do seu sistema. Encapsular o funcionamento de cada página em uma classe facilita sua manutenção. Afinal, você mudará em apenas um lugar se a funcionalidade mudar.
- _**Conheça só os detalhes das páginas que você realmente precisa.**_ Às vezes, para se testar uma funcionalidade, precisamos navegar por várias outras. Imagine que para testar B, você precisa passar antes por A. Se você estiver escrevendo o teste A, seja detalhista. Vá etapa por etapa. Agora, se estiver escrevendo o teste de B, passe por A o mais rápido que puder, e faça seu código depender da menor quantidade de detalhes possível. Assim, se A mudar, você precisará corrigir apenas os testes de A, e nem vai mexer nos testes de B.
- _**Nunca use o conteúdo completo do HTML da página.**_ Nossos asserts são geralmente feitos em cima do conteúdo de elementos HTML que apareceram na página. Quando for fazer o assert, o faça no elemento específico que a mensagem apareceu. Evite pegar o HTML inteiro e verificar se o conteúdo existe lá. A chance do seu teste passar, mesmo que a funcionalidade não funcione, é maior.

Nós escrevemos sobre todos esses padrões de maneira muito mais profunda e detalhada. Você pode a versão estendida desse [conjunto de boas práticas](https://docs.google.com/document/d/1agC_bLN2wEEIj3uLqzEIcwQibhX7dA49gxKS0Bj6E7Y/edit), que nós escrevemos em formato de padrão, e apresentamos no [PLoP 2014](http://www.hillside.net/plop/2014/), a maior conferência mundial sobre padrões na área de software. Discutimos muito sobre qualidade de código e boas práticas na escrita de testes automatizados isso na nossa [formação de testes no Alura](http://www.alura.com.br/cursos-online-testes).

E você? O que faz para garantir que seus códigos de teste de sistema fiquem fáceis de serem mantidos?
