---
title: "Gerenciamento de memória e o ARC no Objective-C"
date: "2012-04-11"
author: "ricardo.valeriano"
authorEmail: "ricardo.valeriano@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em outubro de 2011 a Apple lançou o iOS 5. Os desenvolvedores comemoraram bastante a disponibilização de uma funcionalidade chamada [Automatic Reference Couting](http://clang.llvm.org/docs/AutomaticReferenceCounting.html) (ARC), que promete facilitar muito o desenvolvimento de aplicações iOS.

O desenvolvedor Objective-C precisa se  preocupar com o gerenciamento de memória em suas aplicações, basicamente isso significa alocar a memória que será usada pelos objetos, utilizá-la e depois liberar essa memória. Esquecer de liberar a memória, ou liberá-la enquanto ainda está em uso, são problemas que podem causar mau funcionamento da aplicação e inclusive o famoso "crash", fazendo-a fechar inesperadamente.

O mecanismo para gerenciamento de memória usado em Objective-C é conhecido como _reference counting_. Para alocar a memória manualmente, enviamos uma mensagem `alloc` (uma invocação de método) para uma classe. Uma receita comum para a criação de objetos é invocar o método `init` para o retorno de uma invocação ao `alloc`, como no exemplo para instanciar uma `NSArray`:

\[sourcecode language="objc"\] NSArray \*array = \[\[NSArray alloc\] init\] \[/sourcecode\]

O retorno dessa chamada a `init` é uma referência a um objeto pronto para uso, mas por quanto tempo esse objeto ficará em uso? Quando vamos permitir que ele seja varrido da memória?

Podemos invocar o `retain` para um objeto e isso soma 1 ao seu contador de referências. O método `release` é usado para subtrair 1 do contador, podendo possibilitar sua desalocação. Se esse balanço é comprometido (que é o que acontece quando esquecemos de invocar `release` para uma referência que não será mais usada) começam aparecer os tais vazamentos de memória. Esse processo manual de alocação e liberação de memória pode ser ilustrado como na [imagem abaixo](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html):

![](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Art/memory_management.jpg)

