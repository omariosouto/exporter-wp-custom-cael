---
title: "Código expressivo e programação funcional em Java com LambdaJ"
date: "2012-02-13"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Há um certo tempo refatoramos o sistema que trabalhamos. Migramos de uma arquitetura definida pelo Fowler como [Transaction Script](http://takacsot.freeblog.hu/Files/martinfowler/transactionScript.html) ([explicada nesse edição da MundoJ](http://www.mundoj.com.br/44conteudo.shtml)) para uma arquitetura essencialmente [O.O Domain Model](http://www.mundoj.com.br/42conteudo.shtml). Por incrível que pareça, o que mais nos chamou a atenção nesse processo não foi o fato de transferir a lógica de negócios para as entidades responsáveis, visto que delegar responsabilidades é complicado e é isso que faz do O.O tão desafiante, mas sim a qualidade na [expressividade do código](http://geraldoferraz.blogspot.com/2011/11/seu-codigo-e-legivel.html) que atingimos.

Alguns sinais de que seu código não esteja expressivo são os [comentários](http://www.infoq.com/br/news/2010/03/To-Comment-or-Not-to-Comment). A lógica é que, se você está explicando com comentários algo que o próprio código deveria dizer, é um smell, neste caso um forte sinal da falta de expressividade (não para a linguagem, mas não está se expressando bem para outro programador).

Sabemos que [programador odeia ver código de outro programador](http://tecnoblog.net/58041/pesquisa-comprova-programar-em-c-gera-mais-xingamentos/), e nos tempos atuais onde a agilidade e ideia de código coletivo estão cada vez mais em alta, código expressivo passa a ser uma exigência, quase que um requisito não funcional. Vamos considerar o código abaixo, tente lê-lo você mesmo:

\[sourcecode language="java"\] public void imprimirEstatisticasDeVenda(Vendedor vendedor) { BigDecimal total = BigDecimal.ZERO; BigDecimal comissao = BigDecimal.ZERO; for (Venda venda : vendedor.getVendas()) { total = total.add(venda.getQuantidade().multiply(venda.getPreco())); if(venda.getQuantidade().doubleValue() >= 5) { if(venda.getPreco().doubleValue() >= 5) { comissao = comissao.add(venda.getQuantidade(). multiply(venda.getPreco()).multiply(new BigDecimal(0.05))); venda.priorizarDespache(); } } } System.out.println(vendedor.getNome() + " - Total arrecadado: " + df.format(total) + " - Comissao: " + df.format(comissao)); } \[/sourcecode\]

Se analisarmos linha a linha, veremos que este código calcula o total arrecadado a partir das vendas, o total de comissão que deve ser pago ao vendedor que efetuou as vendas, e finalmente prioriza aquelas vendas que tiveram quantidade e preço maior que cinco.

Ele poderia ser melhorado? Sim, de diversas formas: poderíamos ter extraído varias partes desse código para métodos menores e com nomes significativos para torná-lo mais limpo. Outra opção é buscar por bibliotecas ou criar [suas próprias](https://blog.caelum.com.br/domain-specific-languages-em-acao/) [DSL's](http://martinfowler.com/bliki/DomainSpecificLanguage.html).

Para não esperar as [closures](https://blog.caelum.com.br/trabalhando-com-closures-no-java-8/) do [Java 8](http://www.infoq.com/br/news/2011/12/java8-lambda), conseguimos um pouco de programação funcional utilizando a biblioteca [LambdaJ](http://code.google.com/p/lambdaj/), dando um toque de [cálculo de lambda](https://blog.caelum.com.br/comecando-com-o-calculo-lambda-e-a-programacao-funcional-de-verdade/) ao Java. Ao reesrever o código utilizando essa biblioteca, temos algo mais enxuto:

\[sourcecode language="java"\] public void imprimirEstatisticasDeVenda(Vendedor vendedor) { List<Venda> vendas = vendedor.getVendas(); BigDecimal totalArrecadado = sumFrom(vendas).getValorDaVenda();

List<Venda> vendasComissionaveis = select(vendas, comQuantidadeMaiorOuIgualA(5).and(comPrecoMaiorOuIgualA(5))); forEach(vendasComissionaveis).priorizarDespache();

BigDecimal comissao = sumFrom(vendasComissionaveis) .getValorDaVenda().multiply(new BigDecimal(0.05));

System.out.println(vendedor.getNome() + " - Total arrecadado: " + df.format(totalArrecadado) + " - Comissao: " + df.format(comissao)); } \[/sourcecode\]

Seria esse um código mais expressivo? Consideramos que sim, mas alguns vão criticar que estamos escondendo uma certa complexidade do sistema, que deveria estar a vista dos programadores. E a performance? O LambdaJ tem suas inicializações e objetos temporários, sendo [um pouco mais lento](http://code.google.com/p/lambdaj/wiki/PerformanceAnalysis "LambdaJ") que a versão anterior, podendo trazer problemas em laços. Há também o problema de ser uma biblioteca de fora da API padrão, e os desenvolvedores precisam de um tempo, mesmo que curto, para se adaptar às novas ideias trazidas por ela. Se você já cursou o FJ-16, [pode conferir a modelagem em Scala](https://blog.caelum.com.br/modelando-as-classes-do-fj-16-em-scala/), mais funcional. 

Expressividade de código derruba alguns [conceitos que aprendemos](https://blog.caelum.com.br/design-patterns-um-mau-sinal/), principalmente sobre [Design Patterns](http://www.guj.com.br/java/101563-o-que-leva-as-pessoas-a-criar-vos-anemicas#548092). Afinal, o que o seu cliente entende: [Usar um BO para alterar o VO](http://fragmental.com.br/wiki/index.php/Evitando_VOs_e_BOs.html) e persistir usando o DAO no MYSQL ou "[salvar cliente](http://domaindrivendesign.org/books)  no [repositório de cadastros](https://blog.caelum.com.br/repository-seu-modelo-mais-orientado-a-objeto/)"?
