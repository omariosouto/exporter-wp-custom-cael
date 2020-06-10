---
title: "Qual parâmetro devo receber em meus métodos?"
date: "2015-03-31"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Um dilema interessante sempre é: qual parâmetro devo receber em meu método? Por exemplo, imagine uma classe _NotaFiscal_, que é extremamente complexa e difícil de ser criada, pois depende de muitos outros objetos:

\[code\] class NotaFiscal { public NotaFiscal( Cliente cliente, List<Item> itens, List<Desconto> descontos, Endereco entrega, Endereco cobranca, FormaDePagamento pagto, double valorTotal ) { ...}

// muitos atributos e métodos } \[/code\]

Agora, imagine um outro método, responsável por calcular o valor do imposto dessa nota fiscal. Mas, para calcular o valor, o algoritmo leva em consideração apenas a lista de itens. Os outros atributos não são necessários para ele. Veja:

\[code\] class CalculadorDeImposto { public double calcula(NotaFiscal nf) { double total = 0; for(Item item : nf.getItens()) { if(item.getValor()>1000) total+= item.getValor() \* 0.02; else total+= item.getValor() \* 0.01; } return total; } } \[/code\]

Veja só que o método _calcula()_ recebe uma _NotaFiscal_. Mas veja que não precisaria. Poderíamos apenas receber uma _List_. A pergunta é: qual dos dois devemos receber? Essa é uma pergunta profunda, que envolve até uma discussão sobre acoplamento.

Discutimos anteriormente que [devemos sempre nos acoplar com dependências mais leves e menos frágeis](https://blog.caelum.com.br/orientacao-a-objetos-uma-outra-perspectiva-sobre-o-acoplamento/). Nesse exemplo em particular, _NotaFiscal_ parece ser uma classe pouco estável e bastante complicada; já uma lista de itens parece ser mais simples. Receber essa lista como parâmetro pode deixar o método _calcula()_ mais simples e menos propenso a sofrer modificações. Se, para o cálculo do imposto, o algoritmo precisasse apenas do valor total, por quê não receber um _double total_ apenas? Quanto mais simples, menos problemas.

Mas talvez uma lista de itens ou um double não deixe bem claro o que o método _calcula()_ precisa receber. Um double pode ser qualquer número, uma lista de ítens pode ser qualquer lista de ítens, mas não é isso que ele quer: ele quer um valor de uma nota fiscal, ou uma lista de ítens de uma nota fiscal. Garantias essas que tínhamos quando passávamos uma _NotaFiscal_ como parâmetro.

Para resolver isso de maneira elegante, por quê não criar uma abstração? Poderíamos ter uma interface chamada _Tributavel_, que seria implementada pela classe _NotaFiscal_. O método _calcula()_, por sua vez, receberia essa interface.

[![tributavel](https://blog.caelum.com.br/wp-content/uploads/2015/01/tributavel-300x83.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/tributavel.png)

Assim como no exemplo anterior, acabamos emergindo uma interface leve. Nesse exemplo em particular, a criação da interface _Tributavel_ nos traz vantagens:

- Diminuímos o risco do acoplamento, pois a interface _Tributavel_ é muito mais estável que a classe _NotaFiscal_.
- O método _calcula()_ recebe agora apenas o que realmente precisa. _Tributavel_ é uma interface magra.
- Temos semântica no parâmetro do método. Não recebemos uma lista ou um double qualquer, e sim algo passível de tributação.

Em código:

\[code\] interface Tributavel { List<Item> itensASeremTributados(); }

class NotaFiscal implements Tributavel { public NotaFiscal(...) { ... }

public List<Item> itensASeremTributados() { ... } }

class CalculadorDeImposto { public double calcula(Tributavel t) { double total = 0; for(Item item : t.itensASeremTributados()) { if(item.getValor()>1000) total+= item.getValor() \* 0.02; else total+= item.getValor() \* 0.01; } return total; } }

\[/code\]

Portanto, esse é mais um exemplo de como interfaces leves são importantes. Classes que dependem de interfaces leves sofrem menos com mudanças em outros pontos do sistema. Novamente, elas são pequenas, e portanto, tem poucas razões para mudar.
