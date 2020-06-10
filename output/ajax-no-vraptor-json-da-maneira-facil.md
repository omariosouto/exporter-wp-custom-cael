---
title: "Ajax no VRaptor: JSON da maneira fácil"
date: "2006-11-09"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Vamos falar um pouco de web antes que o Paulo Silveira transforme esse blog em um livro de Hibernate!

O Fabio Kung deu a idéia de usar [JSON](http://json.org/) para fazer a estrutura básica de Ajax do [VRaptor](http://www.vraptor.com.br/). O Paulo, que detesta(va) trabalhar com JavaScript e interfaces com o usuário, gostou bastante da maneira simples de se representar objetos com JSON. Ele correu atrás de diversas bibliotecas como a [JSON-tools](http://developer.berlios.de/projects/jsontools/) e a [JSON-lib](http://json-lib.sourceforge.net/) mas achou que uma dependência apenas para isso seria muita coisa, já que não haveria a necessidade de consumir JSON como Java, apenas produzir. Ele encontrou um [código na internet bem simples](http://blog.stringtree.org/2006/08/12/json/), mas quando foi usar descobriu uma série de testes que falhariam, então ele, juntamente com o Nico Steppat, mexeu e criou o próprio [JSONWriter do VRaptor](http://vraptor2.cvs.sourceforge.net/vraptor2/vraptor2/src/main/java/org/vraptor/remote/json/JSONWriter.java?revision=1.4&view=markup), junto com um [longo test case](http://vraptor2.cvs.sourceforge.net/vraptor2/vraptor2/src/test/java/org/vraptor/remote/JSONSerializerTest.java?revision=1.3&view=markup).

JSON é uma notação simples para definir uma variável em JavaScript que pode ser facilmente traduzida para um objeto de outras linguagens. Muitos frameworks para AJAX, como o [Dojo](http://dojotoolkit.org/) e o [Prototype](http://www.prototypejs.org/), consomem mensagens JSON. E agora no VRaptor, dada uma simples classe como essa:

\[java\] @Component public class ContatoController {

private List<User> users = new ArrayList<User>();

@Remotable public void lista() { // puxaria do banco users.add(new User(1,"Paulo")); users.add(new User(2,"Guilherme")); } public List<User> getUsers() { return users; } } \[/java\]

Basta você anotar o método como `@Remotable` e acessar `contato.list.ajax.logic` pela URL, que o resultado será:

`{"users":[{"nome":"Paulo","id":1},{"nome":"Guilherme","id":2}]}`

Pronto para ser consumido por um browser! Você pode ver no site [mais detalhes sobre o AJAX com o VRaptor](http://vraptor.org/ajax.html).

O Paulo ficou empolgado com a remotabilidade, e agora está implementando o RESTful do VRaptor da mesma maneira: `contatos.lista.xml.logic` vai te renderizar os mesmos objetos só que em XML! Já está no CVS e estará presente uma versão básica na versão 2.2.4 (assim como integração com Spring). Isso será não só útil para expor serviços, mas também para integrar seus componentes VRaptor com consumidores de XML como o [Adobe Flex](http://www.adobe.com/products/flex/) e o [OpenLaszlo](http://www.openlaszlo.com/). O próximo passo é aceitar as requisições XML e JSON, não só apenas produzi-los.

Dei hoje uma palestra sobre esse assunto no SouJava, fazendo analogias com [SOA](http://en.wikipedia.org/wiki/Service-oriented_architecture) e [Service Component Architecture](http://www.osoa.org/display/Main/Service+Component+Architecture+Home) que o Paulo tanto elogia no [Apache Tuscany](http://incubator.apache.org/tuscany/). Ele quer dirigir o desenvolvimento do VRaptor para esse lado: poder expor facilmente seus componentes web como serviços, facilitando a futura integração e manutenção, dois pontos que nós desenvolvedores sofremos muito atualmente com os sistemas legados.
