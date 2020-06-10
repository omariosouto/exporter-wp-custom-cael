---
title: "RESTfulie com C# - O poder do dynamic"
date: "2010-02-26"
author: "luiz.costa"
authorEmail: "luiz.costa@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Há um mês lançamos uma versão inicial do projeto [Restfulie](http://restfulie.caelumobjects.com/ "Restfulie") para C#, [projeto que tem ganhado bastante visibilidade](http://www.infoq.com/interviews/Restfulie).  Para deixa-lo tão fácil quanto a [versão original em Ruby](http://restfulie.caelumobjects.com/ruby "versão original"), utilizamos as novas características dinâmicas da versão 4.0 do C#, alterando a estrutura de objetos em tempo de execução. Algumas pessoas acham [esse recurso perigoso demais](http://www.infoq.com/news/2009/11/Compromise-Dynamic-C-Sharp "isso perigoso demais"), [outras acham a escolha certa](http://are%20dynamic%20languages%20going%20to%20replace%20static%20languages/? "outras acham muito poderoso.").

Para enxergar o uso da nova palavra chave `dynamic`, consideremos um recurso REST que representa um pedido com sua representação em XML como a que segue:

\[xml\] <pedido> <data>26/12/2009 11:40</data> <total>300.00</total> <atom:link rel=”refresh” href=”http://www.caelum.com.br/pedidos/1″ xmlns:atom=”http://www.w3.org/2005/Atom”/> <atom:link rel=”update” href=”http://www.caelum.com.br/pedidos/1″ xmlns:atom=”…”/> <atom:link rel=”pagar” href=”http://www.caelum.com.br/pedidos’/1/pagar” xmlns:atom=”…”/> <atom:link rel=”excluir” href=”http://www.caelum.com.br/pedidos/1″ xmlns:atom=”…”/> <atom:link rel=”obterCliente” href=”http://www.caelum.com.br/pedidos/1/clientexmlns:atom=” xmlns:atom=”…”/> </pedido> \[/xml\]

É interessante notar que neste XML temos os dados (data, total) e temos ações que podem ser executadas sobre este recurso. Por exemplo, depois que obtivemos a representação deste recurso, é possível executar algumas ações, descritas através de links no próprio xml: `pagar`, `excluir`, `obterCliente`. Para obter a representação de um recurso no restfulie C#  fazemos da seguinte maneira:

\[csharp\] dynamic pedido = Restfulie.At(“www.caelum.com.br\\pedidos\\1”).Get(); \[/csharp\]

