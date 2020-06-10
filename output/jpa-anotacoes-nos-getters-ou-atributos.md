---
title: "JPA: anotações nos getters ou atributos?"
date: "2007-06-26"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A [especificação da JPA](http://jcp.org/en/jsr/detail?id=220) permite que você utilize as anotações em uma `Entity` de duas formasou nos atributos, ou nos _getters_. Também diz que não devemos misturar isso, pois o comportamento resultante não é especificado. Na época em que a [JPA estava em draft](http://jcp.org/aboutJava/communityprocess/edr/jsr220/index.html), havia até um atributo na anotação `@Entity` em que você explicitava onde iria usar as anotações.

Se temos essas duas opções, qual devemos usar? Usando o hibernate, há uma diferença fundamental entre as duas abordagens: quando e se o objeto será carregado.

Considere a seguinte classe, anotada pelos atributos:

`@Entity  
**public class** Funcionario {  
 @Id  
 @GeneratedValue  
 **private** Long id;  
  
 **private** String nome;  
  
 // getters e setters  
}`

E agora um pequeno código para carregar, de maneira lazy, um `Funcionario` de id 1:

 `Funcionario f = manager.getReference(Funcionario.class, 1l);  
 System.out.println(f.getId());   
 System.out.println(f.getNome());` Com a propriedade `show_sql` valendo `true`, o código ao rodar produz:

`--- query select executada ---
1
Paulo Silveira` 

Se em vez de anotarmos o atributo `id` com `@Id` e `@GeneratedValue`, anotarmos o método `getId`, obteríamos:

`1
--- query select executada ---
Paulo Silveira` 

Em outras palavras, se no final você só usasse a chave primária (como poderia acontecer se a view fosse uma página web e caísse em uma condição particular), a query nem mesmo teria sido executada! Com a anotação diretamente no atributo, qualquer invocação de método em um objeto carregado mesmo com o `getReference` vai disparar a query.

É interessante sempre anotar os _getters_ em vez dos atributos, mas vale reparar que na maioria absoluta dos casos isso não vai trazer benefícios. Até mesmo porque, em uma aplicação real, estaremos com o cache de segundo nível habilitado, tornando desnecessárias essas pequenas otimizações.
