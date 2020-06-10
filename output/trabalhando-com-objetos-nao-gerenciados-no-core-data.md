---
title: "Trabalhando com objetos não gerenciados no Core Data"
date: "2013-07-16"
author: "erich.egert"
authorEmail: "erich.egert@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

No mercado mobile é comum a integração com um servidor web utilizando webservices. Normalmente a forma considerada mais simples e eficaz para enviar e receber dados é o formato [JSON](http://www.json.org "JSON"). No iOS existem ferramentas que facilitam a conversão desse JSON em objetos do Objective-C, inclusive esse assunto já foi abordado aqui no Blog da Caelum: [Trabalhando com JSON no iOS](https://blog.caelum.com.br/trabalhando-com-json-no-ios/).

Considere o seguinte JSON:

\[sourcecode language="javascript"\] \[ { "materia": "Matematica", "data": "19/06/2013" }, { "materia": "Portugues", "data": "19/06/2013" }, { "materia": "Quimica", "data": "20/06/2013" } \] \[/sourcecode\]

Podemos facilimente converter os dados em uma NSArray de objetos do tipo:

\[sourcecode language="objc"\] @interface Prova : NSObject @property (nonatomic, retain) NSString \* data; @property (nonatomic, retain) NSString \* materia; @end

@implementation Prova @end \[/sourcecode\]

Utilizando a biblioteca AFCNetworking para realizar o request teríamos um código parecido com:

\[sourcecode language="objc"\] AFJSONRequestOperation \*operation = \[AFJSONRequestOperation JSONRequestOperationWithRequest:request success:^(NSURLRequest \*req, NSHTTPURLResponse \*res, id JSON) { NSDictionary \*jsonDict = (NSDictionary \*) JSON; NSMutableArray \*provas = \[\[NSMutableArray alloc\] init\]; for(NSDictionary \*provaDict in jsonDict) { Prova \*prova = \[Prova new\]; prova.materia = \[provaDict objectForKey:@"materia"\]; prova.data = \[provaDict objectForKey:@"data"\]; \[provas addObject:prova\]; } } failure:^(NSURLRequest \*req, NSHTTPURLResponse \*res, NSError \*e, id JSON) {} \]; \[/sourcecode\]

Com um NSArray em mãos fica fácil apresentar os dados para o usuário em um UITableView. Esse procedimento é exatamente o que ocorre no aplicativo BusaoSP da Caelum, que faz a requisição para um servidor web em busca dos pontos próximos dos dispositivo do usuário.

Um dos novos features da nova versão do [Busao SP](https://itunes.apple.com/br/app/busaosp/id518094424?mt=8) apresenta um pequeno desafio que é o foco desse post: O usuário deseja favoritar um Ônibus para que possa selecioná-lo facilmente no futuro. Quando falamos em persistir dados no iOS sempre podemos optar pelo Core Data.

Podemos configurar nossa aplicação para utilizar Core Data, criando um Data Model para que possamos modelar nossas entidades. A partir do modelo criado no Data Model podemos gerar nossa nova classe prova, que agora herda de NSManagedObject:

\[sourcecode language="objc"\] @interface Prova : NSManagedObject @property (nonatomic, retain) NSString \* data; @property (nonatomic, retain) NSString \* materia; @end

@implementation Prova @dynamic data; @dynamic materia; @end \[/sourcecode\]

Se rodarmos agora o mesmo código que recebe o request e cria uma NSArray de provas vamos nos deparar com a seguinte mensagem de erro: `CoreData: error: Failed to call designated initializer on NSManagedObject class 'Prova'`

Para podermos instanciar um NSManagedObject precisamos de um NSManagedObjectContext pois esse contexto contém informações das configurações que fizemos no Data Model, necessárias para que o Core Data consiga criar o objeto e gerenciá-lo com sucesso (não podemos esquecer que o Core Data nos dá possibilidade de fazer "undo" em operações, criar relacionamentos etc...).

A partir de agora para criarmos uma instância de Prova podemos fazer:

\[sourcecode language="objc"\] NSManagedObjectContext context = //pega do AppDelegate Prova \*prova = (Prova\*)\[NSEntityDescription insertNewObjectForEntityForName: @"Prova" inManagedObjectContext: context\] \[/sourcecode\]

O problema dessa abordagem é que se precisarmos salvar alguma informação precisaremos chamar o método "save" do NSManagedObjectContext e por consequência todos objetos gerenciados no contexto serão salvos. No nosso caso isso pode acontecer com todas as provas que criamos a partir do JSON.

Uma das maneiras de sair desse problema é criar 2 classes idênticas com os mesmos atributos uma herdando do NSObject (para ser usada na conversão do JSON em objetos) e outra herdando de NSManagedObject (para salvar apenas os favoritos do JSON). Essa abordagem pode causar complicações especialmente se as classes possuírem regras de negócio além das propriedades.

A outra abordagem é criar um NSManagedObject sem que ele fique no contexto de persistência desde o momento da criação (ou seja "detached" do contexto).

A maneira de fazer isso é criar uma instância de NSEntityDescription para que o Core Data acesse os dados do Data Model, especificando a entidade "Prova" e então criando a entidade a partir do seletor `initWithEntity: insertIntoManagedObjectContext:` Como não queremos o objeto gerenciado podemos passar "nil" no último argumento:

\[sourcecode language="objc"\] NSManagedObjectContext context = //pega do AppDelegate NSEntityDescription \*entity = \[NSEntityDescription entityForName: @"Prova" inManagedObjectContext:context\]; Prova \*prova = (Prova\*)\[\[NSManagedObject alloc\] initWithEntity:entity insertIntoManagedObjectContext:nil\]; \[/sourcecode\]

O resultado é que temos um objeto configurado para o Core Data mais ainda fora do contexto de persistência. Podemos utilizar essa técnica para instanciar os objetos com os dados do JSON. Caso seja necessário favoritar u m dos elementos podemos colocar o objeto no contexto e depois salvar as alterações do contexto:

\[sourcecode language="objc"\] \[context insertObject: umObjetoProvaNaoGerenciado\]; //agora podemos salvar o contexto \[/sourcecode\]

Por último tempos apenas o problema de isolar esse código. No caso de a mesma aplicação possuir várias entidades pode ser trabalhoso replicar o código que instancia o objeto não gerenciado. Esse é um caso clássico para o uso de Categorias do Objetive-C.

Podemos então criar uma categoria baseada no NSManagedObject para extrair o código anterior:

\[sourcecode language="objc"\] @implementation NSManagedObject (ComFacilitadores) +(NSManagedObject\*) detachedManagedObjectWithContext:(NSManagedObjectContext\*) context{ NSEntityDescription \*entity = \[NSEntityDescription entityForName: @"??????" inManagedObjectContext:context\]; return \[\[NSManagedObject alloc\] initWithEntity:entity insertIntoManagedObjectContext:nil\]; } \[/sourcecode\]

Mas como vamos saber o nome da classe que deve ser instanciada? Como estamos em um método de classe podemos utilizar o NSStringFromClass passando "self":

\[sourcecode language="objc"\] NSEntityDescription \*entity = \[NSEntityDescription entityForName: NSStringFromClass(self) inManagedObjectContext:context\]; \[/sourcecode\]

Agora se quisermos criar uma prova com um construtor que pede a data e a matéria:

\[sourcecode language="objc"\] #import "NSManagedObject+ComFacilitadores.h" @implementation Prova //... +(Prova\*) provaWithData: (NSString\*) data andMateria: (NSString\*) materia detachedFromContext: (NSManagedObjectContext\*) ctx {

Prova \*nova = (Prova\*) \[self detachedManagedObjectWithContext:ctx\]; nova.data = data; nova.materia = materia; return nova; } @end \[/sourcecode\]

Conheça o Busao SP na versões [iOS](https://itunes.apple.com/br/app/busaosp/id518094424?mt=8) e [Android](https://play.google.com/store/apps/details?id=br.com.caelum.ondeestaobusao.activity&hl=pt_BR)
