---
title: "Recursos dinâmicos do Objective-C"
date: "2012-03-13"
author: "dchohfi"
authorEmail: "dchohfi@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

É bem simples invocar um método a partir de uma `NSString`, pelo [performSelector](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Protocols/NSObject_Protocol/Reference/NSObject.html#//apple_ref/occ/intfm/NSObject/performSelector:):

\[sourcecode language="objc"\] Contato \*contato = \[\[Contato alloc\] init\]; \[contato performSelector:NSSelectorFromString(@"nomeDoMetodo")\]; \[/sourcecode\]

Quando não utilizamos o [ARC](http://clang.llvm.org/docs/AutomaticReferenceCounting.html), podemos ir bem mais além. Para isso, vamos conhecer um pouco mais do [metamodelo de classes](http://cocoawithlove.com/2010/01/what-is-meta-class-in-objective-c.html) do [runtime do Objective-C](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html).

Para entender melhor as funcionalidades dinâmicas da linguagem, precisamos entender como é feita a busca de métodos quando os chamamos. A busca dos métodos de instância de um objeto é feita na sua classe. A partir do método `+ (BOOL)instancesRespondToSelector:(SEL)aSelector;` podemos saber se a instância de um objeto responde ou não ao método passado.

Na definição de `[NSObject](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Protocols/NSObject_Protocol/Reference/NSObject.html)` encontramos apenas um atributo privado, a sua própria classe:

\[sourcecode language="objc"\] @interface NSObject <NSObject> { Class isa; } \[/sourcecode\]

Algo que lembra um pouco o `getClass` do java. Quando um método é invocado em um objeto (nos termos mais puros, quando uma mensagem é enviada a um objeto), o Objective-C precisa buscar a implementação. Para isso, ele será procurado nesse atributo privado `isa`, que é o objeto que representa a classe que foi usada para gerar essa instância. Caso não encontre, essa busca é delegada a classe mãe, através do método `- (Class)superclass` da `NSObject`.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/03/Screen-shot-2012-03-12-at-4.49.22-PM-292x300.png "Screen shot 2012-03-12 at 4.49.22 PM")](http://www.sealiesoftware.com/blog/archive/2009/04/14/objc_explain_Classes_and_metaclasses.html)

Outro fato importante é que a checagem pela existência de um método invocado em um objeto não é feita em tempo de compilação (lembrando: no caso de não estarmos usando o ARC). Essa é a característica dinâmica da linguagem, isso permite que métodos que não existiam em tempo de compilação sejam adicionados durante a execução. Por isso, a linguagem não faz a checagem em tempo de compilação para garantir que determinado método exista. Há algumas funcionalidades que permitem verificar se um objeto realmente responde para determinados métodos:

\[sourcecode language="objc"\] - (BOOL)isKindOfClass:(Class)aClass; - (BOOL)isMemberOfClass:(Class)aClass; - (BOOL)conformsToProtocol:(Protocol \*)aProtocol; - (BOOL)respondsToSelector:(SEL)aSelector; \[/sourcecode\]

E se um método que foi chamado em um objeto realmente não existir? A linguagem possui diversos callbacks para nos ajudar a tratar esse método não declarado. Podendo dar característica ao nosso objeto que até o momento ele não possui.

Poderíamos adicionar um método em nossa classe de forma dinâmica. Para isso, a linguagem disponibiliza diversas funcionalidades dinâmicas que são executadas apenas durante a execução do código. No header `runtime.h` temos:

\[sourcecode language="objc"\] class\_addMethod(Class cls, SEL name, IMP imp, const char \*types); \[/sourcecode\]

Primeiramente, um método é chamado na classe para validar se o método realmente foi declarado:

\[sourcecode language="objc"\] + (BOOL)resolveInstanceMethod:(SEL)sel; \[/sourcecode\]

Ao responder `YES`, dizemos que as instâncias daquela classe realmente implementam o método passado. Mas, se o método não for encontrado, um erro será lançado. Porém, ao responder `NO`, o método não será chamado e continuará a execução chamando alguns outros callbacks no objeto, para dar a chance de tratar aquela chamada específica.

Temos a capacidade de delegar a execução de um objeto para outro; um atributo, por exemplo. Chamamos isso de message forwarding. Podemos fazer reescrevendo o método `- (id)forwardingTargetForSelector:(SEL)aSelector`.

Se retornarmos qualquer coisa diferente de `NIL`, o seletor passado como argumento será executado no objeto retornado. Com isso, não precisamos fazer nosso objeto ser filho de `NSString` para ganhar os comportamentos dele, poderíamos apenas delegar as chamadas dos métodos definidos em `NSString` para um atributo. [Evitando o abuso de herança e valorizando a composição, porém de uma forma transparente](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/).

Para realmente receber a chamada do método que não existe e tratá-lo de uma forma mais elegante, precisamos sobrescrever dois métodos definidos em `NSObject`:

\[sourcecode language="objc"\] - (NSMethodSignature \*)methodSignatureForSelector:(SEL)aSelector; \[/sourcecode\]

Um `NSMethodSignature` é uma forma de auxiliar o runtime para buscar a assinatura do método. É feito, então, um enconde do método com os argumentos e o retorno. Para mais informações: [Veja a documentação da Apple](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html)

Após responder um `NSMethodSignature`, um outro método será chamado recebendo como parametro um objeto que contém todas as informações do método que aparentemente não existe.

\[sourcecode language="objc"\] - (void)forwardInvocation:(NSInvocation \*)anInvocation; \[/sourcecode\]

Essa é a última chance que temos para fazer algo com as informações que foram passadas. No objeto `NSInvocation` podemos encontrar os parâmetros enviados, o nome do método, entre outras informações úteis para criar um comportamento dinâmico em tempo de execução. Se nada for feito, o método `- (void)doesNotRecognizeSelector:(SEL)aSelector` será invcado e uma exceção será lançada.

Podemos então comparar o método `forwardInvocation:` ao `method_missing` do Ruby, dando características e dinâmicas ao objeto em tempo de execução.
