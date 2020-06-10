---
title: "Trabalhando com arquivos do Java IO ao NIO 2"
date: "2011-08-10"
author: "mario.amaral"
authorEmail: "mario.amaral@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Durante anos, trabalhar com arquivos em Java foi muito trabalhoso. Precisávamos conhecer e interagir com diversas classes do pacote `java.io` a fim de realizar tarefas simples, como ler um arquivo ou simplesmente copiá-los de uma pasta para outra. Alguns projetos open source sugiram para facilitar essas tarefas, como o [Commons-IO](http://commons.apache.org/io/ "Commons IO") da Apache.

Conforme a linguagem foi evoluindo, estas tarefas foram se tornando mais fáceis de serem realizadas sem a necessidade de bibliotecas externas, graças aos recursos incorporados na API padrão. Vamos acompanhar como foi parte dessa evolução, usando o exemplo de copiar uma árvore de diretórios.

A seguir, vemos a árvore de diretórios que iremos copiar:

[![](https://blog.caelum.com.br/wp-content/uploads/2011/08/diret%C3%B3rio-300x239.png "arvore de diretórios")](https://blog.caelum.com.br/wp-content/uploads/2011/08/diret%C3%B3rio.png)

É uma arvore de arquivos comum. Antes de copiar a árvore inteira, vamos ver o código para copiar um arquivo sozinho:

\[java\] // Arquivos que iremos copiar File origem = new File("/home/caelum/Java/vraptor.zip"); File destino = new File("/home/caelum/Java/vraptor-copia.zip");

// abrimos os streams para leitura/escrita FileInputStream fis = new FileInputStream(origem); FileOutputStream fos = new FileOutputStream(destino);

// Obtém os canais por onde lemos/escrevemos nos arquivos FileChannel inChannel = fis.getChannel(); FileChannel outChannel = fos.getChannel();

// copia todos o conteúdo do canal de entrada para o canal de saída outChannel.transferFrom(inChannel, 0, inChannel.size());

// fecha os streams/channels usados... \[/java\]

Cada `FileInputStream`/`FileOutputStream` recebe um objeto do tipo `File`, que representa o local do arquivo no disco. Após isso criamos canais por onde copiaremos o conteudo do arquivo de origem para o de destino. Usar `FileChannels` para realizar a cópia pode ser bem mais eficiente do que se lessemos/escrevessemos utilizando os antigos _streams_ diretamente. Essa API não-blocante foi adicionada ao Java 1.4, em 2001.

Para copiar a estrutura de diretórios é muito mais simples, basta chamar o método `mkdirs` da classe `File`, que cria o diretório e toda a árvore necessária:

\[java\] File novoDiretorio = new File("/home/caelum-copia/Java"); novoDiretorio.mkdirs(); //cria o diretório "caelum-copia" e o diretório "Java" dentro da pasta /home/ \[/java\]

Mas isso só cria o diretório vazio, sem nenhum arquivo dentro. Para copiar o conteúdo, primeiro precisamos saber o que há dentro dele, para isso usamos o método `File.listFiles`, que nos retorna um array com todas as entradas do diretório, tanto arquivos quanto subdiretórios:

\[java\] File raiz = new File("/home/mario/Documents/caelum/Java"); File\[\] files = raiz.listFiles();

for (File file : files) { // copia os arquivos / subdiretórios } \[/java\]

Precisamos repetir esta operação para cada subdiretório encontrado. A melhor solução neste caso é realizar uma invocação recursiva para copiar o conteúdo de cada subdiretório encontrado.

\[java\] public void copiarArquivos(File origem, File destino) { // verifica se estamos tentando copiar um diretório if (origem.isDirectory()) { File\[\] files = origem.listFiles();

for (File file : files) { // invocacao recursiva de cada item de dentro do diretorio copiaArquivos(file, destinoEquivalente); } } else { // copia o arquivo usando os streams/channels } } \[/java\]

Juntando tudo que vimos até aqui, temos um método de cópia de diretórios. Não é um código simples, pois além de usar recursão para realizar a cópia dos subdiretórios, ainda envolve várias diferentes classes na cópia dos arquivos. Também deveria ser refatorado para 2 ou 3 métodos.

Existem diversos outros problemas com as antigas classes do `java.io`. Podemos pegar como exemplo a classe `File`, que tem alguns dos métodos para manipulação de arquivos, mas a maioria desses métodos não lança exceptions quando falham, e podem funcionar diferentemente em cada sistema operacional.

Vamos tentar deletar um diretório que possui arquivos dentro. O código segue abaixo:

\[java\] File root = new File("/home/caelum"); boolean deletou = root.delete(); // retorna false... \[/java\]

Sabemos que o diretório não foi removido pelo retorno do método, mas é dificil saber o motivo, se é um problema de acesso, se o diretório que não está vazio, se não existe, etc., ficando para o programador a responsabilidade de descobrir o que aconteceu.

A partir do Java 7, foi introduzida uma nova API no pacote [java.nio.file](http://download.oracle.com/javase/7/docs/api/index.html?java/nio/file/package-summary.html "java.nio.file"), visando reduzir a complexidade das operações realizadas em arquivos. A nova classe `Files` possui diversos métodos utilitários para manipular arquivos, lançando exceptions quando ocorre algum erro na operação, possibilitando ao desenvolvedor entender de forma rápida o motivo da falha. Os métodos de `Files` utilizam a classe `Path`, uma nova interface utilizada para representar entradas de arquivos/diretórios no sistema operacional.

O código para deletar o diretório ficaria da seguinte maneira:

\[java\] Path rootPath = Paths.get("/home/caelum"); Files.delete(rootPath); // o que acontece aqui? \[/java\]

A classe `Paths` é uma fábrica de `Path`. O resultado desse código, é uma exception:

\[java\] Exception in thread "main" java.nio.file.DirectoryNotEmptyException: /home/caelum \[/java\]

Vamos alterar nosso método `copiaArquivos` para utilizar as novas classes do Java 7:

\[java\] public void copiarArquivos(Path origem, Path destino) throws IOException { // se é um diretório, tentamos criar. se já existir, não tem problema. if(Files.isDirectory(origem)){ Files.createDirectories(destino);

// listamos todas as entradas do diretório DirectoryStream<Path> entradas = Files.newDirectoryStream(origem);

for (Path entrada : entradas) { // para cada entrada, achamos o arquivo equivalente dentro de cada arvore Path novaOrigem = origem.resolve(entrada.getFileName()); Path novoDestino = destino.resolve(entrada.getFileName());

// invoca o metodo de maneira recursiva copiarArquivos(novaOrigem, novoDestino); } } else { // copiamos o arquivo Files.copy(origem, destino); } } \[/java\]

A estrutura do algoritmo é a mesma utilizada anteriormente, mas agora o número de classes envolvidas é muito menor. Para copiar um arquivo, apenas chamamos o método `Files.copy`, não precisando mais trabalhar com _streams_ e _channels_ nesses casos.

Ainda é trabalhoso percorrer um diretório inteiro dessa maneira, mas podemos utilizar a nova interface [`FileVisitor`](http://download.oracle.com/javase/7/docs/api/index.html?java/nio/file/FileVisitor.html "FileVisitor"), uma implementação do pattern [Visitor](http://en.wikipedia.org/wiki/Visitor_pattern "Visitor"), para percorrer toda a árvore de diretórios de maneira mais simples. Os métodos dessa interface são

- `preVisitDirectory` Chamado antes de entrar em um diretório

- `postVisitDirectory` Chamado depois de passar por todos arquivos/subdiretórios de um diretótio

- `visitFile` Chamado para cada arquivo

- `visitFileFailed` Chamado se acontecer alguma exception em algum dos outros métodos

Todos os métodos retornam `FileVisitResult`, um `enum` com os as opções para continuar ou não percorendo a árvore de diretórios.

Vamos então implementar um `FileVisitor` para copiar nosso diretório. Nem sempre precisamos de todos os métodos da interface; neste caso, podemos estender a classe `SimpleFileVisitor` e apenas sobreescrever os métodos que realmente necessitamos:

\[java\] public class CopiadorDeArquivos extends SimpleFileVisitor { private Path origem; private Path destino;

public CopiadorDeArquivos(Path origem, Path destino) { this.origem = origem; this.destino = destino; }

public FileVisitResult preVisitDirectory(Path dir, BasicFileAttributes attrs) throws IOException { copiaPath(dir); return FileVisitResult.CONTINUE; }

public FileVisitResult visitFile(Path file, BasicFileAttributes attrs) throws IOException { copiaPath(file); return FileVisitResult.CONTINUE; }

private void copiaPath(Path entrada) throws IOException { // encontra o caminho equivalente na árvore de cópia Path novoDiretorio = destino.resolve(origem.relativize(entrada)); Files.copy(entrada, novoDiretorio); } } \[/java\]

Na classe acima, usamos apenas dois métodos, o `visitFile`, chamado para copiar cada arquivo existente na árvore, e o `preVisitDirectory`, chamado para criar o diretório. Para executar a cópia, faríamos:

\[java\] Path origem = Paths.get("/home/caelum"); Path destino = Paths.get("/home/caelum-copia");

Files.walkFileTree(origem, new CopiadorDeArquivos(origem, destino)); \[/java\]

O método `walkFileTree` recebe o diretório que será percorido, e um `FileVisitor`, que irá passar por todos os arquivos.

Apesar de ter tornado alguma operações mais simples, essa nova API de arquivos possui mais métodos estáticos e a maioria recebe uma instância de `Path`, separando comportamento e dados. Talvez com o futuro Java 8, [as closures e defender methods](https://blog.caelum.com.br/trabalhando-com-closures-no-java-8/) tornarão a interface `Path` mais amigável, sem depender tanto de métodos estáticos.

Além das classes apresentadas, ainda tivemos outros acréscimos na API de NIO, como [leitura/escrita de streams de forma assíncrona](http://www.ibm.com/developerworks/java/library/j-nio2-1/index.html "leitura de streams de forma assíncrona"), serviços para [monitorar alterações em diretórios](http://download.oracle.com/javase/tutorial/essential/io/notification.html "monitorar alterações em diretórios") e métodos para ler [metadados de arquivos](http://download.oracle.com/javase/tutorial/essential/io/fileAttr.html "metadados de arquivos") de maneira simplificadas. Essas novidades são conhecidas como NIO2. Com o passar das versões, o Java traz cada vez mais para suas APIs internas ferramentas necessárias para o dia a dia.
