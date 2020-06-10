---
title: "Manipulando arquivos com recursos do Java 8"
date: "2015-07-14"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Há algum tempo, minha esposa pediu que eu descobrisse quantas horas semanais os servidores públicos federais trabalham. Essa informação está disponível no [Portal de Transparência](http://portaldatransparencia.gov.br/downloads/servidores.asp) do Governo Federal.

Baixei um .csv com mais de 730 mil linhas e com quase 320 MB depois de descompactado. Estudei a estrutura do arquivo e resolvi fazer um programa Java para extrair as informações necessárias, utilizando alguns recursos do Java 8.

É importante que você esteja minimamente familiarizado com [lambdas, streams, default methods e method references](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/), para entender bem esse post.

## Lendo as linhas do arquivo

A primeira tarefa é ler as linhas do arquivo.

No Java 8, a classe `java.nio.files.Files` ganhou o método `lines`, que retorna um `java.util.stream.Stream`.

Temos que passar um `java.nio.files.Path` com o caminho do arquivo. Vamos utilizar a classe `java.nio.files.Paths`, para obter o arquivo `20140228_Cadastro.csv`, que está na sub-pasta `Downloads/201402_Servidores/` da minha pasta pessoal. A propriedade `user.home` contém o caminho da pasta pessoal do usuário.

Faremos isso com o código:

\[code language="java"\] Path caminho = Paths.get(System.getProperty("user.home"), "Downloads/201402\_Servidores/20140228\_Cadastro.csv"); Stream<String> linhas = Files.lines(caminho); \[/code\]

O código anterior, quando executado, lança a exceção `java.nio.charset.MalformedInputException`. Acontece que o método `Files::lines` considera por padrão o [encoding](https://blog.caelum.com.br/entendendo-unicode-e-os-character-encodings/) UTF-8. Mas nosso arquivo está com o encoding ISO-8859-1. Precisamos passar um `java.nio.charset.Charset`, com o encoding correto. Para isso, vamos utilizar a classe `java.nio.charset.StandardCharsets`:

\[code language="java"\] Path caminho = Paths.get(System.getProperty("user.home"), "Downloads/201402\_Servidores/20140228\_Cadastro.csv"); Stream<String> linhas = Files.lines(caminho, StandardCharsets.ISO\_8859\_1); \[/code\]

Poderíamos imprimir as linhas utilizando `linhas.forEach(System.out::println)`. Mas não é nosso objetivo (além de demorar bastante!).

## Encontrando a informação relevante

Estamos interessados apenas nas horas semanais, e não na linha toda. Quero extrair das linhas trechos como: 32,5 HORAS SEMANAIS, 40 HORAS SEMANAIS e DEDICACAO EXCLUSIVA.

Para extrair esses trechos, utilizaremos uma expressão regular, ou _regex_.

Criaremos uma constante HORAS, que terá um `java.util.regex.Pattern` com a regex: \[code language="java"\] private static final Pattern HORAS = Pattern .compile(".\*(\[0-9\]{2}(,\[0-9\])? HORAS SEMANAIS|DEDICACAO EXCLUSIVA).\*"); \[/code\]

_Não esquente com a regex, já que não é o foco desse post. Foi criada aos poucos, analisando os dados do arquivo e verificando o resultado._

Precisamos aplicar a regex a cada linha do arquivo. Para isso utilizaremos o método `Stream::map`, que aplica a cada item do stream um _lambda_ passado como parâmetro, retornando um novo stream com os novos itens.

Nosso lambda será responsável por aplicar a regex da constante `HORA`, utilizando um `java.util.regex.Matcher` para extrair a parte relevante da linha. Teremos o código:

\[code language="java"\] Stream<String> horasSemanais = linhas.map(linha -> { Matcher matcher = HORAS.matcher(linha); return matcher.matches() ? matcher.group(1) : ""; }); \[/code\]

A variável `horasSemanais` conterá um `Stream` somente com as informações de horas semanais de cada linha.

## Filtrando linhas vazias

Há um problema na nossa regex: ela deixa passar uma linha que fica vazia. Vamos filtrar o stream `horasSemanais`, deixando apenas as linhas não vazias.

Para isso, podemos utilizar o método `Stream::filter`, que recebe como parâmetro um lambda. Permanecem apenas os itens para os quais o lambda retornou true, em um novo stream.

Utilizaremos um lambda que chama o método `String::isEmpty`, negando o resultado:

\[code language="java"\] Stream<String> horasSemanaisNaoVazias = horasSemanais .filter(horaSemanal -> !horaSemanal.isEmpty()); \[/code\]

Perceba que a sintaxe desse lambda ficou bem elegante!

Agora temos só as horas semanais, sem linhas vazias.

Se utilizarmos `horasSemanaisNaoVazias.forEach(System.out::println)`, teríamos apenas as informações de horas semanais dos milhares de linhas: \[code\] DEDICACAO EXCLUSIVA DEDICACAO EXCLUSIVA 40 HORAS SEMANAIS 40 HORAS SEMANAIS DEDICACAO EXCLUSIVA 40 HORAS SEMANAIS 20 HORAS SEMANAIS 40 HORAS SEMANAIS DEDICACAO EXCLUSIVA 20 HORAS SEMANAIS 40 HORAS SEMANAIS 32,5 HORAS SEMANAIS 32,5 HORAS SEMANAIS 40 HORAS SEMANAIS ... \[/code\]

## Agrupando os valores

Estamos quase lá!

Na verdade, precisamos agrupar os dados obtidos no passo anterior, contando quantas ocorrências existem de cada tipo.

Fazer isso parece complicado, né? Mas com o stream do Java 8, podemos usar o método `Stream::collect`, passando alguma implementação da interface funcional `java.util.stream.Collector`.

Na classe `java.util.stream.Collectors` (no plural), há uma implementação de `Collector` que faz agrupamentos. Exatamente o que a gente precisa! Para obtê-la, devemos chamar o método `Collectors::groupingBy`. Devemos passar dois lambdas, um para montar as chaves e outro para montar os valores do `java.util.Map` que será retornado pelo agrupador.

No nosso caso, o lambda que monta as chaves deve retornar a própria linha que está sendo agrupada. Poderíamos representar isso com o lambda `s -> s`. Mas já existe um lambda pronto que simplesmente retorna o que recebe: o `Function.identity()`.

Já o lambda que monta os valores deve contar as ocorrências de linhas iguais. Também existe um lambda pronto que faz contagens: o `Collectors.counting()`.

Juntando tudo, teríamos:

\[code language="java"\] Map<String, Long> horasSemanaisAgrupadas = horasSemanaisNaoVazias .collect(Collectors.groupingBy(Function.identity(), Collectors.counting())); \[/code\]

Pronto! Ao executarmos `System.out.println(horasSemanaisAgrupadas)`, teríamos: \[code\] {40 HORAS SEMANAIS=571777, 60 HORAS SEMANAIS=9325, 25 HORAS SEMANAIS=1052, 44 HORAS SEMANAIS=7434, 12 HORAS SEMANAIS=69, 30 HORAS SEMANAIS=5509, DEDICACAO EXCLUSIVA=115359, 32,5 HORAS SEMANAIS=2, 24 HORAS SEMANAIS=2216, 20 HORAS SEMANAIS=23069, 66 HORAS SEMANAIS=308, 36 HORAS SEMANAIS=1538} \[/code\]

## Ordenando os resultados

Conseguimos agrupar os resultados! Bacana!

Mas a impressão não ficou lá essas coisas. Seria melhor que ordenássemos as chaves do `Map`, ou seja, os tipos de horas semanais.

Uma maneira fácil de fazer isso é ordenar todas as linhas antes de agrupá-las, usando o método `Stream::sorted`: \[code language="java"\] Stream<String> horasSemanaisOrdenadas = horasSemanaisNaoVazias.sorted(); Map<String, Long> horasSemanaisAgrupadas = horasSemanaisOrdenadas .collect(Collectors.groupingBy(Function.identity(), Collectors.counting())); \[/code\]

Se imprimirmos novamente a variável `horasSemanaisAgrupadas`, teremos:

\[code\] {44 HORAS SEMANAIS=7434, 12 HORAS SEMANAIS=69, DEDICACAO EXCLUSIVA=115359, 40 HORAS SEMANAIS=571777, 60 HORAS SEMANAIS=9325, 25 HORAS SEMANAIS=1052, 20 HORAS SEMANAIS=23069, 66 HORAS SEMANAIS=308, 36 HORAS SEMANAIS=1538, 30 HORAS SEMANAIS=5509, 32,5 HORAS SEMANAIS=2, 24 HORAS SEMANAIS=2216} \[/code\]

Há **algo de estranho**... Apesar de termos ordenado o stream, o resultado impresso não está ordenado.

Acontece que o `Collectors::groupingBy` cria um `java.util.HashMap` por padrão, que não garante que a ordem de inserção será mantida. Para manter a ordem do `Map`, podemos usar um `java.util.LinkedHashMap`.

Há uma versão do método `Collectors::groupingBy` em que o segundo parâmetro é um lambda que cria o `Map` a ser utilizado no agrupamento. Passaremos o construtor padrão de `LinkedHashMap` com o method reference `LinkedHashMap::new`.

Teremos, então, com o seguinte código:

\[code language="java"\] Map<String, Long> horasSemanaisAgrupadas = horasSemanaisOrdenadas .collect(Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting())); \[/code\]

Agora, ao imprimirmos o valor da variável `horasSemanaisAgrupadas`, os valores ficariam ordenados corretamente:

\[code\] {12 HORAS SEMANAIS=69, 20 HORAS SEMANAIS=23069, 24 HORAS SEMANAIS=2216, 25 HORAS SEMANAIS=1052, 30 HORAS SEMANAIS=5509, 32,5 HORAS SEMANAIS=2, 36 HORAS SEMANAIS=1538, 40 HORAS SEMANAIS=571777, 44 HORAS SEMANAIS=7434, 60 HORAS SEMANAIS=9325, 66 HORAS SEMANAIS=308, DEDICACAO EXCLUSIVA=115359} \[/code\]

## Caprichando na impressão

Ótimo! Mas podemos melhorar um pouquinho a impressão do resultado.

Seria interessante imprimir primeiro a contagem e depois o tipo de horas semanais, formatando com tabs.

Para isso, podemos utilizar o método default `forEach`, da interface `Map`, passando um lambda que imprime da maneira que achamos melhor:

\[code language="java"\] horasSemanaisAgrupadas.forEach((k,v) -> System.out.println(v+"\\t"+k)); \[/code\]

Finalmente, teríamos:

\[code\] 69 12 HORAS SEMANAIS 23069 20 HORAS SEMANAIS 2216 24 HORAS SEMANAIS 1052 25 HORAS SEMANAIS 5509 30 HORAS SEMANAIS 2 32,5 HORAS SEMANAIS 1538 36 HORAS SEMANAIS 571777 40 HORAS SEMANAIS 7434 44 HORAS SEMANAIS 9325 60 HORAS SEMANAIS 308 66 HORAS SEMANAIS 115359 DEDICACAO EXCLUSIVA \[/code\]

## Tudo isso rápido pra caramba

Juntando tudo e omitindo variáveis intermediárias, teríamos a seguinte classe:

\[code language="java"\] public class CargaHorariaServidores {

private static final Pattern HORAS = Pattern .compile(".\*(\[0-9\]{2}(,\[0-9\])? HORAS SEMANAIS|DEDICACAO EXCLUSIVA).\*");

public static void main(String\[\] args) throws IOException {

Path caminho = Paths.get(System.getProperty("user.home"), "Downloads/201402\_Servidores/20140228\_Cadastro.csv");

Files.lines(caminho, StandardCharsets.ISO\_8859\_1) .map(linha -> { Matcher matcher = HORAS.matcher(linha); return matcher.matches() ? matcher.group(1) : ""; }) .filter(horaSemanal -> !horaSemanal.isEmpty()) .sorted() .collect( Collectors.groupingBy(Function.identity(), LinkedHashMap::new, Collectors.counting())) .forEach((k, v) -> System.out.println(v + "\\t" + k)); } } \[/code\]

Ao executar o código anterior na minha máquina (core i5, 6GB RAM, HDD), o arquivo de mais de 730 mil linhas é processado em menos de 8 segundos.

Fiz a [mesma coisa](https://gist.github.com/alexandreaquiles/10609788) com os comandos `grep`, `sort` e `uniq` do Unix, comparando os tempos de execução.

O Java foi 12x mais rápido. Surpreendente, não?

Como é tão rápido? Um dos motivos é que streams são _lazy_: não fazem nada, só preparam uma computação pra ser feita posteriormente. O `collect` é a operação _terminal_, que puxa a computação das linhas.
