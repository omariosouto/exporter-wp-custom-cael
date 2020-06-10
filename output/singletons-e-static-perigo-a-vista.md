---
title: "Singletons e static: perigo a vista"
date: "2006-08-08"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Este [post no guj](http://guj.com.br/posts/list/38669.java) trouxe uma discussão já conhecida de volta a ativa: singleton. Devemos usar singletons ou métodos estáticos?

O [Carlos Villela](http://lixo.org) já coloca uma opinião muito interessante:

> Estado mantido de forma estática numa aplicação é tão venenoso quanto variáveis globais. Não importa como esse estado é acessado. Seja via singletons ou métodos estáticos, vai continuar sendo venenoso.

Mais para frente, ele ainda diz:

> Singletons é um indício de que você precisa pensar melhor sua arquitetura

Muitas pessoas (inclusive eu) consideram o Carlos Villela um extremista da orientação a objeto. Mas nesse tópico eu concordo totalmente com ele: estamos viciados em usar Singletons apenas para facilitar nossa busca por uma varáivel global (no caso de OO, uma referência a um objeto que é compartilhado globalmente). Quando que realmente faz sentido existir um e apenas um objeto de determinado tipo? É raro.

Em outras palavras, você deve evitar ao máximo a utilização de métodos estáticos (que tem um sabor de função procedural) e nem singletons (com o sabor variável global). Como resolver essa nossa necessidade? Certamente com [injeção de dependências](http://www.martinfowler.com/articles/injection.html). Algumas pessoas já classificam Singleton como um **antipattern**: uma má solução para o seu problema.

A idéia é que você tire a responsabilidade de fazer lookup de recursos da sua lógica de negócio, e que esse seu recurso (essa sua _dependência_) seja lhe dada (_injeção de dependências, DI_) por outra pessoa, invertendo assim os papéis (inversão de controle, _IOC_): você não mais busca por um recurso, esse recurso é dado a você!

Entendo que muitos dos leitores já estão cansados de usar essas técnicas, mas vale lembrar que elas são úteis também no nosso caso de Singletons. Tanto que você vai encontrar diversos tópicos por aí como este: [Como refatorar Singleton para injeção de dependências](http://forum.springframework.org/showthread.php?t=12130).

Mais abaixo o Villela fala do interessante recurso que alguns linguagens consideradas mais puramente OO tem, que no Java seria equivalente ao seguinte trecho (apenas ficção):

\[java\] Integer i = 5; Integer raiz = i.squareRoot(); \[/java\]

Imagine todas as classes wrappers terem todos os tipos imagiáveis de operações? Não só elas, mas também `BigInteger` e `BigDecimal`? Nesse caso ainda prefiro as funçõezinhas procedurais da `java.lang.Math` :).
