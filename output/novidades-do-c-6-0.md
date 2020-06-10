---
title: "Novidades do C# 6.0"
date: "2015-05-19"
author: "gabriel.ferreira"
authorEmail: "gabriel.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A versão 2015 do [Visual Studio](https://www.visualstudio.com/en-us/downloads/visual-studio-2015-downloads-vs.aspx)(versão [Release Candidate](http://www.webopedia.com/TERM/R/release_candidate.html)) já está disponível para download e com ele podemos utilizar o novo C# 6.

De acordo com [Mads Torgensen](https://channel9.msdn.com/Events/Speakers/Mads-Torgersen), um dos principais responsáveis pelo futuro da linguagem, a ideia geral dessa nova versão é não ter grandes conceitos para você ter que ficar quebrando a cabeça pra aprender. Ao invés disso, focaram em criar pequenas novas funcionalidades pra te ajudar a escrever código melhor e mais limpo.

Vamos dar uma olhada nessas funcionalidades.

**Auto-properties só com get**

Atualmente, se quisermos criar uma auto-property temos que fazer dessa forma:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; } public Cliente Titular { get; set; } }\[/code\]

Mas e se eu quiser que _Saldo_ seja uma propriedade somente leitura(ou seja, sem o _set_)? Atualmente, não conseguimos fazer isso com propriedades, teríamos que criar um atributo somente leitura:

\[code language="java"\]public class Conta { public readonly double Saldo; public int Numero { get; set; } public Cliente Titular { get; set; } }\[/code\]

No C# 6.0, podemos fazer diferente e definir a propriedade sem o _set_:

\[code language="java"\]public class Conta { public double Saldo { get; } public int Numero { get; set; } public Cliente Titular { get; set; } }\[/code\]

Por trás dos panos, o C# está criando um atributo _readonly_ para nós que, se quisermos, podemos inicializar no construtor da classe:

\[code language="java"\]public class Conta { public double Saldo { get; } public int Numero { get; set; } public Cliente Titular { get; set; }

public Conta() { this.Saldo = 100; //inicializando a conta com o saldo inicial de 100 reais } }\[/code\]

É bom salientar que não estamos usando um _setter_ pra inicializar a propridade, pois não há _setter_. O C# está colocando o valor 100 direto no atributo readonly que está por trás da propriedade.

**Initializers para auto-properties**

Podemos também agora inicializar nossas propriedades com um valor padrão:

\[code language="java"\]public class Conta { public double Saldo { get; } = 100; public int Numero { get; set; } = 1; public Cliente Titular { get; set; } = new Cliente(); }\[/code\]

Isso antes não era possível, se quiséssemos fazer algo parecido tinhamos que inicializar as propriedades no construtor da classe. Veja que isso vale tanto para propriedade com _get_ e _set_ quanto para a que tem só o _get_.

**Using estático**

Vamos supor que eu vá fazer várias conversões em minha classe e usarei muito a classe _Convert_:

\[code language="java"\]public class Conta { public double Saldo { get; } = 100; public int Numero { get; set; } = 1; public Cliente Titular { get; set; } = new Cliente(); public void Metodo1(string parametro) { int variavelInt = Convert.ToInt32(parametro); }

public void Metodo2(string parametro) { double variavelDouble = Convert.ToDouble(parametro); } }\[/code\]

Ao invés de ficar utilizando o nome da classe toda hora, podemos fazer:

\[code language="java"\] using static System.Convert;

public class Conta { public double Saldo { get; } = 100; public int Numero { get; set; } = 1; public Cliente Titular { get; set; } = new Cliente(); public void Metodo1(string parametro) { int variavelInt = ToInt32(parametro); }

public void Metodo2(string parametro) { double variavelDouble = ToDouble(parametro); } }\[/code\]

Veja que colocamos um _using_ direto para a **classe**, e não para um namespace. Dessa forma podemos chamar os métodos da classe _Convert_ sem termos que ficar colocando o nome da classe toda vez. Detalhe: isso só funciona com classes estáticas.

**Interpolação de strings**

Suponhamos que eu queira sobrescrever o método _ToString_ em minha classe Conta para que ele retorne o numero e o saldo da conta no formato "Numero:X, Saldo:Y". Eu poderia fazer dessa forma, usando _String.Format_:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; } public Cliente Titular { get; set; }

public override string ToString() { return string.Format("Numero:{0}, Saldo:{1}", this.Numero, this.Saldo); } }\[/code\]

Mas esse negócio de usar números dentro de uma string é algo que pode ficar meio confuso dependendo do tamanho dela. Pra simplificar, podemos fazer a mesma coisa, mas agora com uma sintaxe diferente:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; }

