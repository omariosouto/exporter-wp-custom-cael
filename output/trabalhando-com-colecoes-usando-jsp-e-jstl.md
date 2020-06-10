---
title: "Trabalhando com coleções usando JSP e JSTL"
date: "2006-08-22"
author: "gmoreira"
authorEmail: "guilherme.moreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em um projeto usando JSP 2 e JSTL como view, tenho um problema extremamente simples, e que deve ser comum a todos: como descobrir o `size()` de uma `Collection`. E como posso chamar o `contains()` dela, ou outro método qualquer, sem ser um `getter`?

É aí que muitos sentem saudades do [Velocity](http://jakarta.apache.org/velocity/), que permite escrever algo como `$colecao.contains(x)` e `$colecao.size`. Usando a expression language em um JSP isso não funciona, pois você só pode invocar getters. `$colecao.size` iria chamar `colecao.getSize()`, que obviamente não funciona. No Velocity e outros engines de template (como o [freemarker](http://freemarker.sourceforge.net/)), ele vai tentar arrancar o `size` de várias maneiras, inclusive fazendo `.size()` por reflection.

Ele ainda disse que muita gente que fez a especificação da expression language eram desenvolvedores do velocity, mas que esse tipo de recurso eles não colocaram no JSP pois você estaria colocando regras de negócios dentro da sua camada View, quebrando o MVC.

O Paulo costuma criar uma taglib com funçõezinhas estáticas para chamar do jsp, criando um .tld dentro do seu `WEB-INF`, por exemplo: \[xml\] <?xml version="1.0" encoding="UTF-8"?> <taglib version="2.0"> <tlib-version>1.1</tlib-version> <uri>http://www.caelum.com.br/taglib</uri> <function> <name>contains</name> <function-class>br.com.caelum.util.TagLibrary</function-class> <function-signature> boolean contains(java.util.Collection , br.com.caelum.nomedoprojeto.Option) </function-signature> </function> </taglib> \[/xml\]

Aí, você cria uma classe cheia de métodos estáticos (realmente não é elegante!): \[java\] public class TagLibrary { public static boolean contains(Collection options, Option option) { return options == null ? false : options.contains(option); } } \[/java\]

E acaba usando na página: \[html\] <%@ taglib uri="http://www.caelum.com.br/taglib" prefix="m" %>

<c:if test="${m:contains(listOfSelected, option)}">checked</c:if> \[/html\]

Já o Sérgio Lopes disse que você poderia colocar esse linha dentro de um scriptlet, desde que ela esteja dentro de um `.tag`. Depois o Paulo e o Sérgio começaram a brigardiscutir sobre as duas opções, e no fundo me parece que nenhum deles gosta muito de nenhuma das duas.

Uma outra saída seria ter alguns métodos que fazem esse trabalho nos seus value objects, como por exemplo ter `getTamanhoDaColecao()` . Mas isso nem sempre é possível e começa a gerar uma quantidade de métodos enorme que são totalmente redundantes.

Então eu pergunto, o que fazer para resolver esse tipo de problema com JSP e JSTL?
