---
title: "Brincando com Generics: o BizarreGenericDao"
date: "2006-10-29"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Conversando com o Orseni Campos, ele me contou de uma sacada muito interessante que teve juntamente com seu colega Alexandre Bitencourt para resolver um clássico problema do [generics](http://java.sun.com/j2se/1.5/pdf/generics-tutorial.pdf): em tempo de execução você não consegue descobrir o tipo parametrizado que foi passado como argumento.

Em outras palavras, repare no código do `Dao` genérico que foi [discutido aqui](https://blog.caelum.com.br/2006/08/26/ei-como-e-o-seu-dao-ele-e-tao-abstraido-quanto-o-meu/) (estou pulando a interface por questão de simplicidade):

\[java\] public class Dao<T> { // ... public Dao(Session session, Class<T> persistentClass) { this.session = session; this.persistentClass = persistentClass; } } \[/java\]

Aí, quando vamos criar um novo dao genérico:

\[java\] Dao<Livro> dao = new Dao<Livro>(session, Livro.class); \[/java\] Parece estranho ter de passar a Class que representa o livro, se eu já estou passando essa informação através do tipo parametrizado. Seria interessante fazer algo como:

\[code lang="java" highlight="5"\] public class Dao<T> { // ... public Dao(Session session) { this.session = session; this.persistentClass = T.class; } } \[/code\]

Isso não compila, essa informação (sobre quem é `T` dentro de `Dao`) não é armazenada em um `.class`. O motivo é simples. Só existe uma classe `Dao`, não importa como ela foi instânciada. Em outras palavras:

\[java\] (new Dao<Autor>(session, Autor.class)).getClass() == (new Dao<Livro>(session, Livro.class)).getClass() \[/java\]

retorna `true`. Então o problema é o seguinte: existe apenas uma classe, não importa quantas instâncias de tipo parametrizado diferentes você criar. Não faria sentido algum ter um método como `clazz.getTypeArguments()` que te devolvesse uma array com os tipos parametrizados que tivessem sido usados na instanciação, já que só existe uma classe para todas as instâncias de diferentes tipos parametrizados. Isso se deve a [erasure](http://mindview.net/WebLog/log-0057): em tempo de execução só sabemos que `T` pode ser um filha de `Object`, nesse caso.

Mas e se existisse mais de uma classe?

\[java\] class AutorDao extends Dao<Autor> { }

class LivroDao extends Dao<Livro> { } \[/java\]

Obviamente `(new LivroDao()).getClass() == (new AutorDao()).getClass()` retorna `false`. Mais que isso, agora você consegue buscar por reflection qual é o parâmetro que foi passado na hora de estender a classe `Dao`, já que `AutorDao` e `LivroDao` possuem essa informação em seus bytecodes:

\[java\] Class clazz = (Class<T>) ((ParameterizedType) getClass().getGenericSuperclass()).getActualTypeArguments()\[0\]; \[/java\]

Então, se colocamos essa linha dentro da nossa classe mãe:

\[java\] public class Dao<T> { // ... public Dao(Session session) { this.session = session; this.persistentClass = (Class<T>) ((ParameterizedType) getClass().getGenericSuperclass()).getActualTypeArguments()\[0\]; } } \[/java\]

Agora funciona caso utilizemos uma instância de uma filha de `Dao` que tenha explicitado quem é `T`. Bem, eu não gostei dessa solução porque nos obrigava a ter uma classe filha, mesmo que vazia, para cada entidade. Mas o Orseni e o Alexandre não desistiram. A proposta deles foi de declarar `Dao` como abstrata, e na hora de instanciar: \[java\] Dao<Livro> dao = new Dao<Livro>(session){}; \[/java\]

Repare o `{}`! Esse código vai gerar uma classe anônima em tempo de compilação, e essa classe sem nome vai ser filha de `Dao<Livro>`, e agora podemos pegar essa informação por reflection! Isso sim é gambiarra criatividade :). Usar em produção? Acho que não seria elegante. Nem eles estão usando, mas foi um bonito desafio.
