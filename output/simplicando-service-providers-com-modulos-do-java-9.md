---
title: "Simplificando Service Providers com módulos do Java 9+"
date: "2018-04-04"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Num [post anterior](https://blog.caelum.com.br/plugins-em-java-com-service-providers), vimos como usar Service Providers na implementação de um plugin de temas da empresa Paradizo para o Cotuba, uma aplicação que gera PDFs e EPUBs a partir de arquivos Markdown. Nesse post, veremos como simplificar a criação de Service Providers com a solução de modularização disponível Java 9 em diante.

## Modularizando com o JPMS

A partir do [Java 9](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-9/), podemos definir módulos usando o JPMS (Java Platform Module System).

No arquivo `module-info.java` do Cotuba, vamos descrever o módulo `cotuba`:

```java

module cotuba {
}
```

Um módulo permite a criação de encapsulamento um nível acima de pacotes ou classes. É possível escolher quais pacotes de um JAR serão expostos para outros módulos.

Vamos exportar os pacotes:  
\- `cotuba.plugin`, que contém as interfaces `LogoAposRenderizarMDParaHTML` e `AoFinalizarGeracao`  
\- `cotuba.domain`, que contém as classes `Ebook` e `Capitulo`, entre outras.

Para isso, devemos usar o `exports`:

```java

module cotuba {
  exports cotuba.plugin;
  exports cotuba.domain;
}
```

Usamos o `requires` para definir de quais outros módulos dependemos:

```java

module cotuba {
  exports cotuba.plugin;
  exports cotuba.domain;

  requires commons.cli;
  requires org.commonmark;
  requires epublib.core;
  requires html2pdf;
  //outras dependências...
}
```

No Cotuba, usamos as bibliotecas Commons CLI, CommonMark, Epublib e iText. Cada um desses tem suas próprias dependências.

A equipe da Paradizo também modularizou o tema:

```java

module tema.paradizo {
  requires cotuba;
  requires jsoup;
}
```

O tema da Paradizo depende da biblioteca JSoup e do módulo do Cotuba.

> No caso de dependermos de um JAR ainda não modularizado, é criado um módulo automaticamente, que expõe todos os pacotes.
> 
> O nome do módulo automático será definido pela configuração `Automatic-Module-Name` do arquivo `META-INF/MANIFEST.MF`, o que acontece na biblioteca CommonMark.
> 
> Caso esse arquivo não exista, o nome do próprio JAR será usado. É o caso para as demais bibliotecas.

Com o JPMS ainda podemos executar a aplicação com um Classpath, por motivos de retrocompatibilidade. Porém, não teríamos as vantagens da modularização.

Para executarmos de maneira modularizada, devemos usar a opção `--module-path`. Além disso, precisamos usar a opção `--module` para indicar qual é o módulo da classe `Main`:

```

java --module-path libs --module cotuba/cotuba.Main -f epub
```

O resultado do comando anterior é o seguinte erro:

```

Exception in thread "main" java.util.ServiceConfigurationError: 
  cotuba.plugin.LogoAposRenderizarMDParaHTML:
    module cotuba does not declare `uses`
  at java.base/java.util.ServiceLoader.fail(ServiceLoader.java:588)
  ...
  at cotuba/cotuba.plugin.LogoAposRenderizarMDParaHTML.rodaPara(LogoAposRenderizarMDParaHTML.java:13)
```

Quando usamos módulos do JPMS, precisamos mudar um pouco a maneira de definir os service providers e interfaces.

## Usando a ServiceLoader API em módulos do JPMS

Em módulos do JPMS, as Service Provider Interfaces (SPI) devem ser definidas com o `uses`.

No módulo `cotuba`, teremos:

```java

module cotuba {
  exports cotuba.plugin;
  exports cotuba.domain;

  requires org.commonmark;
  requires epublib.core;
  requires html2pdf;
  //outras dependências...

  uses cotuba.plugin.LogoAposRenderizarMDParaHTML; //inserido
  uses cotuba.plugin.AoFinalizarGeracao; //inserido
}
```

Já no código do tema da Paradizo, indicamos um service provider com `provides ... with`:

```java

module tema.paradizo {
  requires cotuba;
  requires jsoup;

  provides cotuba.plugin.LogoAposRenderizarMDParaHTML
      with br.com.paradizo.cotuba.tema.TemaParadizo; //inserido
}
```

Podemos excluir o arquivo `META-INF/services/cotuba.plugin.LogoAposRenderizarMDParaHTML`, já que um módulo do Java 9 ignora esse arquivo.

A definição de SPIs com `uses` e de service providers com `provides ... with` nos módulos do JPMS simplifica o uso da Service Loader API.

Executamos com o mesmo comando anterior:

```

java --module-path libs --module cotuba/cotuba.Main -f epub
```

O resultado será:

```

Executando o tema Paradizo para o capítulo: Aberturas de livros
Executando o tema Paradizo para o capítulo: Frases
Arquivo gerado com sucesso: book.epub
```

O EPUB foi gerado com sucesso e a borda do no título do capítulo foi aplicada.

![Foto do EPUB gerado com o tema da Paradizo](https://blog.caelum.com.br/wp-content/uploads/2019/02/epub-com-estilo.png)

O código pode ser encontrado em:  
[https://github.com/alexandreaquiles/cotuba/tree/java9](https://github.com/alexandreaquiles/cotuba/tree/java9)

## Plugins na sua arquitetura

Há um estilo arquitetural em que o _domain model_ é o núcleo da aplicação. Uma interface de linha comando seria um plugin. Uma aplicação Web seria outro plugin. A persistência em um banco de dados relacional, outro plugin. Persistência em um datastore NoSQL, mais um plugin. Integrações com outros sistemas, mais plugins.

Usando interfaces, invertemos as dependências para que todos dependam do domain model, que não depende de ninguém. As dependências sempre apontam na direção das regras de negócio, nunca ao contrário. Cada "mecanismo de entrega" seria um plugin diferente.

Teríamos uma melhor manutenibilidade, extensibilidade e testabilidade, mas ao custo de maior complexidade. Esse estilo arquitetural é detalhado na [arquitetura hexagonal](http://alistair.cockburn.us/Hexagonal+architecture) e na [clean architecture](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html).

No caso do Cotuba, o núcleo da aplicação poderia ficar no módulo `cotuba`. Extrairíamos a interface de linha de comando para o módulo `cotuba.cli`. A geração de PDFs para o módulo `cotuba.pdf` e de EPUBs para o módulo `cotuba.epub`. Definiríamos interfaces, criando contratos e invertendo as dependências entre esses módulos, sempre apontando na direção do módulo `cotuba`.

![Módulos cotuba.cli, cotuba.pdf e cotuba.epub dependem do módulo cotuba](https://blog.caelum.com.br/wp-content/uploads/2019/02/cotuba-hexagonal.png)

O desenvolvimento de cada módulo poderia ser feito por diferentes times. As interfaces dos plugins seriam muito importantes para a comunicação entre esses times.
