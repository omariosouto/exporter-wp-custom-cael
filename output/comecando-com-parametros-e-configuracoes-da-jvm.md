---
title: "Começando com parâmetros e configurações da JVM"
date: "2010-09-09"
author: "lucasas"
authorEmail: "lucasas@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Quando rodamos nossas aplicações na JVM não sabemos o que acontece internamente dentro dela. Questões como o [Garbage Collector](http://www.oracle.com/technetwork/java/gc-tuning-5-138395.html), [JIT](http://java.sun.com/developer/onlineTraining/Programming/JDCBook/perf2.html#jit) e alocação de memória heap passam desapercebidas por um bom motivo: não devemos nos preocupar (muito) com elas.

Mesmo sem ter um controle direto sobre as diferentes JVMs existentes, muitas vezes precisamos customizar e fazer ajustes finos de seus comportamentos, em especial quando estamos testando a aplicação ou percebemos fraquezas do sistema em produção. Dentre as configurações mais comuns, temos o manjado parâmetro `-Xmx` para indicar a quantidade máxima de memória que a JVM pode alocar para o heap (ex `-Xmx1024m`). É curioso saber que essa, assim como todas as que começam com `-X`, [é uma configuração fora do padrão das JVMs](http://download.oracle.com/javase/1.5.0/docs/tooldocs/solaris/java.html), e pode não existir, apesar de que na prática todas a tem: seja a [JRockit](http://www.oracle.com/technetwork/middleware/jrockit/overview/index.html), a Sun/Oracle, entre outras.

Vamos ver outras importantes configurações e parâmetros importantes para testes e customizações do dia a dia na JVM da Sun/Oracle. Todas as opções relatadas aqui também funcionam na JVM do Mac e na JRockit.

**Desabilitando chamadas explícitas ao Garbage Collector**

Alguns desenvolvedores costumam querer intervir na participação do GC e explicitamente fazem invocações a ele: o `System.gc();`. Invocar o GC manualmente não é uma boa idéia, primeiro porque não é garantido que o mesmo rodará no momento que você o solicitou (por padrão a JVM da Oracle/Sun obedece, mas a JRockit não), segundo porque uma chamada como esta pode fazer com que a JVM faça um "_full sweep_" da memória heap, ou seja, tudo ficará congelado durante esse tempo ("_stop-the-world_"), mesmo que isso não fosse necessário.

Não podemos evitar que algum desenvolvedor tente invocar de maneira manual o GC, mas podemos fazer com que a JVM ignore esta invocação. Basta no momento em que formos chamar a JVM, passemos o parâmetro `-XX:+DisableExplicitGC`. Deste modo a invocação direta é sempre ignorada.

**Verificando as chamadas ao GC**

Observando como o GC se comporta em nossa aplicação podemos concluir alguns possíveis problemas de performance na mesma. Vamos pegar como exemplo abaixo:

\[java\] for (int i = 0; i < 100; i++) { List<Object> lista = new ArrayList<Object>(); for (int j = 0; j < 300000; j++) { lista.add(new Object()); } } \[/java\]

Habilitando a opção **\-verbose:gc** no momento de rodar este simples código dentro de um `main`, pode-se notar que o Full GC está sendo executado várias vezes, contrariando a teoria das gerações. Os objetos estão demorando para serem liberados, o que acaba causando várias chamadas ao Full GC para liberar memória. Poderíamos alterar o programa para resolver este problema ou alterar algumas configurações na JVM.

[![Divisão da memória na JVM da Sun/Oracle não é tão simples](http://java.sun.com/docs/hotspot/gc5.0/fig3.gif "Divisão da memória da JVM da Sun/Oracle")](https://blog.caelum.com.br/wp-content/uploads/2010/08/dois-dois.png)

A divisão de memória da JVM da Sun/Oracle não é tão simples assim. [Veja mais sobre esta divisão](http://www.oracle.com/technetwork/java/gc-tuning-5-138395.html#1.1.%20Generations|outline).

Para resolver este problema precisamos entender melhor o [funcionamento interno da memória da JVM](http://www.oracle.com/technetwork/java/gc-tuning-5-138395.html#1.1.%20Generations|outline) que divide os objetos de acordo com o seu tempo de vida. [Configurando alguns parâmetros](http://docs.sun.com/source/819-0084/pt_tuningjava.html) na JVM teríamos menos chamadas ao Full GC e com isso os Minors GC serão bem mais performáticos.

**Performance**

Podemos habilitar a flag **\-Xprof** para obtermos mais informações sobre o processamento da nossa aplicação, chamadas de métodos e em quais objetos estes métodos foram chamados. Esta flag não substitui um profiler profissional como JProfiler ou o TestKit, porém, pode nos trazer informações úteis sobre como encontra-se nossa aplicação durante a sua execução.

Durante a construção de determinados algoritmos surge a necessidade de fazermos invocações recursivas, ou mesmo percorrer muitos métodos. Cada thread possui a sua própria pilha de execução, sendo que esta pilha possui um tamanho máximo (`stack size`), que dependendo da quantidade de invocações e uso de variáveis locais pode causar o conhecido [java.lang.StackOverflowError](http://download.oracle.com/javase/1.4.2/docs/api/java/lang/StackOverflowError.html). Podemos aumentar o tamanho da pilha de execução ajustando a flag **\-Xss** (por exemplo, `-Xss1024k`).

**Mais sobre -verbose**

O `-verbose` pode ser usado além da verificação do comportamento do GC. Problemas de Class Loader Hell podem ser resolvidos se você descobrir de onde a classe que você está utilizando está sendo carregada por algum `ClassLoader`. Basta adicionar a opção **\-verbose:class** no momento de rodar sua aplicação e será exibido no console qual a localização das classes carregadas.

**Outras opções interessantes** A memória da JVM possui uma parte conhecida como **permanent generation**, ou **PermGen.** Este espaço fica fora do heap e contém objetos internos da JVM, além de objetos do tipo **Class, Method, Field** e o **Pool de Strings.**

O erro mais comum referente ao PermGen é o [java.lang.OutOfMemoryError: PermGen space](http://download.oracle.com/javase/1.4.2/docs/api/java/lang/OutOfMemoryError.html), que acaba confundindo o programador. A confusão ocorre porque são partes diferentes de memória, e o desenvolvedor muitas vezes pensa que o erro refere-se a memória heap e acaba tentando corrigí-lo com os parâmetros -Xms e -Xmx.

Para aumentar o tamanho do `PermGen` devemos utilizar o parâmetro **\-XX:MaxPermSize** (por exemplo, `-XX:MaxPermSize=128m`). Erros que acontecem e são referentes a esta parte da memória, são difíceis de serem identificados, justamente por não se tratarem de objetos que temos muito controle. Em geral acontecem devido a uma grande quantidade de classes carregadas na memória, e aparece frequentemente no Eclipse quando temos muitos plugins carregados.

**Um exemplo real: configurações de JVM no Jetty**

Em servlet containers, como o Jetty, existem arquivos onde podemos configurar alguns parâmetros para a JVM, sendo alguns deles sugeridos. Vamos ver algumas das configurações sugeridas/utilizadas por eles:

\[code\] # -Xmx2000m - Tamanho máximo da memória heap # -Xmn512m - Tamanho na memória heap para a young generation # -verbose:gc - Observar comportamentos do GC # -XX:+DisableExplicitGC - Desabilitar chamadas explícitas ao GC

\# -XX:+PrintGCDateStamps - Imprime as datas das chamadas ao GC # -XX:+PrintGCTimeStamps- Imprime o timestamp das chamadas ao GC # -XX:+PrintGCDetails - Imprime detalhes das chamadas ao GC # -XX:+PrintCommandLineFlags - Imprime as flags que foram passadas na linha de comando \[/code\]

**Configurações extras do GC**

Como se pode ver, grande parte das configurações extras que são frequentemente utilizadas, tem relação com o funcionamento do garbage collector. Devemos nos atentar a detalhes referentes ao GC para melhorar a performance da nossa aplicação.

`-XX:+UseParallelGC` - Utilizar uma versão do GC que é executada em paralelo no momento de coletar objetos da _**young generation**_

`-XX:+UseConcMarkSweepGC` - Habilitar um GC que coleta os objetos _**tenured**_ concorrentemente com a execução do sistema. O GC é executado durante um curto período afim de evitar que a aplicação seja parada por muito tempo.

Para habilitar o GC paralelo da young generation com o GC concorrente inicia o JVM com a flag **`-XX:+UseParNewGC`**. Imporante! Não use a flag `-XX:+UseParallelGC` juntamente com esta flag. Mais detalhes sobre melhorias no GC podem ser encontradas [neste artigo](http://www.petefreitag.com/articles/gctuning/).

**Conclusão**

Obviamente estas flags não podem ser usadas sem critério, mas conhecê-las podem nos ajudar a melhorarmos como desenvolvedores Java. [Existem realmente muitas opções da JVM](http://www.md.pp.ru/~eu/jdk6options.html), e frequentemente ajustes finos podem mudar bruscamente a performance e escalabilidade da sua aplicação.
