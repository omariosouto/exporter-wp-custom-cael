---
title: "Em busca do nome adequado: métodos, variáveis e classes"
date: "2010-10-08"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

É muito comum após alguns dias de trabalho em um projeto perceber que as escolhas de nomes de classes e métodos não condizem com o que cada um representa. Isso acontece pois, com o passar do tempo, aumenta o nosso conhecimento sobre o domínio do problema.

Também é natural surgir o desejo de mudança: [a medida que nos especializamos nesse domínio, o modelo do negócio em nossa mente passa a ser diferente da visão inicial](http://fragmental.tw/2010/09/06/thoughts-on-abstractions-part-2-abstractions-in-your-domain/).

Um exemplo simples dessa mudança acontece quando em OO criamos um relacionamento bidirecional entre duas classes através de listas, como no exemplo a seguir:

\[java\] // a representação de um cliente, funcionário etc public class Pessoa { List<Conta> contas; }

// a representação de uma conta bancária (poupança, corrente, investimento etc) public class Conta { List<Pessoa> pessoas; } \[/java\]

A primeira vista esse é o relacionamento entre as duas entidades mas em determinado momento notamos que na verdade a relação entre pessoa e conta é um Titular, que possue características próprias, como o contrato que ele como titular da conta assinou:

\[java\] public class Pessoa { List<Titular> titularidades; } public class Conta { List<Titular> titulares; } public class Titular { Conta conta; Pessoa pessoa; // outras características de uma titularidade } \[/java\]

Nesse caso o processo de refatoração envolve extrair uma classe que representa tal relação, um Titular: um exemplo simples que demonstra como em OO é comum a existência de classes para representar informações intangíveis.

Outras questões simples mas fundamentais para [o bom design e legibilidade da aplicação estão ligados aos nomes que escolhemos](http://www.tectura.com.br/topics/nomes_complexos_descritivos_no_modelo), não só em relação ao nome da classe e de seus métodos. No exemplo acima, por uma escolha fraca no nome da variável membro **conta** e **pessoa** acabamos com um código que parece repetitivo e pouco descritivo:

\[java\] Pessoa pessoa; \[/java\]

Se em nosso sistema funcionários, gerentes, titulares e caixas são representados através de instâncias do tipo `Pessoa`, o mais adequado seria nomeá-los de acordo:

\[java\] public class Titular { Conta conta; Pessoa cliente; } \[/java\]

A tendência natural do desenvolvedor em linguagens que obrigam a declaração do tipo é criar variáveis com nome identicos ao tipo declarado, por ser um atalho das IDEs e mais simples de escrever. Mas tais alternativas, como visto, dizem pouco sobre a variável que está sendo acessada. Por esses motivos, é muito comum encontrar código como:

\[java\] Cliente cliente = clienteDao.busca(13); Pagamento pagamento = pagamentoDao.busca(15); ProcessadorDePagamento processador = new ProcessadorDePagamento(); processador.processa(cliente, pagamento); \[/java\]

Cliente, pagamento, processador: é somente desses três elementos que nosso domínio é composto? E nem mesmo Ruby foge disso, sendo comum encontrar:

\[ruby\] cliente = Cliente.find\_by\_id 13 pagamento = Pagamento.find\_by\_id 15 processador = Processador.new processador.processa cliente, pagamento \[/ruby\]

Três aparições de cliente, três de pagamento, quatro de processador. Aqui existe uma visão distorcida de que variáveis são somente aquilo que o tipo descreve: um cliente, um pagamento, um processador. Na verdade elas referenciam instâncias específicas e especializadas de um tipo determinado. Só faria sentido chamar o cliente 13 de **cliente** se ele é o único cliente, um singleton. Especialize o nome de suas variáveis e crie um código mais legível:

\[java\] Cliente devedor = clientes.busca(13); Pagamento fatura = pagamentos.busca(15); ProcessadorDePagamento mensalidade = new ProcessadorDePagamento(); mensalidade.cobra(devedor, fatura); \[/java\]

O mesmo pode ser refletido no exemplo de Ruby. E como trabalharemos nossos repositórios para permitir uma maior legibilidade do código?

\[java\] Pessoa pessoa = ...; List<Conta> contas = dao.contasDe(pessoa); \[/java\]

Note como por causa da escolha do nome da variável `pessoa` fomos obrigados a colocar algo mais descritivo no método. Caso a variável possua um nome mais adequado, teríamos então:

\[java\] Pessoa cliente = ...; List<Conta> contas = dao.contasDe(cliente); \[/java\]

Modificando nossa classe `Conta` para adicionar o gerente e o subgerente temos novamente o sentido das variáveis descrito não só pelo seu tipo, mas também pelo seu nome:

\[java\] public class Conta { List<Titular> titulares; Pessoa gerente; Pessoa subgerente; } \[/java\]

Nesse instante a query ficaria mais complexa pois existem dois relacionamentos distintos com pessoa, requerendo [a criação de uma DSL interna para query](http://martinfowler.com/dslwip/InternalOverview.html). No exemplo a seguir usamos um builder para facilitar a construção da query:

\[java\] Pessoa gerente = ...; Pessoa subgerente = ...; List<Conta> contas = dao.comGerenteESubgerente (gerente, subgerente): \[/java\]

Essa é a abordagem que o jbehave 3.2 e o selenium utilizam, onde acabamos por ter uma sintaxe muito longa e repetitiva.

Retomando o problema do nome das variáveis, que pode ser resolvido com outra refatoração de rename: o código da terceira linha é mais descritivo que o anterior:

\[java\] Pessoa gerente = ...; Pessoa subgerente = ...; List<Conta> contas = dao.comGerente(gerente) .comSubgerente(subgerente): \[/java\]

Em linguagens onde mapas podem ser representados através de literais, o ruído sintático é baixo e permite que seja escrito código como a seguir em Ruby:

\[ruby\] gerente = ... subgerente = ... contas = Conta.com :gerente => gerente, :subgerente => subgerente \[/ruby\]

Novamente, a refatoração aqui é fundamental:

\[ruby\] principal = ... secundario = ... contas = Conta.com :gerente => principal, :subgerente => secundario \[/ruby\]

A escolha dos nomes de nossas classes influencia o design e legibilidade de nosso código direta e indiretamente, primeiro pela declaração das variáveis e a nossa visão do domínio. Depois pelas consequências que o mesmo possui na escolha dos nomes das variáveis e métodos, que permitem um design mais ou menos legível.

Essa escolha é tão importante quanto o resto de seu design para facilitar a compreensão do que seu domínio é e qual o problema que está tentando atacar. Na próxima vez que for declarar uma variável com o mesmo nome que o tipo, pense duas vezes.
