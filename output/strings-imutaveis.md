---
title: "Strings (i)mutáveis?"
date: "2007-05-21"
author: "trusso"
authorEmail: "thadeu.russo@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Todos sabemos que, em Java, [Strings](http://java.sun.com/j2se/1.5.0/docs/api/java/lang/String.html) são objetos **imutáveis**. Sendo assim, qualquer chamada de método em uma referência para String **NÃO** irá alterar o valor da referenciada. Uma sugestão aqui é uma pesquisa sobre o pattern [Flyweight](http://en.wikipedia.org/wiki/Flyweight_pattern) que é utilizado nesta classe e justifica tal imutabilidade.

Exemplo:

\[java\] public class Teste {

public static void main(String \[\] args) throws Exception { String nome = "Thadeu de Russo e Carmo"; nome.toUpperCase(); System.out.println(nome); } } \[/java\]

O output será `Thadeu de Russo e Carmo`.

Nos gabamos para nossos colegas quem estão no início de carreira em Java sobre tal conhecimento, que é cobrado inclusive na prova de certificação de programador. Poderiamos também criar uma função que "executasse" algumas alterações.

Algo como:

\[java\] public class Teste {

public static void main(String \[\] args) throws Exception { String nome = "Thadeu de Russo e Carmo"; alteraString(nome); System.out.println(nome); } } \[/java\]

O output será `Thadeu de Russo e Carmo`.

Pelo que conhecemos, independentemente do conteúdo da função `alteraString(String)`, **nunca** conseguiríamos alterar o conteúdo referenciado por `nome`, certo? **Errado!!!!**

Utilizando os recursos da [API de Reflection](http://java.sun.com/j2se/1.5.0/docs/api/java/lang/reflect/package-summary.html), conseguimos facilmente realizar tal alteração. Mãos a obra!

\[java\] import java.lang.reflect.Field;

public class Teste {

public static void main(String \[\] args) throws Exception { String nome = "Thadeu de Russo e Carmo"; alteraString(nome); System.out.println(nome); } public static void alteraString(String nome) throws Exception { Field value = String.class.getDeclaredField("value"); //1 value.setAccessible(true); //2 char \[\] charsDaString = (char \[\]) value.get(nome); // 3 charsDaString\[0\] = 't'; charsDaString\[1\] = 'H'; charsDaString\[2\] = 'A'; charsDaString\[3\] = 'D'; charsDaString\[4\] = 'E'; } } \[/java\]

Uow!!!! Mágica? Vamos ao que esta acontecendo nas linhas numeradas.

linha 1 - Estamos pegando uma referencia para um objeto do tipo [java.lang.reflect.Field](http://java.sun.com/j2se/1.5.0/docs/api/java/lang/reflect/Field.html) para o atributo "value" da classe String. linha 2 - Este é um campo privado (basta olhar o fonte da classe String) e para conseguirmos acesso a ele, temos que "nos dar acesso". (Olha o encapsulamento indo para o buraco!!) linha 3 - Na instancia que eu tenho (nome), eu desejo pegar o field value (que é o array onde os caracteres são armazenados).

Nas demais linhas nós simplesmente trocamos os valores dos caracteres. O que será que teremos impresso? Ta-da!! `"tHADEu de Russo e Carmo"`. Opa, algo diferente do que haviamos afirmado mais acima. Vale afirmar que fazer tal modificação em uma `String` é uma péssima idéia, dado o pool de `String`s: alguém pode estar compartilhando essa `String` com você, e enxergará essa alteração sem ter previsto tal acontecimento!

O que fizemos no código acima foi, simplemente brincar com a API de Reflection. API esta que permite coisas como injeção de dependência como no [EJB3](http://java.sun.com/products/ejb/), [vRaptor](http://www.vraptor.org), [JBoss Seam](http://www.jboss.com/products/seam), etc. Aqueles que já trabalharam com os velhos applets devem estar pensando: "Achei uma falha de segurança no Java!". Isso não é verdade pois dentro do método `setAccessible(boolean)` da classe `Field`, existe uma validação junto ao [SecurityManager](http://java.sun.com/j2se/1.5.0/docs/api/java/lang/SecurityManager.html).

Vale atentar ao fato de que é possível alterar tais propriedades no arquivo java.policy que fica sob a pasta `%JAVA_HOME%\jre\lib\security`.

Finalizando, aqueles que não conheciam ou não sabiam o poder da API de Reflection, viram que com um simples código, conseguimos "mutar" uma `String`, imaginem com um pouco mais de código o que não é possível de se fazer.
