---
title: "Visitando uma Collection em ordem inversa"
date: "2006-08-30"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

As vezes troco algumas mensagens bem técnicas com alguns colegas pelo GTalk, e aí aparecem algumas charadas interessantes.

O Orseni Campos, um desenvolvedor que eu admiro muito, passou o seguinte problema: "_Paulo, como percorrer uma coleção qualquer na ordem inversa de maneira elegante?_". Quando ele disse _maneira elegante_, pensei que ele queria evitar criar uma lista e acessá-la via `lista.get(indice)` de trás para frente. Respondi o trivial: que o `Collections.reverse` resolveria: \[java\] public void imprimeInvertidoClassico(Collection<? extends Object> colecao) { List<Object> invertida = new ArrayList<Object>(colecao); Collections.reverse(invertida); for (Object objeto : invertida) { System.out.println(objeto); } } \[/java\]

Ele não gostou, queria uma maneira de percorrer a coleção invertida sem criar uma coleção temporária, nem mexer na ordem dos objetos da coleção dada.

Desisti. Ele respondeu: "_basta visitar a coleção recursivamente, realizando a ação como pós processamento!_". Perfeito. Uma técnica das [linguagens funcionais](http://en.wikipedia.org/wiki/Functional_programming) e adorada pelos professores de conceitos de linguagens. Aliás, universidades importantes como MIT utilizam linguagens funcionais no curso introdutório de computação, acreditam que é mais fácil do aluno assimilar os conceitos dos algoritmos ([algumas dessas linguagens](http://en.wikipedia.org/wiki/Scheme_programming_language) são dos [mesmos criadores do Java](http://en.wikipedia.org/wiki/Guy_L._Steele)).

Então traduzi a idéia para código: \[java\] public void imprimeInvertido(Collection<? extends Object> colecao) { imprimeInvertido(colecao.iterator()); }

private void imprimeInvertido(Iterator<? extends Object> iterator) { if (iterator.hasNext()) { Object object = iterator.next(); imprimeInvertido(iterator); System.out.println(object); } } \[/java\]

Aqui, o método `imprimeInvertido` rapidamente delega a responsabilidade para o que recebe um `Iterator` como argumento. Este faz uma invocação recursiva no caso de existir um próximo elemento a ser visitado. O importante aqui é que a referência trazida pelo `iterator.next()` só é processado **depois** que seus elementos vizinhos forem, realizando o processo na ordem contrária da determinada pelo `Iterator`. (repare que considerando que a ordem definida pelo `Iterator` define uma lista, e que toda lista é uma árvore, estamos percorrendo essa ávore de maneira _pós-fixa_).

Ok, funciona e não cria coleção temporária! Mas se você parar para analisar bem, cada invocação recursiva empilha mais um _stackframe_, e em cada um temos uma variável temporária (a `Object object` declarado dentro do `if`) para ser recuperada quando a invocação retornar. Pensando na última invocação (base da recursão, quando o `iterator` chegar ao final e não entrar no `if (iterator.hasNext())`) teremos uma pilha com `colecao.size()` _stackframes_, alocando uma memória proporcional ao tamanho da coleção, muito semelhante a ter alocado a coleção temporária para o `Collections.reverse`.

Você ainda corre o risco de um `StackOverflowError` por estar gastando memória da pilha em vez do heap! Outra desvantagem é a legibilidade: nós programadores java não estamos acostumados a utilizar recursão para resolver problemas que poderiam ser facilmente resolvidos de maneira iterativa.

O exercício vale para relembrar que é importante manter contato com outras linguagens, para não ficar viciado no mesmo paradigma, o que te limita na hora de encontrar soluções. Em alguns casos a solução recursiva pode ser muito mais trivial de encontrar, desde que você esteja com o paradigma afiado!

Alguma outra maneira interessante de percorrer uma coleção na ordem inversa?
