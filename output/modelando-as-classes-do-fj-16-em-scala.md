---
title: "Modelando as classes do FJ-16 em Scala"
date: "2010-12-06"
author: "trusso"
authorEmail: "thadeu.russo@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um dos cursos que mais gosto de ministrar na Caelum é o [FJ-16](http://www.caelum.com.br/curso/fj-16-laboratorio-java-testes-xml-design-patterns/). O motivo? Bem, fugir um pouco do velho CRUD e poder mostrar a real utilidade de alguns padrões voltados a OO, uso de testes de unidade, geração de gráficos e o principal: modelar um domínio rico sobre a bolsa de valores e Candlesticks.

O curso é em Java, mas já faz algum tempo que a JVM tem sido explorada além do Java (vide JRuby, Groovy, Rhino, etc), e uma delas me chamou mais atenção de todos, a [Scala](http://www.scala-lang.org/), que [pode ser sua próxima linguagem de programação](https://blog.caelum.com.br/scala-sua-proxima-linguagem/). Assim como toda linguagem nova que se começa a estudar, precisamos arrumar uma 'desculpa' para programar algo, e a minha desculpa foi a implementação do modelo do curso FJ-16.

Um dos perigos ao programar com Scala vindo do Java é realizar apenas uma tradução, sem ganhar muito com suas características funcionais. Vamos tentar aproveitar suas características:

Requisito #1: Criar uma classe `Negocio` com atributos preço, quantidade e data. A classe deve ser imutável e possuir um método para saber o seu volume:

\[scala\] class Negocio(val preco: Double, val quantidade: Int, val data: Calendario) { require(preco >= 0.0) require(quantidade > 0) require(data != null)

def volume = preco \* quantidade override def toString = data.toString } \[/scala\]

Um dos grandes objetivos da linguagem Scala é reduzir o código repetitivo que muitas vezes somos obrigados a escrever em Java, como getters and setters, ifs seguidos de lançamento de exceções, etc.

Vejam que neste trecho de código usamos _val_s no construtor da classe. Isso significa que estes atributos são como variáveis públicas e final, devendo ser passados pelo construtor e não podem ter seus valores alterados. Vale apena dizer que Scala usa o [princípio de acesso uniforme](http://en.wikipedia.org/wiki/Uniform_access_principle), logo não se assuste achando que estará acessando diretamente os atributos.

Outro ponto a se destacar é o uso de _require_s, que são usados para forçar as condições de validação dos nossos atributos (análogos aos if's dentro do construtor seguidos de `IllegalArgumentException`).

Em Java, quando se deseja trabalhar com datas é algo complicado (basta notar o tanto de métodos deprecated na classe `java.util.Date`, e saber que `java.util.Calendar` possui lá seus problemas como add de números negativos, mutabilidate, sem falar em outras alternativas como a api Joda Time). Para o nosso problema, a classe `Calendar` do Java quase que cobre todas nossas necessidades, porém é mutável (poderíamos facilmente criar um negócio com a data de hoje, e mudar o valor interno do calendar para daqui a uma semana). Por este motivo, foi criada a classe `Calendario` apenas para ser um _wrapper_ e resolver o problema da imutabilidade:

\[scala\] class Calendario(calendar: Calendar) extends Ordered\[Calendario\] { import Calendario.\_

private val \_calendario: Calendar = calendar.clone.asInstanceOf\[Calendar\]

def calendario: Calendar = \_calendario.clone.asInstanceOf\[Calendar\]

override def toString = formatter.format(\_calendario.getTime)

def compare(that: Calendario) = this.\_calendario.compareTo(that.\_calendario) }

object Calendario { private\[argentum\] val formatter = new SimpleDateFormat("dd/MM/yyyy")

implicit def calendarToCalendario(calendar: Calendar): Calendario = { require(calendar != null) new Calendario(calendar) }

implicit def calendarioToCalendar(c: Calendario): Calendar = c.calendario

def dia(n: Negocio) = (n.data.get(DAY\_OF\_MONTH), n.data.get(MONTH), n.data.get(YEAR)) } \[/scala\]

Foi criado também um singleton (em Scala para a criação de singletons - ou [companion objects](http://www.artima.com/scalazine/articles/steps.html) - usamos `object` ao invés de `class`) que faz a [conversão implícita](http://www.scala-lang.org/node/130) entre Calendars e Calendarios. Um exemplo de uso das classes:

\[scala\] var negocio = new Negocio(40.5, 100, Calendar.getInstance) print(negocio) \[/scala\]

**Transformar Negocios em Candlesticks**

Requisito #2: Dado uma lista de negócios de um mesmo dia (`List[Negocio]`) criar um [candlestick](http://pt.wikipedia.org/wiki/Candlestick). Um candlestick possui os seguintes atributos: valor de abertura (valor do primeiro negocio da lista), valor de fechamento (valor do ultimo negocio da lista), maior valor (valor do maior negocio da lista), menor valor (valor do menor negocio da lista), volume (a soma dos volumes de todos os negócios) e data. Métodos para saber se o candlestick é de alta ou de baixa:

\[scala\] class Candlestick(val abertura: Double, val fechamento: Double, val minimo: Double, val maximo: Double, val volume: Double, val data: Calendario) { require(abertura >= 0.0) require(fechamento >= 0.0) require(minimo >= 0.0) require(maximo >= 0.0) require(volume >= 0.0) require(data != null)

def alta = abertura < fechamento def baixa = !alta override def toString = "(abertura %f, fechamento %f, minimo %f, maximo %f, volume %f, data: %s)" format (abertura, fechamento, minimo, maximo, volume, data) } \[/scala\]

Agora, precisamos implementar a transformação da `List[Negocio]` do mesmo dia em um `Candlestick`. Para achar os valores de abertura e fechamento basta pegarmos o primeiro e último elemento da lista, respectivamente. Para achar o maior valor basta percorrermos a lista indo do primeiro ao último elemento verificando se é maior que os anteriores. Analogamente, para acharmos o menor seguimos a mesma estratégia, porém verificando se é menor. Para o volume, basta percorrer a lista somando os volumes dos negócios.

Perguntas: E se a lista estiver vazia (não há negocios no dia)? Qual o valor base para achar o maior valor? E o menor? Devemos usar Iterator ou índices para pegar os negócios da lista? Começamos em 0 ou 1? Tem que ser menor (<) ou menor igual (<=) o size da lista? Hum.. isso me cheira um código cheio de ifs e com possíveis runtime exceptions.

Com o uso de funções de ordem superior ([higher order functions](http://en.wikipedia.org/wiki/Higher-order_function)) e [casamento de padrões](http://en.wikipedia.org/wiki/Pattern_matching), recursos de linguagens funcionais, conseguimos ter uma implementação mais enxuta e robusta:

\[scala\] def candlestick(negocios: List\[Negocio\]): Candlestick = negocios match { case Nil => new Candlestick(abertura = 0.0, fechamento = 0.0, minimo = 0.0, maximo = 0.0, volume = 0.0, Calendar.getInstance) case lista => val maiorPreco = lista.map(\_.preco).max val menorPreco = lista.map(\_.preco).min val volume = lista.map(\_.volume).sum new Candlestick( abertura = lista.head.preco, fechamento = lista.last.preco, minimo = menorPreco, maximo = maiorPreco, volume = volume, data = lista.head.data) } \[/scala\]

Vamos entender o código acima. Primeiramente, vale ressaltar que `List` em Scala é uma classe imutável, assim como String. Operações como + ou add geram uma lista nova. Em segundo lugar, estamos usando o casamento de padrões para saber se a lista que estamos recebendo está vazia ou não (Nil neste contexto é equivalente à lista vazia). Caso a lista esteja vazia, devolvemos o que definimos como candlestick nulo.

Para o caso de a lista possuir ao menos um negócio, fazemos uso do método `map` da classe List. Map aplica a função que foi passada como argumento para cada elemento da lista. Nas três primeiras linhas passamos algo como `_.nomeDoAtributo`. O underscore \_ funciona como um substituto para o valor do negócio que estamos olhando e age como açúcar sintático (poderíamos ter feito algo como `lista.map((negocio: Negocio) => negocio.nomeDoAtributo)`). Para as três linhas, teremos como resultado uma lista com os valores dos atributos (na primeira e segunda duas listas com os preços, e na terceira, uma lista com os volumes). Uma vez que os valores foram 'extraídos', pegamos o menor, maior e somamos os valores da lista. Simples não?!

**Transformando uma série de negócios em vários candlesticks**

Requisito #3: Dado uma lista de negócios **de dias diferentes**, criar **uma lista de candlesticks em ordem crescente de data**. Bom, como já sabemos transformar uma lista de negócios _de um mesmo dia_ em um candlestick, para implementar esse requisito devemos apenas agrupar a lista em listas menores que tenham negócios no mesmo dia e fazer uso da função já criada, ordenando o resultado no final:

\[scala\] def candlesticks(negocios: List\[Negocio\]): List\[Candlestick\] = { val candlesticks = negocios.groupBy(dia).map { case (\_, negociosNoDia) => candlestick(negociosNoDia) }.toList candlesticks.sortBy(\_.data) } \[/scala\]

Fazemos o uso do método `List.groupBy` e passamos a função `Calendario.dia` como função de grupo. O resultado será um mapa cujas chaves são instâncias de Calendario e o valores listas de negócios (uau!). Em seguinda, usamos a função `Map.map` para percorrer os valores do mapa (que nesse caso são as listas de negócios agrupados por dia) e aplicar a conversão para candlesticks (uau novamente!). Por fim, extraímos os valores do mapa como uma lista, aplicamos a ordenação pela data e pronto, objetivo atingido!

Pode parecer um pouco indigesto no início, mas fica como desafio escrever em Java este mesmo código. A versão em Scala usando recursos funcionais é mais curta e elegante.

O código completo, incluindo os testes unitários, pode ser baixado no [github](https://github.com/thadeurc/caelum-argentum/).

* * *

Agradecimentos especiais ao [Rafael Ferreira](http://blog.rafaelferreira.net/) pelas excelentes sugestões que fez no código original.