Aliás, é bastante didático [utilizar o Instruments](https://developer.apple.com/library/mac/#documentation/developertools/conceptual/InstrumentsUserGuide/ViewingandAnalyzingData/ViewingandAnalyzingData.html) para poder aprender e visualizar o reference count de cada objeto em memória.

A ideia do ARC é facilitar, tirando a nossa responsabilidade de invocar `retain` e `release`: essa tarefa passa a ser executada por debaixo dos panos, com a ajuda do compilador. Para desenvolver código que aproveita ao máximo essa funcionalidade é preciso pensar na forma como os objetos se relacionam entre si durante a execução de seu programa. Se optar pelo ARC, você não poderá mais invocar `retain` e `release` explicitamente, essas invocações serão incluídas automaticamente pelo compilador. Mas como o compilador decide quando usar esses métodos? Ele considera os chamados modificadores de ciclo de vida, na declaração das referências.

Quando declaramos uma propriedade podemos utilizar esses modificadores de ciclo de vida, é isso que define como um objeto é tratado em termos de gerenciamento de memória. O modificador de ciclo de vida padrão para uma proprieade é o `assign`. Além dele podemos usar `retain` e `copy`. Com o ARC surgiram o `strong` e o `weak`.

### assign

É o modificador padrão de ciclo de vida. A propriedade é apenas um ponteiro para o objeto atribuído a ela, não soma ou subtrai do contador de referências e portanto não interfere no gerenciamento da memória para o objeto referenciado. Se o objeto for liberado da memória em um outro ponto do código, a referência apontada pela propriedade se tornará inválida, pois passa a apontar para um endereço de memória que não existe mais. Uma invocação de método nessa propriedade irá causar um erro de execução.

\[sourcecode language="objc"\] @property NotaFiscal \*nota; \[/sourcecode\]

### retain

Atribuir um objeto para a propriedade declarada com esse modificador soma 1 ao seu contador de referências. Usamos `retain` para tornar explícito que essa propriedade, após atribuída, estará disponível enquanto o objeto que a declara estiver sendo utilizando:

\[sourcecode language="objc"\] @property(retain) Produto \*produto; \[/sourcecode\]

### copy

Usado normalmente quando a propriedade se refere a um objeto que pode ser mutável como strings, arrays e dictionaries. Todos esses tipos têm subclasses que podem gerar objetos mutáveis, porém quando passamos esses objetos como parâmetro a intenção normalmente é a de usar _valores_ no método que recebe o parâmetro, não queremos permitir nenhuma alteração no objeto original. Como uma cópia defensiva, para não expor os atributos do nosso objeto a modificações indesejadas. Se esse for o caso podemos usar o modificador `copy`, isso vai garantir que o objeto seja copiado no momento da atribuição, essa cópia inicia sua vida com seu contador de referência valendo 1.

\[sourcecode language="objc"\] @property(copy) NSString \*nomeCompleto; \[/sourcecode\]

Todos esses modificadores são tratados automaticamente pelo ARC. Isso significa que o próprio compilador vai se encarregar de procurar o melhor momento para adicionar envios de mensagem release para liberar a memória alocada por essas declarações, quando isso for necessário. O ARC também disponibiliza alguns modificadores novos, que vem de encontro com a ideia de livrar o desenvolvedor da preocupação com a alocação de memória:

### strong

Quando um objeto é atribuído o contador de referências é incrementado, e a responsabilidade por fazer um decremento no contador é do objeto que possui a propriedade. O funcionamento é análogo ao do modificador `retain`.

\[sourcecode language="objc"\] @property(strong) Pessoa \*cliente; \[/sourcecode\]

### weak

Atribuir um objeto não incrementa o contador, semelhante ao modificador `assign`. Porém se a memória alocada para o objeto for liberada, a propriedade automaticamente torna-se `nil`. Essa diferença com relação ao `assign` é crucial pois se for invocado um método nessa propriedade (agora com o valor `nil`) não haverá um erro de execução, como se ela fosse ignorada.

\[sourcecode language="objc"\] @property(weak) Endereço \*endereco; \[/sourcecode\]

A vantagem de usar `strong` e `weak` com o ARC é poder pensar no grafo de objetos e suas relações, sem se preocupar com o contador de referências. Uma preocupação que ainda temos é a de garantir que não haverá nenhuma referência cíclica impedindo algum objeto de ser liberado da memória. Imagine uma propriedade chamada cliente que contém uma referência com modificador strong para um objeto do tipo Pessoa. Esse objeto internamente faz uma referência para uma instância de Endereco, veja a declaração dessas classes:

\[sourcecode language="objc"\] @interface Pessoa @property(strong) Endereco \*endereco; @end \[/sourcecode\]

\[sourcecode language="objc"\] @interface Endereco @property(strong) Pessoa \*pessoa; // outras propriedades... @end \[/sourcecode\]

\[sourcecode language="objc"\] @implementation Endereco -(id) initComLogradouro:(NSString \*)\_logradouro eNumero:(NSString \*)\_numero paraPessoa:(Pessoa \*)\_pessoa { self = \[super init\]; if(self) { self.pessoa = \_pessoa; } // restante das atribuições... return self; } \[/sourcecode\]

Podemos pensar em um sistema de cadastro que crie objetos do tipo `Pessoa` e `Endereço`, e faça as associações entre eles:

\[sourcecode language="objc"\] @interface Cadastro @property(strong) Pessoa \*pessoa; - (void) cadastraPessoaComNome:(NSString \*)nome; @end \[/sourcecode\]

E essa poderia ser uma implementação para o método `cadastraPessoa`:

\[sourcecode language="objc"\] - (void) cadastraPessoa:(NSString \*)nome { self.pessoa = \[\[Pessoa alloc\] initComNome:nome\]; Endereco \*e = \[\[Endereco alloc\] initComLogradouro:@"Rua Vergueiro" eNumero:3185 paraPessoa:self.pessoa\]; } \[/sourcecode\]

Dentro do método `cadastraPessoa`: é criado um objeto do tipo `Pessoa`, quando ele é atribuído à propriedade `pessoa`, o contador de referência é incrementado. O compilador irá inserir o envio da mensagem release para a propriedade `pessoa` da classe `Cadastro`. Mas perceba que o contador de referências para esse objeto foi incrementado também durante a execução do inicializador `initComLogradouro:eNumero:paraPessoa:`. Nesse momento o contador de referências para o objeto pessoa criado no método `cadastraPessoa`: tem o total de 2.

Para o gerenciador de memória liberar o objeto do tipo `Cadastro` utilizado no exemplo, uma das coisas que precisa ser feito é a liberação do objeto apontado pela propriedade do tipo `Pessoa`. Para isso será subtraído 1 do contador de referências, agora o contador tem 1, e o objeto referenciado por `pessoa` não será liberado da memória. Faz sentido! Mas não é o comportamento esperado, já que o objeto criado pelo cadastro deveria ser liberado da memória junto com o cadastro que o criou. É um vazamento!

Para evitar isso, podemos usar o modificador de ciclo de vida `weak` na propriedade pessoa de endereço, dessa forma a propriedade será `nil` quando esse objeto for liberado a qualquer momento, e não há o risco de uma referência para o objeto do tipo `Pessoa` ficar pendente (`dangling`). Uma forma de pensar nesse tipo de relacionamento é: endereço pertence a uma pessoa, portanto ele aponta para a pessoa de forma **weak**, se a pessoa for liberada, endereço não tem que interferir nisso. Já a pessoa quer ter certeza de que o endereço estará sempre disponível para ela, portanto ela aponta para o endereço de forma **strong**.

Vamos nos apoiar no ARC para corrigir o problema, podemos declarar as classes `Pessoa` e `Endereço` da seguinte forma:

\[sourcecode language="objc"\] @interface Pessoa

@property(strong) Endereco \*endereco;

@end \[/sourcecode\]

\[sourcecode language="objc"\] @interface Endereco

@property(weak) Pessoa \*pessoa;

@end \[/sourcecode\]

Existem modificadores específicos para variáveis de instância. Além do `__strong` e do `__weak` que funcionam da mesma forma que os análogos `strong` e `weak` utilizados em propriedades, existem também:

### \_\_unsafe\_unretained

Semelhante ao `_weak`, porém a variável não recebe o valor nil quando a memória é liberada, o valor continua sendo um ponteiro que agora fica pendente. Equivalente ao `assign` para propriedades.

### \_\_autoreleasing

Libera a memória utilizada para armazenar um objeto passado como parâmetro para um método assim que ele retorna. Garante que dentro da execução do método o objeto vai estar disponível, mas só durante esse período.

Existem outros aspectos relacionados ao gerenciamento de memória com o ARC. Por exemplo: não é possível usar `retain` e `release` explícitamente com o ARC habilitado, isso irá gerar erros de compilação. Também não é possível usar objetos do tipo `NSAutoreleasePool`, para essa funcionalidade devemos utilizar um bloco @autoreleasepool. É possível desabilitar o ARC em um projeto para arquivos específicos, isso significa que você pode utilizar bibliotecas "pré-ARC", mesmo se sua aplicação quiser usar este novo contador automático de referêncais.

Vale lembrar que o código para atribuição e leitura dos dados de uma proprieade com seus devidos retains serão gerados automaticamente pelo compilador ao usar a diretiva `@synthesize`.

No curso [IP-67](http://www.caelum.com.br/curso/ip-67-desenvolvimento-movel-ios-5-iphone-ipad/) da Caelum esse assunto é debatido e relacionado com vários outros aspectos do desenvolvimento para aplicações iOS.
