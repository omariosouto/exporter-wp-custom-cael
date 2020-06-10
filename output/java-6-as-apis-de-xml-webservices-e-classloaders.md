---
title: "Java 6, as APIs de XML, Webservices e classloaders"
date: "2007-12-17"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A Sun vem há [muito tempo](http://jcp.org/en/jsr/detail?id=67) fazendo esforços para facilitar a manipulação de [XML e de webservices](http://community.java.net/java-ws-xml/) na plataforma. São tantos projetos, subprojetos e especificações que podemos facilmente nos encontrar perdidos no meio de tantas siglas. Elas são:

[JAXP](http://jcp.org/en/jsr/detail?id=206) (pacote `java.xml` no geral) - Processamento geral de XML, com os já antigos SAX e DOM, além de transformadores (XLST) e XPath. [JAXB](http://jcp.org/en/jsr/detail?id=222) (pacote `javax.xml.bind`) - Assocaição/mapeamento de classes java para XML.

[JAX-WS](http://jcp.org/en/jsr/detail?id=224) (pacote `javax.xml.ws`) - Criação e consumo de webservices. Aliada a [especificação de metadados para webservices](http://jcp.org/en/jsr/detail?id=181) (`pacote javax.jws`), [previamente já vista aqui no blog](https://blog.caelum.com.br/criando-um-webservice-com-a-jsr-181/), a JAX-WS tornasse poderosa e fácil de usar. [JAXR](http://jcp.org/en/jsr/detail?id=93) (pacote `javax.xml.registry`) - para acesso aos registros de serviços XMLs, como UDDI. [JAX-RPC](http://jcp.org/en/jsr/detail?id=101) (pacote `javax.xml.rpc`) - era o nome antigo do atual JAX-WS. O JAX-WS mudou de nome e já apareceu como 2.0, essa mundaça foi justificada pelo fato dessa API passar a trabalhar bem mais próxima da API do JAXB, além do óbvio marketing.

E a Sun não para por aí, temos mais especificações: a duvidosa JSR 267 que possibilita um JSP acessar diretamente um webservice (!) através de taglibs e a esperada [JSR 311](http://jcp.org/en/jsr/detail?id=311) para trabalhar com serviços de maneira RESTful, oferecendo simples anotações para expor métodos Java através de URI + métodos HTTP.

Não são [todos subprojetos de manipulação de xml com java](http://wiki.java.net/bin/view/Javawsxml/ProjectStatus) que viraram especificações, e as que viraram nem todas estão no Java SE. A [Fast Infoset](http://asn1.elibel.tm.fr/xml/finf.htm) é uma especificação ISO para representação binária do padrão XML (economizando assim espaço e banda, além de melhorar performance do parsing) possui [uma implementação Java](https://fi.dev.java.net/), utilizada dentro do [Metro](https://metro.dev.java.net/), projeto que fornece os recursos de webservices do Glassfish.

[Muitas dessas APIs](http://java.sun.com/javase/technologies/webservices.jsp), juntamente com implementações de referência (RIs), agora estão presentes no java SE 6.0, que antes eram opcionais. Qual é o problema disso? Até então diversos servidores de aplicação e frameworks traziam embutido implementações do JAXB, JAX-WS, etc. Ao rodar essas aplicações com o Java 6 o sistema de classloading da plataforma vai primeiro carregar as classes da api padrão, mesmo que você tenha implementações dessas APIS de XML no classpath. O ruim aqui é que muitos servidores de aplicação acabam se amarrando a detalhes de sua própria implementação e versão, como é [esse caso do JBoss](http://www.jboss.com/index.html?module=bb&op=viewtopic&t=99036) com o JAX-WS. Quando rodado com o Java 6, temos a seguinte exception quando você tenta acessar um webservice que está implantando no servidor:

`java.lang.UnsupportedOperationException: setProperty must be overridden by all subclasses of SOAPMessage at javax.xml.soap.SOAPMessage.setProperty(SOAPMessage.java:424)`

Enfrentamos esse problema recentemente, e para [resolve-lo](http://thesmallpotato.blogspot.com/2007/06/jdk-16-jboss-420-ejb3-web-service-axis2.html) usamos o sistema de endosso de jars ([endorsed jars](http://java.sun.com/j2se/1.5.0/docs/guide/standards/index.html)) do Java: determinados diretórios podem ser configurados para que alguns pacotes específicos possam ser carregados destes antes do Classloader tentar chegar ao rt.jar.

[Um](http://weblogs.java.net/blog/kohsuke/archive/2006/12/jaxb_21_release.html) [outro](http://weblogs.java.net/blog/kohsuke/archive/2007/02/running_jaxbws.html) [problema](http://weblogs.java.net/blog/kohsuke/archive/2007/02/howitworks_runn.html) [comum](http://www.guj.com.br/posts/list/76001.java) é com o JAXB: o Java 6 vem com a versão 2.0, se você precisar usar a 1.1 ou a 2.1, vai ter problemas. O interessante é que a JAXB do Java SE já foi projetada para ela mesma detectar se o classloading foi correto, ou se partiu de uma versão posterior/anterior a ela, mostrando uma mensagem de erro amigável. Para outras bibliotecas esse problema pode ser **muito** sutil: o classloader pode acabar carregando **parte** da biblioteca de uma versão recente, já que algumas classes novas só existem nesse jar, e o restante de uma outra antiga, resultando exceptions como `NoSuchMethodError`, que não mostram claramente que o problema é a existência de dois jars de versões diferentes no classpath daquela aplicação.

Podemos ver que mesmo seguindos boas práticas, isolando bibliotecas e não usando a terrível variável de ambiente CLASSPATH, acabamos sempre enfrentando o [classloader](http://nullability.org/?p=95) [hell](http://www.kumpera.net/blog/index.php/2007/05/09/classloader-hell/).
