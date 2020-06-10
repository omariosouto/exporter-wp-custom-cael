---
title: "Erros com JAXB no Java 9+"
date: "2018-03-12"
author: "alexandre.aquiles"
authorEmail: "alexandre.aquiles@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Vamos dizer que temos a seguinte classe, que lê o arquivo `livro.xml` transformando-o num objeto da classe `Livro`:

```java

import javax.xml.bind.JAXBException;
//outros imports...
public class TesteUnmarshal {
  public static void main(String[] args) throws JAXBException {
    JAXBContext context = JAXBContext.newInstance(Livro.class);
    Unmarshaller unmarshaller = context.createUnmarshaller();
    Livro livro = (Livro) unmarshaller.unmarshal(new File("livro.xml"));
    System.out.println(livro.getTitulo());
  }
}
```

O código anterior usa a API JAXB, responsável por transformar objetos Java em XML e vice-versa. Foi lançada em 2006, como parte da especificação Java EE 5. A partir do Java SE 6, de 2009, já vem embutida na JRE.

Com o Java 8, _sem nenhum JAR adicional_, a classe seria compilada com sucesso. Ao executá-la com o Java 8, o nome do livro seria exibido na saída padrão.

Tudo funciona perfeitamente.

## JAXB e erros com Java 9+

Porém, se tentássemos rodar a classe compilada anteriormente com o Java 9, teríamos uma exceção:

```

Error: Unable to initialize main class TesteUnmarshal
Caused by: java.lang.NoClassDefFoundError: javax/xml/bind/JAXBException
```

Se compilássemos com o Java 9, teríamos o seguinte erro de compilação:

```

TesteUnmarshal.java:1: error: package javax.xml.bind is not visible
import javax.xml.bind.JAXBContext;
                ^
  (package javax.xml.bind is declared in module java.xml.bind,
   which is not in the module graph)
```

> Os mesmos erros acontecem com o Java 10 e continuarão acontecendo em versões posteriores.

### Exceções semelhantes

Basta executar uma aplicação razoavelmente simples após atualizar para o Java 9 (ou 10) que é bem possível que obtenhamos algumas dessas exceções:

- Ao usar o Hibernate e/ou Hibernate Validator, a própria `java.lang.ClassNotFoundException: javax.xml.bind.JAXBException`
- Ao usar o Jersey, a `java.lang.ClassNotFoundException: javax.xml.bind.Unmarshaller`
- Em uma aplicação Spring Boot, a `java.lang.ClassNotFoundException: javax.xml.bind.ValidationException`

