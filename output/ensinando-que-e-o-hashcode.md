---
title: "Ensinando o que é o hashCode"
date: "2006-09-04"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Sem dúvida um dos pontos mais difíceis para quem está fazendo um curso inicial de java é entender `hashCode`, em especial para quem nunca viu estruturas de dados além de filas e pilhas, isso porque o desenvolvedor já está **bem** confuso em ter seu primeiro contato com a API das coleções, sintaxe do generics e uso extensivo de interfaces.

Aqui vou mostrar como costumo fazer essa explicação, e depois sugerir uma grande melhoria.

Costumo usar o exemplo de pesquisar por uma `String` dentro de uma lista de `String`s. A busca seqüencial vai fazer até `lista.size()` comparações, o que pode ser muito grande. Então como diminuir esse número de comparações? Espalhando nossas Strings de acordo com algum critério, e colocando-as dentro de uma [tabela de espalhamento](http://en.wikipedia.org/wiki/Hash_table) (_hashtable_, _hash_\=espalhar).

Mas como espalhá-las? Podemos classificar essas Strings de acordo com a sua primeira letra em uma array de listas (`List<String>[]`): `strings[65]` teria uma lista de Strings que começam com a letra **A**, e assim por diante. O problema é que, por exemplo, podemos ter todas nossas strings começando com **A** gerando a quantidade máxima de colisões em uma mesma lista! Daqui podemos elaborar a idéia para que as Strings sejam indexadas por um critério mais interessante. Por exemplo, multiplicar o valor inteiro de cada char: `paulo` valeria `p*a*u*l*o`, que é `112*97*117*108*111` = `-1942066240` (explodiu `Integer.MAX_VALUE`). Esse número pode ser muito grande ou não nos servir por algum motivo, então nosso `HashMap` vai limitar o tamanho da array, e fazer algo como `-1942066240 % array.length` para saber em que [bucket](http://en.wikipedia.org/wiki/Hash_table#Choosing_a_good_hash_function) colocar esse ítem.

E continuo nessa linha de pensamento para explicar o [contrato do método `hashCode`](http://java.sun.com/javase/6/docs/api/java/lang/Object.html#hashCode()), realocação do tamanho da tabela de espalhamento e que isso tudo é utilizado na implementação de [arrays associativas](http://en.wikipedia.org/wiki/Associative_array) em algumas linguagens.

Os principais problemas desse exemplo:

- Alguns desenvolvedores conhecem um pouco de árvores binárias, e querem usá-las. O problema é que tabelas de espalhamento são muito mais rápidas na pesquisa, além de que usar árvores binárias necessita de um critério de comparação entre os objetos, o que não é necessário em espalhamentos;
- Gerar um `int` a partir de uma `String` atrapalha bastante. Repare a quantidade de passos que precisei passar: transformação de cada char em seu valor numérico da tabela unicode, multiplicar tudo, fazer mod, etc. Seria melhor se o objeto em questão já tivesse alguma relação numérica, e que não precisássemos fazer contas em cima de seus valores;
- Pessoalmente sinto que poderia ser melhor.

Um amigo analista que trabalha no asset management de um grande banco brasileiro conversava comigo quando surgiu a necessidade dele encontrar dois arquivos de conteúdo idêntico que estavam no mesmo diretório. O problema é que esse diretório tem mais de 10 mil arquivos! Ele mesmo sugeriu fazer uma comparação primeiramente ordenando os arquivos por tamanho, e só vale comparar byte a byte os arquivos que possuírem o mesmo tamanho, os outros obviamente são diferentes entre si. Disse então para ele que esse era um excelente exemplo de tabelas de espalhamento, e que ele não precisaria nem mesmo ordenar os arquivos por tamanho, bastando separá-los!

Repare que esse meu amigo não é programador, mas todo engenheiro e administrador que trabalha em banco acaba sujando suas mãos com centenas (as vezes milhares) de linhas de VB dentro de funções excel. Isso mostra como a idéia de indexação e hash são muito simples e intuitivas, às vezes o que atrapalha é o professor :).

Vamos ver como ficaria ensinar com esse exemplo: o contrato do método hashCode diz que:

se `a.equals(b)` então `a.hashCode() == b.hashCode()`

Pensando no problema da comparação dos arquivos, podemos falar:

`fileA` é equivalente a `fileB` então tamanho de fileA == tamanho de fileB

Colocando em java: se fileA.equals(fileB) então `fileA.length() == fileB.length()`

Reescrevendo `hashCode` para espalhar de acordo com esse nosso critério, teríamos finalmente:

se fileA.equals(fileB) então fileA.hashCode() == fileB.hashCode()

Muito mais simples e direto que o exemplo da `String`! Aqui o debate poderia prosseguir em encontrar um hashCode que espalhasse melhor os arquivos, e o assunto cairia em [checksums](http://en.wikipedia.org/wiki/Checksum)... mais perfeito impossível!

Vale lembrar que eu estou falando aqui de uma classe `File` imaginária, pois a `java.io.File` não leva em consideração o conteúdo do arquivo para o `equals` nem para o `hashCode`, ela usa apenas o abstract pathname em consideração.
