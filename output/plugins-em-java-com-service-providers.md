---
title: "Plugins em Java com Service Providers"
date: "2018-03-28"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

## Cotuba

O Cotuba é uma aplicação (_fictícia_) de linha de comando (CLI) implementada em Java que transforma arquivos Markdown (.md) em ebooks nos formatos PDF ou EPUB.

![Arquivo .md vai para o cotuba que transforma em .pdf e .epub](https://blog.caelum.com.br/wp-content/uploads/2019/02/md-cotuba-pdf_epub.png)

Para usar o Cotuba, é preciso ter o `cotuba.jar` e os JARs de suas dependências no _Classpath_ e invocar a classe `cotuba.Main`:

```

java -cp "libs/*" cotuba.Main -f epub
```

Com a opção `-cp` do `java`, definimos que o Classpath terá todos os JARs do diretório `libs`.

Já a opção `-f` do Cotuba permite definir o formato `epub` ou `pdf` (o padrão).

Seria impressa a seguinte mensagem:

```

Arquivo gerado com sucesso: book.epub
```

O arquivo EPUB gerado teria o conteúdo a seguir:

![EPUB gerado](https://blog.caelum.com.br/wp-content/uploads/2019/02/epub-sem-estilo.png)

Olhando um pouquinho mais de perto, o Cotuba faz o seguinte:  
\- pega parâmetros do usuário  
\- lê cada `.md`, faz o parse e os renderiza para HTML  
\- gera o PDF ou EPUB, de acordo com os parâmetros

Cada arquivo `.md` é considerado um capítulo diferente. O título do capítulo é extraído do maior _heading_: o `#` no Markdown.

## A necessidade de plugins

A empresa Paradizo quer definir um tema que modifica o estilo do ebook gerado pelo Cotuba.

Poderíamos pedir que o time da Paradizo nos mandasse um JAR. No código do Cotuba, invocaríamos o código da Paradizo.

Mas e se tivéssemos outros usuários que querem diferentes temas? Ou, talvez, modificações no texto?

A cada capítulo renderizado de Markdown para HTML, queremos dar a chance de terceiros inserirem seu próprio CSS. Também queremos que terceiros possam executar seu próprio código assim que a geração do ebook acabar.

Fazer com que o Cotuba chame código de terceiros é inviável! Seriam classes que estariam em outros JARs, fora do `cotuba.jar`. Não sabemos quais são esses outros JARs nem o nome das classes e métodos que precisamos chamar. Não podemos depender delas!

![Cotuba chamando o Tema Paradizo não é uma boa ideia](https://blog.caelum.com.br/wp-content/uploads/2019/02/cotuba-chamando-tema_paradizo.png)

Precisamos de pontos de extensão para o Cotuba. Como implementá-los?

## Pontos de extensão por meio de interfaces

O Cotuba não pode depender das classes de terceiros, mas essas podem depender do Cotuba. Podemos [inverter as dependências](https://en.wikipedia.org/wiki/Dependency_inversion_principle)!

Para isso, precisamos fornecer uma interface com um método que será chamado quando um capítulo acabou de ser renderizado para HTML e outro quando a geração do ebook foi finalizada.

Mas e se um usuário quiser apenas tratar cada capítulo e não o ebook final? Ao invés de uma só abstração, podemos [segregar as interfaces](https://en.wikipedia.org/wiki/Interface_segregation_principle)!

Então, teríamos uma interface para a renderização de um capítulo:

```java

package cotuba.plugin;

public interface LogoAposRenderizarMDParaHTML {
  void trata(Capitulo capitulo);
}
```

E outra interface para quando o ebook acabar de ser gerado:

```java

package cotuba.plugin;

public interface AoFinalizarGeracao {
  void trata(Ebook ebook);
}
```

O Cotuba continuaria chamando métodos de terceiros em _runtime_. Porém, com essas interfaces, a dependência de código é invertida:

![Tema Paradizo dependendo das interfaces do Cotuba mas com Cotuba chamando o tema da Paradizo no runtime](https://blog.caelum.com.br/wp-content/uploads/2019/02/dependencia-invertida-tema_paradizo-cotuba-1.png)

## Um plugin de tema

Os desenvolvedores da Paradizo criaram uma implementação da interface `LogoAposRenderizarMDParaHTML` que usa a biblioteca JSoup para colocar um `style` no `head` do HTML do capítulo, definindo uma borda tracejada em volta do título:

```java

package br.com.paradizo.cotuba.tema;

//imports...

public class TemaParadizo implements LogoAposRenderizarMDParaHTML {

  public void trata(Capitulo capitulo) {
    System.out.println("Executando o tema Paradizo para o capítulo: " + capitulo.getTitulo());
    String html = capitulo.getConteudoHTML();
    Document doc = Jsoup.parse(html);
    doc.select("head").prepend("<style> h1 { border: 1px dashed black; } </style>");
    capitulo.setConteudoHTML(doc.html());
  }

}
```

Temos interfaces que fornecem pontos de extensão para o Cotuba. Temos uma implementação para o tema da Paradizo.

Mas como ligar uma coisa com a outra, sem fazer com que o Cotuba dependa do código da Paradizo?

Como fazer que as implementações dos pontos de extensão do Cotuba sejam aplicadas pela simples presença de seus JARs no Classpath?

## A ServiceLoader API

Existem algumas bibliotecas (e até um [padrão](https://www.osgi.org/) bastante robusto) que permitem montar uma arquitetura de plugins em Java. Mas, a partir do Java 6, você pode fazer isso apenas com o Java SE. É a _ServiceLoader API_.

Dois conceitos são importantes:  
\- Service Provider Interface (SPI): as interfaces que definem um serviço. No nosso caso, as interfaces `LogoAposRenderizarMDParaHTML` e `AoFinalizarGeracao`.  
\- Service Provider: uma implementação da SPI. No nosso caso, a classe `TemaParadizo`.

Esse mecanismo é utilizado pelo [carregamento de drivers JDBC](https://docs.oracle.com/javase/6/docs/api/java/sql/DriverManager.html) e a [configuração programática da Servlet 3.0](https://docs.oracle.com/javaee/6/api/javax/servlet/ServletContainerInitializer.html).

No projeto do tema da Paradizo, precisamos criar dentro da pasta `META-INF/services` um arquivo com o _fully qualified name_ da SPI que queremos implementar. No nosso caso, será `cotuba.plugin.LogoAposRenderizarMDParaHTML`:

```

tema-paradizo
└── META-INF
    └── services
        └── cotuba.plugin.LogoAposRenderizarMDParaHTML
```

> Se você estiver usando o Maven, a pasta `META-INF` deverá ficar em `src/main/resources`.

Dentro do arquivo, colocaremos o _fully qualified name_ do service provider. No nosso caso:

```

br.com.paradizo.cotuba.tema.TemaParadizo
```

## Carregando os Service Providers

No projeto do Cotuba, vamos carregar os service providers, ou seja, as implementações da SPI usando a classe `java.util.ServiceLoader`:

```java

ServiceLoader<LogoAposRenderizarMDParaHTML> loader = 
        ServiceLoader.load(LogoAposRenderizarMDParaHTML.class);
```

O método estático `load` vasculha o diretório `META-INF/services` dos JARs do Classpath e encontra as implementações da interface passada como parâmetro. O `ServiceLoader` retornado é um `Iterator` e, por isso, pode ser passado para um _for each_:

```java

for (LogoAposRenderizarMDParaHTML implementacao : loader) {
  implementacao.trata(capitulo);
}
```

> Se tivermos mais de uma implementação para uma mesma interface, todas são carregadas e seu código poderá invocar cada uma delas!  
> A ordem de carregamento das implementaçes não é garantida. Se quisermos definir uma ordem, é preciso criar algum mecanismo. Poderíamos colocar algum critério na interface e usá-lo num `sort`.

Mas aonde colocar esse código?

A partir do Java 8, podemos colocar esse código num método estático da própria interface. E ainda podemos usar [lambdas](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-8/):

```java

package cotuba.plugin;

import java.util.ServiceLoader;

import cotuba.domain.Capitulo;

public interface LogoAposRenderizarMDParaHTML {

  void trata(Capitulo capitulo);

  public static void rodaPara(Capitulo capitulo) {
    ServiceLoader
      .load(LogoAposRenderizarMDParaHTML.class)
      .forEach(i -> i.trata(capitulo));
  }
}
```

Tá, mas aonde devo chamar esse método estático? No caso do Cotuba, no momento em que o Markdown acabou de ser renderizado para HTML. Isso acontece na classe `RenderizadorDeMD`:

```java

public class RenderizadorDeMD {

  public List<Capitulo> renderizaParaHTML(Path diretorioDosMD) {
    List<Capitulo> capitulos = new ArrayList<>();
    obtemArquivosMD(diretorioDosMD)
      .forEach(arquivoMD -> {
        Capitulo capitulo = new Capitulo();
        String html = renderizaArquivoMDParaHTML(arquivoMD);
        capitulo.setConteudoHTML(html);
        capitulos.add(capitulo);
        LogoAposRenderizarMDParaHTML.rodaPara(capitulo); //inserido
      });
    return capitulos;
  }

  //demais métodos...
}
```

Com essa versão atualizada do Cotuba, o pessoal da Paradizo deve colocar tanto o `cotuba.jar` como o `tema-paradizo.jar` no Classpath e executar o comando:

```

java -cp "libs/*" cotuba.Main -f epub
```

Teremos impresso:

```

Executando o tema Paradizo para o capítulo: Aberturas de livros
Executando o tema Paradizo para o capítulo: Frases
Arquivo gerado com sucesso: book.epub
```

O tema, que coloca uma borda no título do capítulo, foi aplicado:

![Foto do EPUB gerado com o tema da Paradizo](https://blog.caelum.com.br/wp-content/uploads/2019/02/epub-com-estilo.png)

O código pode ser encontrado em:  
[https://github.com/alexandreaquiles/cotuba/tree/java8](https://github.com/alexandreaquiles/cotuba/tree/java8)

\* foto por [Hugh Nelson](https://www.flickr.com/photos/hugh_nelson/3270303306/in/photostream/)
