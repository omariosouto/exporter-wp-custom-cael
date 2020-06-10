---
title: "Atribuindo null"
date: "2007-01-04"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um post bem curto sobre uma prática que aparece comumente em códigos por aí: \[java\] public void metodoQueListaUsuarios() { List<Usuario> lista = dao.pegaListaDeUsuarios(); for(Usuario u : lista) { System.out.println(u.getNome()); } lista = null; // pra que isso? } \[/java\]

Essa atribuição da `lista` para `null`, como muitos de vocês sabem, é totalmente desnecessária, pois ao fim do método o escopo da variável `lista` termina e ela morre automaticamente, já que essa é uma variável de pilha. Achar que isto está ajudando o Garbage Collector é um engano.

Porém tem alguns casos que isto é aplicável. O primeiro é se `lista` fosse um atributo, e não uma variável local ao método. Mas se você está atribuindo `null` a um atributo no fim de todo método, ele tem grande potencial de ser refatorado para varíaveis locais.

O segundo caso é quando você tem um objeto `X` alocado no heap e não vai usá-lo mais, porém você ainda tem trabalho a fazer no escopo da variável que referencia `X`. O Garbage Collector não poderá coletar `X`, nem mesmo marca-lo para coleta, enquanto a sua referência existir, e pode ser que ela continue existindo por muito tempo, como no código abaixo:

\[java\] public void metodoQueListaUsuarios() { List<Usuario> lista = dao.pegaListaDeUsuarios(); for(Usuario u : lista) { System.out.println(u.getNome()); } lista = null; dao.fazOperacaoDemorada(); // lista ainda em uso? } \[/java\]

Neste caso faz sentido tentar liberar a variável o mais rápido possível. Caso contrário, durante a nossa `dao.fazOperacaoDemorada()`, o objeto estará presente na memória e não poderá ser coletado (as vezes, em vez de atribuir `null`, podemos obter o mesmo efeito criando um novo escopo com `{}`). Claro que essa atribuição merece ser documentada. Essa `operacaoDemorada` poderia ser mais grave ainda caso a thread vá ser colocada em `wait`.

Dizem também, mas aí já está além do meu conhecimento, que a virtual machine hoje em dia consegue detectar essas variáveis que não tem mais uso em um determinado escopo, eliminando-a antes mesmo que o seu escopo feche.
