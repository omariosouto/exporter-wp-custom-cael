---
title: "TDD e sua influência no acoplamento e coesão"
date: "2011-02-17"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Escrever testes de unidade é uma prática cada vez mais adotada. Ela ajuda a verificar se tudo funciona como o esperado mesmo após mudanças, trazendo mais segurança para a equipe ao alterar o código. Mas os testes de unidade vão além, **possibilitando a validação de um design**.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/02/tdd-300x186.jpg "tdd")](https://blog.caelum.com.br/wp-content/uploads/2011/02/tdd.jpg)

Um código fácil de testar tende a apresentar um bom design. _[Existe uma grande sinergia entre testabilidade e um bom design](http://michaelfeathers.typepad.com/michael_feathers_blog/2007/09/the-deep-synerg.html)_. Isso acontece pois, para que o programador consiga testar uma classe de maneira isolada e facilmente, essa classe deve lidar muito bem com suas dependências (buscando sempre um baixo acoplamento) e possuir pouca responsabilidade (ou seja, ser altamente coesa). Caso contrário, o programador gastará muito tempo tentando testá-la, um possível indicador de que a classe apresenta um design pobre.

Um design que apresenta classes com alto acoplamento são difíceis de testar, surgindo a necessidade de passar as dependências para nossos objetos e só então testá-los.

O primeiro resultado ao adicionar testes de unidade a um sistema é a mudança de padrões imperativos de execução onde instanciamos objetos e invocamos métodos para a injeção de dependências.

O exemplo a seguir mostra um processador sendo instanciado, o que torna difícil verificar se o comportamento do `ExecutorDeBatch` foi correto sem executar o código da classe `Processador`:

\[java\] public class ExecutorDeBatch { public void le(ListaDeItens itens) { Processador processador = new Processador(); while(itens.temProximo()) { String item = sc.proximo(); processador.interpreta(item); } } } \[/java\]

O teste acima verifica o comportamento do `ExecutorDeBatch` junto com o `Processador`. Esse tipo de teste é considerado de integração, não passando muita informação sobre o acoplamento entre as classes, mas somente garantindo o comportamento em conjunto.

Para isolar a classe acima seria necessário a utilização de um [mock](https://blog.caelum.com.br/testes-unitarios-com-jmock-2/) no lugar do `Processador`. Da maneira que o código está, seria bastante trabalhoso. No Java exigiria até manipulação de bytecode durante o classload para que fosse possível alterar a classe instanciada no momento do `new Processador()` por exemplo. Para isso, o programador deve deixar essas dependências bem explícitas, recebendo-as através do construtor, por exemplo. Teríamos então o seguinte código:

\[java\] public class ExecutorDeBatch { private Processador processador; public ExecutorDeBatch(Processador processador) { this.processador = processador; }

public void le(ListaDeItens itens) { while(itens.temProximo()) { String item = sc.proximo(); processador.interpreta(item); } } } \[/java\]

Passar um mock para a classe acima e consequentemente testá-la de maneira isolada ficou é fácil. A adoção dos testes de unidade deixa explícito o excesso de dependências (ou dependências implícitas escondidas).

Por fim, os testes precisam ser simples e curtos, limitando seu escopo; um teste muito grande ou complicado [pode indicar que o método em questão possui muita responsabilidade](http://www.amazon.com/Growing-Object-Oriented-Software-Guided-Tests/dp/0321503627).

Considere uma classe que é responsável por gerar uma nota fiscal a partir de uma fatura e disparar a mesma para o e-mail do cliente, assim como para um sistema SAP qualquer. Essa classe já possui as dependências bem explícitas:

\[java\] public class GeradorDeNotaFiscal { private final Sap sap; private final EnviadorDeEmails enviador;

public GeradorDeNotaFiscal(Sap sap, EnviadorDeEmails enviador) { this.sap = sap; this.enviador = enviador; }

private NotaFiscal geraNotaFiscalAPartirDa(Fatura fatura) { // codigo de geracao da nota fiscal }

public void gera(Fatura fatura) { NotaFiscal nf = geraNotaFiscalAPartirDa(fatura);

sap.armazena(nf); enviador.envia(nf); } } \[/java\]

Passar mocks para essa classe é fácil, já que é possível injetar todas as dependências nela. O problema é que, para criar testes para ela, o programador se preocuparia em testar se a nota fiscal foi gerada, se ela é enviada ao SAP e se ela é enviada para o cliente:

\[java\] @Test public void deveGerarANf() { ... } @Test public void deveArmazenarNfNoSap() { ... } @Test public void deveEnviarNfParaOEmailDoCliente() { ... } \[/java\]

Perceba que essa classe tem responsabilidades demais: ela gera NF, envia para o SAP, envia para o cliente. Ao escrever esses testes, o programador perceberia que, em cada teste, ele estaria preocupado em testar uma responsabilidade da classe, sem se importar com a outra. No teste `deveArmazenarNfNoSap`, por exemplo, o programador seria obrigado a passar um stub da classe `Email`, mesmo não sendo o foco daquele teste. Isso torna a escrita do teste mais massante é um sinal de que a classe tem baixa coesão.

Portanto, se o seu design possue um alto acoplamento ou uma baixa coesão, será praticamente impossível escrever testes de unidade. Por esse mesmo motivo métodos estáticos, singletons, factories e estado global dificultam os testes de unidade.

Pensar e criar os testes antes do código também auxiliam no design. Usar [Test-Driven Development](http://www.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530) força que a concentração do pensamento lógico foque nas responsabilidades de cada método, ao invés da exata implementação do mesmo.

Esse impacto no design é tão grande que muitos [vão utilizar a sigla TDD para Test-Driven Design](http://www.agiledata.org/essays/tdd.html). Com TDD o programador é forçado a pensar em decisões de design antes do código de implementação, criando um maior desacoplamento, [deixando mais claro suas necessidades](http://athenea.ort.edu.uy/publicaciones/ingsoft/recursos/articulos/AimFire.pdf).

O TDD, através dos testes de unidade e refatoração, possibilitam que o [design apareça de uma maneira mais evolutiva](http://martinfowler.com/articles/designDead.html), mas sem mágicas, sempre dependendo de conhecimento do desenvolvedor em relação a design e orientação a objetos.
