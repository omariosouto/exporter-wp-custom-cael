---
title: "Testes são mais do que regressão: os benefícios no design"
date: "2015-03-24"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma das maiores, e mais conhecidas, vantagens da bateria de testes automatizados é justamente o seu rápido _feedback_ em relação a possíveis quebras no funcionamento original de uma parte de seu sistema. Garantir que uma nova alteração no código não quebrou o que já funcionava antes é também conhecido por testes de regressão.

Entretanto, as vantagens da prática de testes não param por aí. Um [assunto abordado bastante em nossos cursos de agile](http://www.caelum.com.br/cursos/agile/) é justamente o outro lado da moeda, tão importante quanto, que os testes possuem: dizer algo sobre o design de classes que você está desenvolvendo/testando naquele momento.

Testar um programa, manual ou automaticamente, é sempre parecido: fornecemos entradas diferentes para nosso software, e verificamos como ele se comporta com ela:

\[caption id="attachment\_3817" align="aligncenter" width="250"\][![Código](https://blog.caelum.com.br/wp-content/uploads/2010/12/qualotamanhodeumteste.png "Codigo")](https://blog.caelum.com.br/wp-content/uploads/2010/12/qualotamanhodeumteste.png) Para executar o código, precisamos de uma entrada.\[/caption\]

O resultado da execução do trecho de código é o conjunto de **efeitos colaterais** e o **retorno** da chamada efetuada. As asserções que fazemos em um código de teste verificam que esse conjunto de **efeitos** e **retorno** são exatamente o esperado. Em código, é algo como instanciar uma classe, invocar um método e verificar sua saída:

\[code\] @Test public void umTesteQualquer() { NotaFiscal nf = new NotaFiscal("Mauricio", 2500.0); double imposto = nf.calculaImposto(); Assert.assertEquals(250.0, imposto); } \[/code\]

Portanto, o teste automatizado é basicamente um trecho de código que exercita algum trecho de código de produção. **E, se a criação desse teste for um processo difícil e trabalhoso, isso é um indicador claro que a utilização desse código é também difícil e trabalhosa: há um problema na maneira que o código vai ser utilizado.**

\[caption id="attachment\_3814" align="aligncenter" width="373"\][![O que testar](https://blog.caelum.com.br/wp-content/uploads/2010/12/qualotamanhodeumteste-1.png "O que testar")](https://blog.caelum.com.br/wp-content/uploads/2010/12/qualotamanhodeumteste-1.png) Ao executar um teste, podemos verificar o retorno e os efeitos colaterais - dependendo da linguagem de programação utilizada.\[/caption\]

Imagine um trecho que possui 100 linhas de código, incluindo diversos condicionais dependente de variáveis no escopo, e que precisa ser testado. Assim como no momento de utilização desse código, a preparação envolve atribuir valor a todas as variáveis que influenciam no fluxo de condicionais e loops: um processo geralmente penoso para um código de 100 linhas.

O seu teste, aquele trecho de código que faz uso do método de produção, indica que o código existente está executando muita coisa - e é óbvio, são 100 linhas, ele assumiu muitas responsabilidades com o passar do tempo (e [agora é a hora de quebrar tal comportamento em diversas partes](https://blog.caelum.com.br/2010/06/28/compondo-seu-comportamento-heranca-chain-of-responsibility-e-interceptors/)). O feedback que o teste dá - criado antes ou depois do código funcional, e mostrando a complexidade - é um reforço para perceber momentos de refatoração.

Se o código testado é uma unidade pequena, e for difícil testá-lo, o feedback indica que ele há algo nocivo na comunicação dessa unidade com as quais ela se integra, conforme o diagrama a seguir:

\[caption id="attachment\_3815" align="aligncenter" width="503"\][![Usando mocks](https://blog.caelum.com.br/wp-content/uploads/2010/12/qualotamanhodeumteste-2.png "Usando mocks")](https://blog.caelum.com.br/wp-content/uploads/2010/12/qualotamanhodeumteste-2.png) Para testar isoladamente uma parte do sistema, simulamos o comportamento da parte que integra com ela. As integrações são tanto no retorno quanto nos efeitos colaterais de uma unidade de código.\[/caption\]

Se o código testado envolve diversas unidades (na verdade, o que chamamos de um teste de integração), o feedback é sobre a comunicação somente entre duas pontas, **perdendo qualquer benefício em relação ao design de suas unidades**:

Se você analisar seu teste com cuidado, você verá que um determinado tipo de problema no código de teste pode implicar em um determinado problema na classe de produção. Por exemplo, se sua bateria de testes de uma única classe tem 50 métodos de testes, isso nos diz que a classe de produção tem 50 comportamentos diferentes, o que pode nos indicar problemas de coesão. Se a classe de teste faz uso de muitos _mock objects_ para isolar, isso pode nos indicar que a classe de produção tem problemas de acoplamento. E sobre [a vontade que temos de vez em quando de testar métodos privados](http://www.aniche.com.br/2010/11/voce-quer-testar-metodos-privados-certeza/)? Podemos pensar em muitas coisas que "podem dar errado" em nosso teste.

Esses feedbacks podem nos ser extremamente úteis. No [livro de TDD da Casa do Código](http://www.casadocodigo.com.br/products/livro-tdd), o Maurício Aniche discute e elenca uma série desses padrões. Nosso [curso de Práticas Ágeis de Desenvolvimento de Software](https://www.caelum.com.br/curso-praticas-ageis/) também.

E você, já usa seus testes de unidade para aprender e melhorar seu design de classes?
