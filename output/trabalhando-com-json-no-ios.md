---
title: "Trabalhando com JSON no iOS"
date: "2012-05-10"
author: "dchohfi"
authorEmail: "dchohfi@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Com o crescimento do mercado mobile, a integração de sistemas com os pequenos aparelhos se tornou essencial. O formato [JSON](http://www.json.org "JSON") se popularizou com a mesma velocidade que o desenvolvimento mobile. Sua simplicidade e facilidade de desenvolver parsers o tornou uma das principais alternativas na integração de sistemas web, em especial para ser consumido via AJAX.

No **iOS** não foi diferente. A necessidade dos desenvolvedores levou o surgimento de diversas bibliotecas que faziam o trabalho de ler e gerar JSON com extrema facilidade, como o [JSON Framework](https://github.com/stig/json-framework/ "JSON-Framework") e o [TouchJSON](https://github.com/TouchCode/TouchJSON "TouchJSON"). Porém a Apple não quis ficar de fora e incorporou a classe [`NSJSONSerialization`](http://developer.apple.com/library/ios/#documentation/Foundation/Reference/NSJSONSerialization_Class/Reference/Reference.html "NSJSONSerialization"), capaz de gerenciar o formato tanto no iOS como no desenvolvimento para OSX, com a mesma facilidade que as bibliotecas opensource trouxeram.

Recetemente [desenvolvemos um projeto para iOS](https://github.com/caelum/onibus-ios) para auxiliar os alunos do [nosso curso de iOS, o IP-67](http://www.caelum.com.br/curso/ip-67-desenvolvimento-movel-ios-5-iphone-ipad/ "IP-67"), enriquecendo a experiência e trazendo um exemplo bem palpável. A aplicação consiste em buscar os pontos de ônibus próximos a localização do usuário. Para integrar a nossa aplicação com um backend em Ruby utilizamos JSON como o formato e a classe `NSJSONSerialization` se mostrou extremamente simples e fácil de se trabalhar. Vamos entender como fizemos, do lado do dispositivo, a leitura e manipulação dos dados.

Primeiramente precisamos obter as informações no formato JSON para então parsea-los. Utilizando o método `+ (id)dataWithContentsOfURL:(NSURL *)url;` da classe [NSData](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSData_Class/Reference/Reference.html "NSData") para fazer uma request e obter as informações de forma síncrona em um endereço da WEB:

\[sourcecode language="objc"\] NSString \*url = \[NSString stringWithFormat:@"http://ondeestaoalbi.herokuapp.com/onibusesNosPontosProximos.json?lat=%f&long=%f", -23.588453, -46.632103\]; NSData \*jsonData = \[NSData dataWithContentsOfURL: \[NSURL URLWithString:url\]\]; \[/sourcecode\]

O nosso serviço retornará um JSON com a seguinte estrutura:

\[sourcecode language="javascript"\] { "nome": "920016054", "descricao": "R. Dr. Neto De Araujo, 311", "coordenada": { "latitude": -23.5876, "longitude": -46.6321 } } \[/sourcecode\]

[![](https://blog.caelum.com.br/wp-content/uploads/2012/05/json-ios.png "json-ios")](https://blog.caelum.com.br/wp-content/uploads/2012/05/json-ios.png)

Após receber as informações, vamos utilizar a classe `NSJSONSerialization` para transformar o JSON em um [NSDictionary](https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/nsdictionary_Class/Reference/Reference.html "NSDictionary"), contendo as chaves e valores das informações sobre o ponto.

\[sourcecode language="objc"\] NSError\* error; NSDictionary \*resultados = \[NSJSONSerialization JSONObjectWithData:jsonData options:NSJSONReadingMutableContainers error:&error\]; \[/sourcecode\]

Caso o JSON seja válido e a conversão seja feita com sucesso podemos buscar os valores no `NSDictionary` a partir de uma chave, utilizando o método `- (id)objectForKey:(id)aKey;`:

\[sourcecode language="objc"\] NSError \*error; NSDictionary \*resultados = \[NSJSONSerialization JSONObjectWithData:jsonData options:NSJSONReadingMutableContainers error:&error\]; if(!error) { NSString \*nome = \[resultados objectForKey:@"nome"\]; NSString \*descricao = \[resultados objectForKey:@"descricao"\]; NSLog(@"Nome: %@, descrição: %@", nome, descricao); } \[/sourcecode\]

Poderíamos continuar utilizando o `NSDictionary` para armazenar as informações dos pontos encontrados, mas imagine que por algum motivo precisemos mostrar a descrição do nosso Ponto sempre com letra minúscula e removendo os espaços do começo e do fim da nossa NSString, o código ficaria da seguinte forma:

\[sourcecode language="objc"\] NSString \*nome = \[resultados objectForKey:@"nome"\]; NSString \*descricao = \[\[resultados objectForKey:@"descricao"\] lowercaseString\]; descricao = \[descricao stringByTrimmingCharactersInSet:\[NSCharacterSet whitespaceAndNewlineCharacterSet\]\]; NSLog(@"Nome: %@, descrição: %@", nome, descricao); \[/sourcecode\]

Perceba que o código não é mais tão simples quanto anteriormente, além disso, precisaríamos espalhá-lo por todo o projeto. A manutenção também seria difícil, pois precisaremos _copiar e colar_ o mesmo código toda vez que a descrição for exibida e caso a regra de exibição seja alterada o código que copiamos e colamos terá que ser alterado.

Temos esse problema pois estamos utilizando a classe `NSDictionary` para guardar **estado** do nosso `Ponto` e não podemos definir nenhum comportamento adicional. Para manter os dados organizados e de fácil manutenção devemos sempre criar classes para representar tipos e não mantê-los na representação de chave e valor, desta forma, além de guardar o estado do objeto, podemos também definir novos comportamentos, facilitando a manutenção e a reutilização de código.

Vamos criar as classes com os referentes atributos e também definir um método de classe para criar uma instância do `Ponto`, esse método será responsável por buscar suas informações em um `NSDictionary` passado como argumento. Essa regra fica isolada e fácil de dar manutenção caso alguma das chaves mude:

\[sourcecode language="objc"\] #import <Foundation/Foundation.h> #import "Coordenada.h"

@interface Ponto : NSObject

@property(nonatomic, strong) NSString \*nome; @property(nonatomic, strong) NSString \*descricao; @property(nonatomic, strong) Coordenada \*coordenada;

\+ (Ponto \*) comDicionario: (NSDictionary \*) dicionario;

@end

#import "Ponto.h"

@implementation Ponto @synthesize nome, descricao, coordenada;

\+ (Ponto \*) comDicionario: (NSDictionary \*) dicionario { Ponto \* ponto = \[\[Ponto alloc\] init\]; \[ponto setNome: \[dicionario objectForKey:@"nome"\]\]; \[ponto setDescricao: \[dicionario objectForKey:@"descricao"\]\]; Coordenada \*coordenada = \[Coordenada comDicionario: \[dicionario objectForKey: @"coordenada"\]\]; \[ponto setCoordenada: coordenada\]; return ponto; }

@end \[/sourcecode\]

Se fosse preciso definir uma regra para buscar a descrição do `Ponto` da forma que descrevemos anteriormente, podemos alterar o método que busca o atributo na classe, isolando o código e facilitando futuras manutenções.

\[sourcecode language="objc"\] #import "Ponto.h"

@implementation Ponto @synthesize nome, descricao, coordenada;

\- (NSString \*) descricao { NSString \*descricaoEmMinusculo = \[descricao lowercaseString\]; return \[descricaoEmMinusculo stringByTrimmingCharactersInSet:\[NSCharacterSet whitespaceAndNewlineCharacterSet\]\]; }

@end \[/sourcecode\]

Para obter um Ponto a partir de um JSON chegamos ao seguinte código:

\[sourcecode language="objc"\] NSString \*url = \[NSString stringWithFormat:@"http://ondeestaoalbi.herokuapp.com/onibusesNosPontosProximos.json?lat=%f&long=%f", -23.588453, -46.632103\]; NSData \*jsonData = \[NSData dataWithContentsOfURL: \[NSURL URLWithString:url\]\];

NSError \*error; NSDictionary \*resultados = \[NSJSONSerialization JSONObjectWithData:jsonData options:NSJSONReadingMutableContainers error:&error\]; if(!error){ Ponto \*ponto = \[Ponto comDicionario: resultados\]; NSLog(@"Ponto: %@", ponto.descricao); } \[/sourcecode\]

Percebendo a dificuldade e a duplicação de código que temos que escrever ao buscar os valores no `NSDictionary`, desenvolvemos uma pequena biblioteca para facilitar essa tarefa. O `[KeyValueObjectMapping](https://github.com/dchohfi/KeyValueObjectMapping)` surgiu da necessidade de transformar grandes estruturas de dados automaticamente em objetos, seguindo padrões e quando necessário adicionando configurações para alterar as convenções. Poderíamos remover a implementação do método `+ (Ponto *) comDicionario: (NSDictionary *) dicionario;` e com o `KeyValueObjectMapping` teríamos:

\[sourcecode language="objc"\] DCKeyValueObjectMapping \*parser = \[DCKeyValueObjectMapping mapperForClass:\[Ponto class\]\]; Ponto \*ponto = \[parser parseDictionary:resultados\]; \[/sourcecode\]

Lembrando que o `KeyValueObjectMapping` serve para transformar qualquer tipo de dado que se comporte como um `NSDictionary`, chave e valor, podendo ser um JSON, um PLIST, entre outros. Você está convidado a contribuir nos projetos!