Repare que o primeiro passo que fazemos ao declarar a variável pedido é ignorar o seu tipo.  Ou melhor, dizer explicitamente que ela é dinâmica, através do tipo **[dynamic](http://msdn.microsoft.com/en-us/library/dd264741%28VS.100%29.aspx "dynamic")**. Isso nos trás uma série de vantagens como, por exemplo, simplesmente acessar suas [properties](http://msdn.microsoft.com/en-us/library/x9fsa0sw.aspx "properties"):

\[csharp\] Console.WriteLine(string.Format(“A data do pedido é: {0}”, pedido.Data)); Console.WriteLine(string.Format(“O valor total do pedido é: {0}”, pedido.Total)); \[/csharp\]

Além de podermos acessar suas properties, podemos também seguir os links que estão disponíveis na representação, utilizando invocações de métodos:

\[csharp\] pedido.Pagar(); pedido.Excluir();

dynamic cliente = pedido.obterCliente(); Console.WriteLine(string.Format(“Nome do cliente: {0}”, cliente.Nome)); \[/csharp\]

Isso é bastante poderoso: perceba que do lado cliente não precisamos definir nada, apenas dizer que uma referência é dinâmica. Mas como esta mágica acontece com o C#?

Além de existir a palavra reservada `dynamic` existe uma classe [DynamicObject](http://msdn.microsoft.com/en-us/library/system.dynamic.dynamicobject%28VS.100%29.aspx "DynamicObject") no C#. Diferente de Ruby, em C#, nem todos os objetos podem ser [modificados em Runtime.](http://dotnetslackers.com/articles/csharp/Dynamic-Binding-in-a-Static-Language-Part-1.aspx "modificados em Runtime.") Para que seja possível adicionar comportamento dinâmico em a um objeto, precisamos estender a classe `DynamicObject`.

Ao estender esta classe, ganhamos a oportunidade de alterar o comportamento do objeto. Quando fizermos então uma invocação a `pedido.Total`, o que de fato tem que ocorrer aqui? De acordo com o xml de representação do recurso, devemos obter o valor que está na tag <Total>.

Para implementar isso utilizamos um recurso bem parecido com o [method\_missing em Ruby](http://andersonleiteblog.wordpress.com/2010/02/10/metaprogramacao-method_missing/ "method_missing em Ruby") e, de certa forma, como as dynamic proxies do Java (apesar destas precisarem de interfaces explícitas). Vejamos a classe `DynamicXmlResource`:

\[csharp\] class DynamicXmlResource : DynamicObject { private XElement xmlRepresentation;

public override bool TryGetMember(GetMemberBinder binder, out object result) { //pseudo código que encontra a tag xml de acordo //com o nome da property informada object = XmlRepresentation.FindXMLTagWithName( binder.Name) .ReadTheValue() return result != null; } } \[/csharp\]

Esta classe estende `DynamicObject` e tem um atributo que é a representação do recurso em XML (xmlRepresentation). O mais interessante é o método `TryGetMember` que intercepta qualquer invocação a um `Get` de uma property e nos permite fazer o que for necessário. No nosso caso, o que é feito é buscar o valor da da property no xml, que nesse caso tem o nome igual ao da tag do xml. Com isso, toda vez que fizermos `pedido.Total`, o que está acontecendo é a invocação de `TryGetMember` da classe `DynamicXmlResource`.

Da mesma forma que existe o método `TryGetMember`, existem outros que nos permitem alterar o comportamento do objeto. No nosso projeto, também fizemos o uso do `TryInvokeMember`:

\[csharp\] class DynamicXmlResource : DynamicObject { private XElement xmlRepresentation;

public override bool TryGetMember(GetMemberBinder binder, out object result) { result = XmlRepresentation.FindXMLTagWithName(binder.Name).ReadTheValue() return result != null; }

public override bool TryInvokeMember(InvokeMemberBinder binder, object\[\] args, out object result) { // pseudo código para pegar o link no xml a partir do nome do método object link = XmlRepresentation.FindRelAttributeWithName(Binder.Name).ReadTheLink(); if (link == null) throw new ArgumentException (string.Format("There is not method defined with name:", binder.Name));

// faz a chamada remota para o servidor através do link. HttpRemoteResponse response =  (HttpRemoteResponse) this.InvokeRemoteResource(value.ToString(), binder.Name); return result != null; } } \[/csharp\]

Este método também é parecido com o `method_missing`, mas neste caso ele intercepta a chamada de um método. No caso do restfulie,  quando chamamos um método qualquer, o que ele faz é, procurar na representação XML e descobrir o link relacionado com o nome do método. Depois disso é só fazer uma requisição http. Novamente, quando fizermos a invocação **pedido.Pagar()**, ela será interceptada pelo `TryInvokeMember` e executará uma invocação remota para o Restfulie Server.

Utilizando estas características do C# conseguimos alcançar algo muito próximo do que se faz com o Restfulie Ruby, como comparamos a seguir. Em Ruby:

\[ruby\] pedido = Restfulie.at(“http://restfulie-test.heroku.com/orders/14”).get puts pedido.customer-name pedido.cancel \[/ruby\]

E, em C#, temos o código tão sucinto quanto:

\[csharp\] dynamic pedido = Restfulie.At(“http://restfulie-test.heroku.com/orders/14.xml”).Get(); Console.WriteLine(pedido.customer\_name); pedido.Cancel(); \[/csharp\]

Uma das consequências da utilização de tipos dinâmicos é que perdemos as vantagens da fase da tipagem estática: não há code complete, e pode ser que a gente erre o nome de um membro, como `customer_name`, e só viremos a saber disso em tempo de execução. Nada que testes unitários bem colocados não resolvam.

Como podemos ver, a utilização de tipos dinâmicos facilita bastante a utilização da api do Restfulie, assim como diversos outros frameworks vem tirando vantagem com o auxílio desse recurso.

Reforçando a tendência, o Java também anda nesse caminho, com a [JSR 292](http://jcp.org/en/jsr/detail?id=292 "JSR 292"), que melhora o suporte de linguagens dinâmicas na JVM. Em abril de 2003 Robert C. Martin escreveu o artigo "[As linguagens dinâmicas vão substituir as estáticas?"](http://www.artima.com/weblogs/viewpost.jsp?thread=4639 "Are Dynamic Languages Going to Replace Static Languages?"") e, no final deste artigo, ele faz seguinte pergunta: _Estaremos todos nós programando numa linguagem dinamicamente tipada em 2010?_. Isso não aconteceu, mas com certeza cada vez mais cenários de utilização estão surgindo, e a tendência é cada vez mais forte.
