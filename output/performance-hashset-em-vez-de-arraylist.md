---
title: "Performance: HashSet em vez de ArrayList"
date: "2006-10-05"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando um programador começa com Java, ele rapidamente desiste das arrays para trabalhar com a `ArrayList`, que encapsula algumas rotinas comuns e trabalhosas. Depois o programador começa a se preocupar mais com o encapsulamento e passa a se refernciar as `ArrayList`s como `List`.

Um último passo que é mais díficil do programador tomar é de abstrair suas referências para `Collection`. Isso talvez ocorra por causa da óbvia ausência do método `get(int)` na interface `Collection`, apesar de que em muitos casos estamos apenas usando uma coleção para guardar um punhado de referências, sem nos importar com a ordem em que vamos percorrê-las e nem vamos precisar pegar uma referência que está em determinada posição (acesso aleatório). É nesse caso que a utilização dos `Set`s (em especial os que usam tabelas de espalhamento) é fortemente indicada (e mesmo que você precisasse garantir a ordem para iterar as referências, poderia usar uma coleção como a `LinkedHashSet`).

No curso de Java e Orientação a Objetos da Caelum, temos alguns exercícios extras. No capítulo de coleções tentamos mostrar a grande diferença de se usar um `HashSet` em vez de uma `ArrayList` quando precisamos realizar muitas buscas nessa coleção (isto é, usar o `contains()`). Você pode baixar essa apostila [através do site](http://www.caelum.com.br/caelum/curso-11.jsp).

Segue um código em que inserimos 30 mil referências a objetos do tipo `Integer` em uma `ArrayList`, e depois fazemos uma busca por cada um deles. Repare que 30 mil não é um número incomum.

\[java\] public class TestaCollections { public static void main(String\[\] args) { Collection<Integer> colecao = new ArrayList<Integer>();

long tempoInicial = System.currentTimeMillis();

for (int i = 0; i < 30000; i++) { colecao.add(i); }

for (int i = 0; i < 30000; i++) { colecao.contains(i); }

long tempoFinal = System.currentTimeMillis(); System.out.printf("%.3f segundos%n", (tempoFinal - tempoInicial) / 1000d); } } \[/java\]

O resultado: `8,891 segundos`.

Vamos agora realizar o mesmo teste só que com uma estrutura que utilize [tabelas de espalhamento](http://en.wikipedia.org/wiki/Hash_table), nesse caso o `HashSet`. Mudamos a seguinte linha: \[java\] Collection<Integer> colecao = new ArrayList<Integer>(); \[/java\] Para: \[java\] Collection<Integer> colecao = new HashSet<Integer>(); \[/java\]

O novo resultado? Incríveis `0,078 segundos`. 114 vezes mais rápido! E se você aumentar o tamanho dos laços dos testes, essa proporção só vai aumentar! Porque isso? O `contains` da `ArrayList` faz uma busca linear, já o `HashSet` utiliza uma tabela de espalhamento para tentar fazer a busca em tempo constante.

Você ainda pode fazer outros testes interessantes. Um `TreeSet` será mais devagar para inserir, pois mantém uma [árvore rubro negra](http://en.wikipedia.org/wiki/Red-black_tree) como estrutura de dados interna, e sua busca muito melhor que a da `ArrayList` e um pouco mais lenta que a do `HashSet`, porém teremos nossos elementos ordenados. Comparar a inserção e busca de elementos entre `LinkedList` e `ArrayList` também mostra números impressionantes: percorrer uma `LinkedList` sem usar um `Iterator` (ou enhanced for) é absurdamente lento, já que o seu método `get(int)` percorre a lista toda desde o começo, por sua vez, na `ArrayList` inserir elementos nas posições iniciais não é nada performático (é necessário deslocar todo o restante da array pra frente).

Obviamente uma [boa função de hash](https://blog.caelum.com.br/2006/09/04/ensinando-que-e-o-hashcode/) também é essencial. Recentemente a Sun [melhorou a função de hash dos nomes dos métodos](http://bugs.sun.com/bugdatabase/view_bug.do?bug_id=6393408) que o compilador utilizava para depois procurá-los, e com apenas isso conseguiu uma melhoria de performance de 2% na execução geral. Imagine se eles utilizassem uma `List` para isso? Dominar as coleções e estruturas de dados do Java é fundamental para um bom código.
