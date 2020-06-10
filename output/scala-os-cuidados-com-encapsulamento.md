---
title: "Scala: os cuidados com encapsulamento"
date: "2011-07-26"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um dos pontos difíceis de lidar em qualquer sistema está ligado com [quebra de encapsulamento](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod). Em Java, uma vez que o padrão de uma variável membro permite que ela seja acessada por fora do objeto, protegemos os dados através do modificador `private` e um getter:

\[java\] public class Conta { private List<Movimentacao> movimentacoes = new ArrayList<Movimentacao>(); private double saldo;

public double getSaldo() { return saldo; }

public List<Movimentacao> getMovimentacoes() { return movimentacoes; } } \[/java\]

Enquanto a criação de getters é um padrão amplamente adotado, muitas vezes ainda deixamos vazar os objetos mutáveis, como no caso da lista acima. Para não vazar referências a tais objetos, quebrando o encapsulamento e possívelmente alguma funcionalidade interna, retornamos uma cópia segura da mesma:

\[java\] public List<Movimentacao> getMovimentacoes() { // protegendo a lista interna para não quebrar o encapsulamento return Collections.unmodifiableList(movimentacoes); } \[/java\]

[![http://www.flickr.com/photos/fxtreme/235948470/](https://blog.caelum.com.br/wp-content/uploads/2011/07/235948470_38c8994d9d_z-300x200.jpg "235948470_38c8994d9d_z")](https://blog.caelum.com.br/wp-content/uploads/2011/07/235948470_38c8994d9d_z.jpg) [A criação de getters e setters deve ser feita com cuidado](https://blog.caelum.com.br/nao-aprender-oo-getters-e-setters/): uma vez que a implementação é exposta através de um getter e setter, a remoção dele, para finalmente encapsulá-lo, implica na mudança de todas as chamadas a ele. Em Ruby, o mesmo cuidado pode ser tomado:

\[ruby\] class Conta def movimentacoes @movimentacoes.dup end end \[/ruby\]

Em Scala, existem diversas variações para declaração de variáveis membro. A padrão que ainda [nos ajuda a criar good citizens](http://docs.codehaus.org/display/PICO/Good+Citizen), é a definição de uma variável privada, final:

\[java\] class Conta(saldo:Double) \[/java\]

A desvantagem é que as vezes é interessante colocar um getter para expor o conteúdo referenciado. No caso de uma conta bancária, é interessante expor o saldo da mesmo, mas não uma lista de movimentações sem protegê-la.

Para isso, podemos definir uma `val` (equivalente a variáveis final em Java), uma vez que o papel do getter é gerado automaticamente pelo compilador (nomes de variáveis e métodos estão no mesmo contexto, portanto não requerem recompilação quando um substitui o outro):

\[java\] class Conta(val saldo:Double) \[/java\]

A desvantagem pode estar em desejar alterar o saldo da conta e não poder. Nesse caso definiríamos o membro como `var`:

\[java\] class Conta(var saldo:Double) \[/java\]

A desvantagem nessa abordagem é a criação do getter e do setter de exposição: ao definir uma variável como "private" mas permitir o acesso a mesma, o encapsulamento é novamente quebrado. Portanto, para membros cuja referência pode ser alterada (vars), é interessante gerar o "getter" mas não expor o setter para não quebrar o encapsulamento, como em:

\[java\] class Conta(private var \_saldo:Double) { def saldo = \_saldo } \[/java\]

Mas nesse caso, o código deixa de ser curto como nas abordagens onde o encapsulamento é mais facilmente quebrado. O mesmo é válido para muitas linguagens: é mais fácil quebrar o encapsulamento dos objetos do que executar a troca de mensagens para efetuar uma transferência:

\[java\] class Conta(private var \_saldo:Double) { def saldo = \_saldo def debita(valor:Double) { movimentacoes.add(new Movimentacao(-valor)); \_saldo -= valor } } \[/java\]

O encapsulamento é a base de orientação a objetos. Nos momentos em que é adequado trabalhar de forma OO, é ele que [permite a troca de mensagens entre objetos](http://pragprog.com/articles/tell-dont-ask) prevenindo o usuário de depender de como o tipo funciona internamente. Das maneiras demonstradas aqui, escolha a maneira de encapsular suficientemente adequada para faciltiar a manutenção de suas classes a longo prazo (para evitar quebras inesperadas).

Como pergunta, será que seria interessante a linguagem fornecer uma opcao só de getter para var?
