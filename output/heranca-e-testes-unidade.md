---
title: "Herança e testes de unidade"
date: "2016-03-14"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Herança é um dos termos mais discutidos em orientação a objetos. Há uma discussão antiga sobre as [vantagens e desvantagens com relação à Composição](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/).  Em outro artigo, o Aniche trouxe o **[Príncipio da Substituição de Liskov.](https://blog.caelum.com.br/principios-do-codigo-solido-na-orientacao-a-objetos/)** Além disso, uma outra discussão famosa e [bem antiga](https://blog.caelum.com.br/ei-como-e-o-seu-dao-ele-e-tao-abstraido-quanto-o-meu/) é a [possibilidade de DAOs genéricos](https://blog.caelum.com.br/possibilidades-de-design-no-uso-do-seu-generic-dao/). Por fim, também cito o uso da [herança para lidar com chain of resposibility](https://blog.caelum.com.br/compondo-seu-comportamento-heranca-chain-of-responsibility-e-interceptors/) e o [excelente Joshua Bloch](https://blog.caelum.com.br/effective-java-segunda-edicao/).

Como podemos ver, a [discussão é longa](http://www.guj.com.br/t/mau-uso-da-heranca/220973/3) e o questionamento que eu trago é: como lidar com herança e [testes de unidade](https://blog.caelum.com.br/unidade-integracao-ou-sistema-qual-teste-fazer/)?

Bom, para entender o post, além de Herança, você deve estar familiarizado com a [importância de se fazer testes automatizados](http://blog.triadworks.com.br/na-minha-maquina-funciona-e-na-sua-testes-tdd-e-build-automatizado). Principalmente [os testes de unidade](http://www.aniche.com.br/2011/02/sera-que-eu-preciso-de-100-de-cobertura-de-testes/), que é basicamente o [foco do artigo](https://blog.caelum.com.br/perdendo-ou-ganhando-tempo-com-testes-de-unidade/). A ideia aqui é ver o quanto que o teste [pode te ajudar a descobrir problemas de design no seu código](https://blog.caelum.com.br/testes-sao-mais-do-que-regressao-os-beneficios-no-design/).

No cenário proposto, se você tivesse controle sobre este código, provavelmente iria preferir utilizar a composição para resolver o problema, devido aos motivos já relatados no primeiro parágrafo. Todavia, vamos partir da premissa que seja necessário o uso de uma API de um terceiro e a herança é obrigatória. Como lidar com os testes? Vamos ao código!

\[java\] //classe do Fabricante abstract class AbstractService { public Service getService() { //faz algo que vc não tem controle consultar um WS, REST, BD,etc. } }

class ServicoDeNotaFiscal extends AbstractService { private NotaFiscal notaFiscal = new NotaFiscal(); public void run(){

Service service = getService();

if(service.isOperationInitialized()){

notaFiscal.mudarParaEstado(Estado.CRIADA); }else{

notaFiscal.mudarParaEstado(Estado.SUSPENSA); } } }

class ServicoDeNotaFiscalTest{ private ServicoNotaFiscal servicoNotaFiscal;

@Before public void init(){ servicoNotaFiscal = new ServicoNotaFiscal(); }

@Test public void quandoOperacaoForIniciadaDeveColocarNotaFiscalComoCriada(){ servicoNotaFiscal.run(); assertEquals(Estado.CRIADA, servicoNotaFiscal.getNotaFiscal().getEstado()); } } \[/java\]

## Isolando com objetos de mentira...

Obviamente há outras cenários de testes, mas o objetivo aqui não é explicar [Coverage](http://eclemma.org/), vamos focar apenas na herança. Ao rodar os testes, você encontrará algum erro de comunicação relacionado à execução do método herdado _getService_. Não temos controle sobre ele, e como estamos fazendo [Testes de Unidade](http://tdd.caelum.com.br), temos que arrumar alguma forma de isolá-lo. Ademais, precisamos definir algum comportamento para o método _isOperationInitialized()_. Afinal, se o retorno for **true**, nota fiscal deve ir para criada, caso contrário, suspensa.

Uma primeira solução seria transformar nossa objeto ServicoNotaFiscal no que algumas pessoas [chamam de Stub.](http://martinfowler.com/articles/mocksArentStubs.html) [Nomenclaturas à parte](http://xunitpatterns.com/Mocks,%20Fakes,%20Stubs%20and%20Dummies.html), temos que definir um comportamento para o método _getService._

\[java\] class ServicoDeNotaFiscalTest{ private ServicoNotaFiscalStub servicoNotaFiscal;

@Before public void init(){ servicoNotaFiscal = new ServicoNotaFiscalStub(); }

}

class ServicoNotaFiscalStub extends ServicoNotaFiscal{ public Service getService(){ return new ServiceStub(); } } class ServiceStub extends Service{ public boolean isOperationInitialized(){ return true; } } \[/java\]

## Frameworks podem ajudar...

Ao rodar os testes, os stubs serão executados ao invés da implementação real. O teste passa, porém é uma solução mais prolixa. Você poderia usar o conceito de SPY Object ([já explicado anteriormente](https://blog.caelum.com.br/facilitando-seus-testes-de-unidade-no-java-um-pouco-de-mockito/)  --> "_[objeto real até que prove o contrário](http://docs.mockito.googlecode.com/hg/1.9.5/org/mockito/Spy.html)_") e um mock para Service.

 

> When you're doing testing like this, you're focusing on one element of the software at a time -hence the common term unit testing. The problem is that to make a single unit work, you often need other units

 

\[java\] class ServicoDeNotaFiscalTest{ @Spy private ServicoNotaFiscal servicoNotaFiscal; @Mock private Service service; @Before public void init(){

when(service.isOperationIniatilized()).thenReturn(true)); doReturn(service).when(servicoNotaFiscal).getService(); } } \[/java\]

## Só piorar um pouquinho...

Particularmente acho que o código fica mais limpo e fácil de entender. Contudo, você pode encontrar um cenário mais desafiador: se o método _getService_ for _final_.

 

\[java\] abstract class AbstractService{ public final Service getService(){} }\[/java\]

Agora a herança começa realmente a cobrar o seu [acoplamento](https://blog.caelum.com.br/orientacao-a-objetos-uma-outra-perspectiva-sobre-o-acoplamento/). Perceba que pelo método herdado ser final, não poderemos mais fazer os stubs. Além disso, o próprio spy do Mockito não funcionaria:

 

> Watch out for final methods. Mockito doesn't mock final methods so the bottom line is: when you spy on real objects + you try to stub a final method = trouble. Also you won't be able to verify those method as well.

 

A solução aqui é quebrar um pouco o [encapsulamento](https://blog.caelum.com.br/revisitando-a-orientacao-a-objetos-encapsulamento-no-java/) e extrair a chamada do _getService_ para um método com visibilidade _[default](https://www.caelum.com.br/apostila-csharp-orientacao-objetos/encapsulamento-e-modificadores-de-acesso/)._ Depois, usaremos o _Spy object_ para redefinir o comportamento deste novo método criado.

\[java\] class ServicoDeNotaFiscal extends AbstractService { public void run(){ Service service = buscarPorServico(); //mais código aqui }

Service buscarPorServico(){ return getService(); } }

class ServicoDeNotaFiscalTest{ @Spy private ServicoNotaFiscal servicoNotaFiscal; @Mock private Service service; @Before public void init(){ doReturn(service).when(servicoNotaFiscal).buscarPorServico(); } } \[/java\]

 

Concluindo, a atividade de automatizar os testes envolve uma série de desafios e herança é apenas um deles. No cenário citado, se ao invés de herdar de AbstractService ele recebesse como [Injenção de Dependêcia](https://blog.caelum.com.br/ioc-e-di-para-frameworks-mvc/) um Service, seria muito mais fácil testar, [inclusive mais fácil ver a sua consistência](https://blog.caelum.com.br/o-problema-de-objetos-nao-consistentes/). Por isso que ["há uma grande sinergia entre um bom design e testes automatizados"](http://blog.lowendahl.net/ndc2010/ndc2010-michael-feathers-the-deep-synergy-between-good-design-and-testability/) (_Michael Feathers_). Estes não vão fazer sua [aplicação ter um bom Design](http://www.aniche.com.br/2010/12/eh-tdd-e-nao-ddt/) da noite pro dia, entretanto a prática irá [apontar](http://www.aniche.com.br/2013/04/quantidade-de-asserts-no-teste) os [caminhos](https://blog.caelum.com.br/tdd-e-sua-influencia-no-acoplamento-e-coesao/) para melhorar.

 

E quer saber mais sobre Testes? [Livro do Aniche é leitura obrigatória](https://www.casadocodigo.com.br/products/livro-testes-de-software)!  Corre lá!!!!
