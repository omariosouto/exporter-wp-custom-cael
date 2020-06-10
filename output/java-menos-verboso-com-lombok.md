---
title: "Java menos verboso com Lombok"
date: "2013-02-14"
author: "flavio.almeida"
authorEmail: "flavio.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já vimos [como não aprender Java e Orientação a Objetos](https://blog.caelum.com.br/nao-aprender-oo-getters-e-setters/). Aprendemos que a geração indiscriminada de getters e setters pode quebrar o encapsulamento de suas classes.

Quando há realmente a necessidade de alguns getters e setters, podemos gerá-los com o auxílio de nossa IDE preferida, inclusive é boa prática reescrevermos os métodos equals(), hashCode() e toString(). Vejamos um exemplo de uma simples classe com apenas 3 atributos:

\[code language="java"\] import java.util.Calendar;

public class Contato { private Long id; private String nome; private Calendar dataNascimento;

public void setId(Long id) { this.id = id; }

public Long getId() { return this.id; }

public void setNome(String nome) { this.nome = nome; }

public String getNome() { return this.nome; }

public void setDataNascimento(Calendar dataNascimento) { this.dataNascimento = dataNascimento; }

public Calendar getDataNascimento() { return this.dataNascimento; }

@Override public String toString() { return "Contato \[id=" + id + ", nome=" + nome + ", dataNascimento=" + dataNascimento + "\]"; }

@Override public int hashCode() { final int prime = 31; int result = 1; result = prime \* result + ((dataNascimento == null) ? 0 : dataNascimento.hashCode()); result = prime \* result + ((id == null) ? 0 : id.hashCode()); result = prime \* result + ((nome == null) ? 0 : nome.hashCode()); return result; }

@Override public boolean equals(Object obj) { if (this == obj) return true; if (obj == null) return false; if (getClass() != obj.getClass()) return false; Contato other = (Contato) obj; if (dataNascimento == null) { if (other.dataNascimento != null) return false; } else if (!dataNascimento.equals(other.dataNascimento)) return false; if (id == null) { if (other.id != null) return false; } else if (!id.equals(other.id)) return false; if (nome == null) { if (other.nome != null) return false; } else if (!nome.equals(other.nome)) return false; return true; } } \[/code\]

Muita coisa, não?

### Diminuindo a verbosidade

Temos um código funcional, porém extremamente verboso. Podemos evitar esta verbosidade tão comum em código Java com o auxilio do [Project Lombok](http://projectlombok.org), que já foi comentado neste [post sobre meta programação em Java](https://blog.caelum.com.br/metaprogramacao-em-java-o-papel-do-apt/). Vejamos o mesmo código reescrito utilizando **Lombok**:

\[code language="java"\] import java.util.Calendar;

import lombok.EqualsAndHashCode; import lombok.Getter; import lombok.Setter; import lombok.ToString;

@EqualsAndHashCode @ToString public class Contato { @Getter @Setter private Long id;

@Getter @Setter private String nome;

@Getter @Setter private Calendar dataNascimento; } \[/code\]

Utilizando a classe Contato:

\[code language="java"\] Contato contato = new Contato();

// Como este setter apareceu? contato.setNome("Flávio Almeida");

// E este getter? System.out.println(contato.getNome());

// Imprimiu todos os atributos da classe! contato.toString(); \[/code\]

### A mágica por trás do Lombok

Tudo isso é possível porque o Lombok integra-se tanto ao **Eclipse** quanto ao **NetBeans**. Quando a IDE **compila** suas classes, automaticamente é gerado o respectivo código Java para cada um das anotações utilizadas: **@Setter**, **@Getter**, **@EqualsAndHashCode** e **@ToString**, tudo através de [Abstract Syntax Tree (AST)](http://www.eclipse.org/resources/resource.php?id=260).

Isso pode ser visto através da view de outline do eclipse:

[![outline-eclipse](https://blog.caelum.com.br/wp-content/uploads/2014/06/IMAGEM_1-300x177.png)](https://blog.caelum.com.br/wp-content/uploads/2014/06/IMAGEM_1.png)

### Integrando o Lombok ao seu Eclipse/Netbeans e ao seu projeto

O lombok precisa ser instalado no Eclipse/NetBeans, o que é feito baixando o jar no site [Project Lombok](http://projectlombok.org).

Ao executar o jar através do comando `java -jar lombok.jar`, um assistente perguntará o local da instalação da IDE e nada mais.

[![instalador-lombok](https://blog.caelum.com.br/wp-content/uploads/2014/06/IMAGEM_2-300x199.png)](https://blog.caelum.com.br/wp-content/uploads/2014/06/IMAGEM_2.png)

Por fim, como precisamos importar suas anotações, o jar deve estar no **build path** do seu projeto.

### Conclusão

É possível escrever código Java menos verboso com auxílio do Lombok sem termos que esperar por modificações na linguagem. Existem [outras anotações](http://projectlombok.org/features/index.html) deste projeto que podem ser utilizadas para enxugar ainda mais o seu código.

Certamente uma extensão tão poderosa da linguagem pode também te trazer problemas: uma equipe nova pode demorar a se habituar, além de você ficar dependendo da ferramenta em relação a atualizações, bugs, etc. Mas com isso podemos ver que o Java pode sim ter um código mais conciso e sucinto.
