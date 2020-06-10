---
title: "Mirror DSL: facilitando o uso da API de reflection"
date: "2008-11-17"
author: "jonas"
authorEmail: "jonas@vidageek.net"
main_guide: "main_guide"
main_category: "geral"
---

No último domingo foi feito o primeiro release público do projeto Mirror (versão 1.2).

O [Mirror](http://projetos.vidageek.net/mirror/) é um projeto que tem por objetivo facilitar o uso da [Java Reflection API](http://java.sun.com/javase/6/docs/api/java/lang/reflect/package-summary.html), removendo boa parte da burocracia (como as diversas checked exceptions que são lançadas) e utilizando uma [DSL](http://en.wikipedia.org/wiki/Domain-specific_language) para melhorar a legibilidade do código.

Com essa remoção de burocracia e a DSL, é possível transformar o seguinte código:

\[java\] Field toSet = null; for (Field f : target.getClass().getDeclaredFields()) { if (f.getName().equals("field")) { toSet = f; } } if (toSet != null && ((toSet.getModifiers() & Modifier.STATIC) == 0) && ((toSet.getModifiers() & Modifier.FINAL) == 0)) { toSet.setAccessible(true); toSet.set(target, value); } \[/java\]

em algo mais legível e expressivo:

\[java\] Mirror.on(target).set().field("fieldName").withValue(value); \[/java\]

Atualmente o Mirror possui suporte para lidar com as operações reflectivas mais comuns (como instanciar objetos, invocar métodos, ler ou escrever atributos, etc). Ele foi desenvolvido por Adriano Almeida, Diego Feitosa e eu, todos consultores/instrutores aqui da Caelum, enquanto enfretavamos problemas comuns no dia a dia.

Esperamos que possa ser útil para vocês também!