public override string ToString() { return $"Numero:{this.Numero}, Saldo:{this.Saldo}"; } }\[/code\]

Note que tiramos a chamada para o método _Format_ e o substituímos por um cifrão(**$**). Fazemos isso para dizer ao C# que estamos fazendo uma interpolação de strings. Dessa forma, ao invés de utilizarmos os _placeholders_(as chaves) com números, podemos colocar direto os valores que queremos.

**_Expression-bodied methods_ para a definição de métodos simples**

Esse nosso método ToString() tem só uma linha que retorna um valor, certo? É comum termos vários métodos em nossa aplicação parecidos com esse. Para simplificar a escrita desse tipo de método, agora podemos defini-lo da seguinte forma:

\[code language="java"\]public override string ToString() => $"Numero:{this.Numero}, Saldo:{this.Saldo}";\[/code\]

Podemos utilizar essa síntaxe para propriedades também. Vamos supor que tenhamos uma propriedade chamada _ProximoNumero_ que simplesmente retorna o número da próxima conta:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; }

public int ProximoNumero { get { return this.Numero + 1; } } }\[/code\]

Podemos simplificar essa propriedade da seguinte forma:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; }

public int ProximoNumero => this.Numero + 1; }\[/code\]

Veja que nessa versão não precisamos colocar o _get_: ele já está implícito através dessa sintaxe(dessa forma não temos como fazer o get **e** set).

**Operador condicional nulo**

Suponhamos que em nosso método _ToString()_, além de mostrar o número e saldo da conta, queremos também mostrar o nome do titular. Porém, em nossa classe _Conta_, _Titular_ é uma propriedade do tipo Cliente, que pode ter sido inicializada ou não(estar com o valor **null**). Sendo assim teríamos que verificar se se a propriedade é nula, e fazendo isso perderíamos aquela sintaxe simplificada e legal que fizemos para o método:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; } public Cliente Titular { get; set; } = new Cliente();

public override string ToString() { if (this.Titular != null) { return $"Numero:{this.Numero}, Saldo:{this.Saldo}, Titular:{this.Titular.Nome}"; }

else { return $"Numero:{this.Numero}, Saldo:{this.Saldo}, Titular:"; } } }\[/code\]

Com o novo operador condicional nulo do C# 6.0, podemos fazer esse método da seguinte forma:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; } public Cliente Titular { get; set; } = new Cliente();

public override string ToString() => $"Numero:{this.Numero}, Saldo:{this.Saldo}, Titular:{this.Titular?.Nome}"; }\[/code\]

Repare no **?** antes do acesso à propriedade _Nome_ do _Titular_. Isso significa: tente acessar o Nome do cliente **somente** se o Titular for diferente de nulo. Se não for, ele simplesmente não acessa. Legal, não? Isso pode evitar vários problemas de referência nula.

**Operador nameof**

Nós, programadores, lidamos com todo tipo de problema no dia-a-dia. Às vezes, por alguma razão, pode surgir a necessidade de nós sabermos o **nome** de uma variável e guardarmos ela em uma string. Vamos supor que por alguma razão bizarra queiramos que nossa classe Conta tenha um método que retorne o **nome** da propriedade Titular. Podemos usar o novo operador **nameof**:

\[code language="java"\]public class Conta { public double Saldo { get; set; } public int Numero { get; set; } public Cliente Titular { get; set; } = new Cliente();

public string RetornaNomeDaPropriedadeTitular() { return nameof(this.Titular); //retorna "Titular" } } \[/code\]

**Conclusão**

Como dito no inicio do post, a principal motivação da Microsoft para essa versão do C# são pequenas mudanças que fazem diferença na simplicidade e produtividade. As mudanças aqui realmente parecem pequenas, mas se olharmos do panorama de um grande projeto, elas podem fazer **muita** diferença na hora de escrever código.

Você pode saber mais sobre as novidades do C# [nesse video](http://channel9.msdn.com/Series/Visual-Studio-2015-Enterprise-Videos/Whats-New-in-CSharp-6) e [nesse post](http://www.kunal-chowdhury.com/2014/12/csharp-6-null-conditional-operators.html).

E ai, o que você achou das novidades? Deixe seu comentário a respeito!

_Você pode aprender C# no curso [C# e Orientação a Objetos](www.caelum.com.br/apostila-csharp-orientacao-objetos/) da Caelum ou através da [trilha C# e .NET](https://www.alura.com.br/cursos-online-csharp) do Alura._
