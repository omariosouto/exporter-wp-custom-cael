---
title: "Intent: passando objetos com campos não serializáveis"
date: "2014-09-24"
author: "felipe.torres"
authorEmail: "felipe.torres@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Suponha a seguinte classe `Aluno`:

\[code language="java"\] public class Aluno { private String nome; private String telefone;

public Aluno(String nome, String telefone) { this.nome = nome; this.telefone = telefone; } } \[/code\]

Como faríamos para passar uma instância dessa classe para uma _Activity_?

Como visto nos cursos de Android da Caelum, poderiamos usar o método `putExtra()` da `Intent` para isso:

\[code language="java"\] Aluno aluno = new Aluno("Felipe", "555-1234");

Intent intent = new Intent(this, OutraActivity.class); intent.putExtra("aluno", aluno); startActivity(intent); \[/code\]

Porém, o `putExtra()` não é capaz de receber um `Aluno`! Então, temos que implementar a interface `Serializable`:

\[code language="java"\] public class Aluno implements Serializable { //... } \[/code\]

Agora a classe `Aluno` é capaz de serializar todos os seus atributos que podem ser serializáveis. Porém, o que acontece se a classe `Aluno` possuir algum atributo que não é serializável?

Um exemplo disso seria colocar um atributo `Context` na nossa classe para exibir um _Toast_:

\[code language="java"\] public class Aluno { private String nome; private String telefone; private Context context = CaixaPreta.getContext();

public Aluno(String nome, String telefone) { this.nome = nome; this.telefone = telefone; }

public void mostraNome() { Toast.makeText(context, this.nome, Toast.LENGTH\_LONG).show(); } } \[/code\]

Como `Context` não é serializável, teremos uma `java.io.NotSerializableException` ao chamar o `putExtra()`!

Para resolver esse problema, podemos simplesmente ignorar o campo `Context` na serialização por meio do _modifier_ `transient`:

\[code language="java"\] public class Aluno { private String nome; private String telefone; private transient Context context = CaixaPreta.getContext();

public Aluno(String nome, String telefone) { this.nome = nome; this.telefone = telefone; }

public void mostraNome() { Toast.makeText(context, this.nome, Toast.LENGTH\_LONG).show(); } } \[/code\]

Como poderemos chamar o método `mostraNome()` após recuperar o _aluno_ na `OutraActivity`?

\[code language="java"\] Intent intent = getIntent(); Aluno recuperado = (Aluno) intent.getSerializableExtra("aluno");

recuperado.mostraNome(); \[/code\]

Como ignoramos o campo _Context_, ao recuperar o _aluno_ o conteúdo de `context` será _null_.

À primeira vista, poderíamos popular o `context` no construtor do `Aluno`:

\[code language="java"\] public Aluno(String nome, String telefone) { this.nome = nome; this.telefone = telefone; this.context = CaixaPreta.getContext(); } \[/code\]

Porém, o processo de desserialização não chama o construtor da classe. Então ainda teremos um _null_ no `context`.

Ao fazer a desserialização do nosso _aluno_ podemos implementar um método chamado `readObject`, responsável por popular os campos do objeto desseralizado.

\[code language="java"\] public class Aluno { private String nome; private String telefone; private transient Context context = CaixaPreta.getContext();

public Aluno(String nome, String telefone) { this.nome = nome; this.telefone = telefone; }

public void mostraNome() { Toast.makeText(context, this.nome, Toast.LENGTH\_LONG).show(); }

private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException { in.defaultReadObject(); this.context = CaixaPreta.getContext(); } } \[/code\]

Com isso, podemos passar o `Aluno` para uma outra _Activity_ e repopular o campo que não é serializável!