No fórum da Alura, por exemplo, são [erros relativamente comuns](https://cursos.alura.com.br/forum/search/1?term=java.lang.NoClassDefFoundError%3A+javax%2Fxml%2Fbind%2FJAXBException).

Parece, então, que o JAXB foi removido a partir do Java 9. Mas não é bem assim...

## Java 9+ e o Class Path

Uma das principais [novidades do Java 9](https://blog.caelum.com.br/o-minimo-que-voce-deve-saber-de-java-9/) foi o Java Platform Module System (JPMS), um sistema de módulos que resolve vários problemas do Classpath e permite um nível de encapsulamento acima dos pacotes.

A própria JDK foi dividida em módulos. O módulo `java.base` contém os pacotes mais básicos como o `java.lang`, `java.util`, `java.io` e `java.time`. Há ainda módulos para as diversas APIs do Java: JDBC (`java.sql`), JNDI (`java.naming`), Swing (`java.desktop`), JMX (`java.management`), entre outros.

Toda aplicação modularizada só tem disponíveis, por padrão, as classes do módulo `java.base`. Se for necessário usar o JDBC ou Swing, por exemplo, os módulos correspondentes devem ser declarados explicitamente como dependências no arquivo `module-info.java`. Os JARs de bibliotecas devem colocados no Module Path.

Para permitir uma migração mais suave, ainda é possível usar o velho Class Path com o JPMS. Dessa maneira, é possível executar classes compiladas com o Java 8 (ou anterior) e desenvolver uma aplicação não modularizada.

> O conteúdo do Class Path no JPMS também é chamado de _unnamed module_, o "módulo sem nome".

Quando usamos o Class Path com a JDK modularizada do Java 9 em diante, quais classes ficam disponíveis? Se fossem só as do módulo `java.base`, muitas aplicações não seriam executadas corretamente.

Por isso, foi criado o módulo agregador `java.se` que deixa disponível no Class Path o conteúdo dos módulos `java.base`, `java.sql`, `java.desktop`, `java.management` e uma série de outros.

A ideia é que o `java.se` contenha a maioria das bibliotecas disponíveis em versões anteriores do Java. Mas não o JAXB...

## JAXB deprecated no Java 9

A [JEP 320](http://openjdk.java.net/jeps/320) pretende remover da JRE APIs como JAXB, JAX-WS, JTA, JAF, CORBA e algumas outras.

Segundo a proposta, são APIs do Java EE que possuem implementações de terceiros e podem ser usadas como bibliotecas. Por isso, não precisariam estar na JRE.

A remoção deve ser efetivada no Java 11, a ser lançado em setembro de 2018.

Enquanto essa remoção não acontece, módulos para o JAXB (`java.xml.bind`), JAX-WS (`java.xml.ws`), JTA (`java.transaction`), JAF (`java.activation`), CORBA (`java.corba`) e alguns outros foram incorporados ao Java 9. Todos esses módulos foram anotados com `@Deprecated`.

Os módulos do Java EE não fazem parte do módulo agregador `java.se` e, por isso, não ficam disponíveis no Class Path do Java 9, nem ficarão em versões posteriores do Java. Foi criado um outro módulo agregador, o `java.se.ee`, que junta o `java.se` a esses módulos do Java EE, mas que não é disponibilizado por padrão.

Como usar esses módulos do Java EE para que a classe `TesteUnmarshal` seja compilada e executada com sucesso?

## Uma solução temporária

É possível usar a opção `--add-modules` para adicionarmos módulos ao Class Path.

### Com Java

Poderíamos adicionar ao Class Path o módulo `java.xml.bind`, do JAXB, durante a compilação da seguinte maneira:

```

javac --add-modules java.xml.bind TesteUnmarshal.java
```

Executaríamos de maneira parecida:

```

java --add-modules java.xml.bind TesteUnmarshal
```

Se quisermos disponibilizar todas as APIs do Java EE que estão _deprecated_, basta adicionar o módulo `java.se.ee`.

### Com Maven

Se estivermos usando o Maven no _build_ de nosso projeto, é necessário configurarmos um argumento do compilador:

```xml

<plugin>
  <groupid>org.apache.maven.plugins</groupid>
  <artifactid>maven-compiler-plugin</artifactid>
  <version>3.7.0</version>
  <configuration>
    <compilerargs>
      <arg>--add-modules</arg>
      <arg>java.xml.bind</arg>
    </compilerargs>
  </configuration>
</plugin>
```

### Com Tomcat e Jetty

Se tivermos uma aplicação rodando no Tomcat e estivermos obtendo alguns dos erros mencionados acima com o Java 9, podemos adicionar módulos sem mudarmos nem a aplicação nem configurações do próprio Tomcat: é só definirmos a variável de ambiente `JAVA_OPTS`. No Linux, seria algo como:

```

export JAVA_OPTS="--add-modules java.xml.bind"
```

O Jetty tem uma variável de ambiente semelhante, chamada `JAVA_OPTIONS`:

```

export JAVA_OPTIONS="--add-modules java.xml.bind"
```

## A solução definitiva

A [JEP 320](http://openjdk.java.net/jeps/320), que propõe a remoção do JAXB, JAX-WS, JTA e outros da JRE, recomenda que as aplicações incluam no Class Path os JARs dessas APIs e alguma implementação.

Se estivermos usando o Maven, basta adicionarmos as seguintes dependências:

```xml

<dependency>
  <groupid>javax.xml.bind</groupid>
  <artifactid>jaxb-api</artifactid>
  <version>2.2.11</version>
</dependency>
<dependency>
  <groupid>com.sun.xml.bind</groupid>
  <artifactid>jaxb-core</artifactid>
  <version>2.2.11</version>
</dependency>
<dependency>
  <groupid>com.sun.xml.bind</groupid>
  <artifactid>jaxb-impl</artifactid>
  <version>2.2.11</version>
</dependency>
```

Com o JAR incorporado às dependências do nosso projeto, teremos uma implementação do JAXB no Class Path. Dessa maneira, nosso código estaria preparado para a remoção da API no Java 11!

foto por [Paul Wilkinson](https://www.flickr.com/photos/eepaul/7396791752/)
