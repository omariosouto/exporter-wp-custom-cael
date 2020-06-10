---
title: "Como medir a coesão do seu código? A métrica LCOM"
date: "2013-01-23"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Aqui no blog da Caelum já discutimos métricas como [complexidade ciclomática](https://blog.caelum.com.br/medindo-a-complexidade-do-seu-codigo/) e [acoplamento](https://blog.caelum.com.br/orientacao-a-objetos-uma-outra-perspectiva-sobre-o-acoplamento/), e chegamos em maneiras quantitativas de medi-las. Agora, uma boa pergunta é: como medir coesão? Mais claramente, como chegar em um número que diz o quão coesa uma classe é?

Vamos estudar novamente o que é coesão. Sabemos que uma classe deve ser simples, enxuta. Quanto mais enxuta, mais facil será a sua manutenção. E, para que uma classe seja enxuta, ela não pode fazer coisas demais (ter responsabilidades demais). Se cada classe tiver apenas uma única e exclusiva responsabilidade, a manutenção do sistema se torna fácil: se uma regra de nota fiscal mudar, mudamos na classe `NotaFiscal`; se uma regra de imposto mudar, mudamos na classe `Imposto`.

E isso é coesão. **Uma classe coesa é aquela que tem apenas uma única responsabilidade. Uma classe pouco coesa é aquela que tem muitas responsabilidades**, como [definido por Bob Martin](http://www.butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod).

Apesar da teoria ser simples, discutir se uma classe é coesa não é. O que é "uma única responsabilidade"? _Emitir uma Nota Fiscal_ e _Gerenciar Nota Fiscais_ podem ser consideradas uma única responsabilidade, sob pontos de vista diferentes. Ou seja, olhar uma classe e dizer se a mesma é coesa ou não pode ser complicado. Imagine agora tentar calcular, de forma automática, a coesão de uma classe?

Sim, existem algumas métricas que tentam medir a coesão de uma classe. A mais famosa delas é conhecida por **LCOM (Lack of Cohesion of Methods)** ou, em português, **Falta de Coesão dos Métodos**. Existem várias versões dessa métricas, mas a ideia geral de todas elas é mais ou menos a mesma. Qual é?

Uma classe é composta por métodos e atributos. Esses métodos tendem a fazer alterações nos valores dos atributos das classes (essa é a ideia da OO, certo? A classe sendo responsável por manter/alterar seu estado). Sabendo disso, a métrica descobre quantos atributos são modificados por quantos métodos, de maneira independente.

Imagine uma classe abaixo com 2 métodos (A, B) e 4 atributos (X, Y, Z, K). O método A modifica os atributos X e Y. O método B modifica os atributos Z e K. Pelo desenho abaixo, conseguimos ver que existem "2 conjuntos diferentes" nessa classe: A modifica uma parte da classe, e B, outra. Para a métrica LCOM, essa classe possui 2 responsabilidades.

[![coesao](https://blog.caelum.com.br/wp-content/uploads/2013/01/coesao-300x103.png)](https://blog.caelum.com.br/como-medir-a-coesao-lcom/coesao/)

No fim, a métrica mede quanto uma classe não é coesa. **Ou seja, quanto maior o número que a métrica LCOM apontar, menos coesa é a classe**.

Apesar de ser uma solução bastante inteligente, veja que isso é apenas uma heurística. Essa classe pode ou não pode ser coesa. Por exemplo, no mundo Java, os _getters_ e _setters_ podem enganar o valor dessa métrica. Por esse e outros motivos, a academia, de maneira geral, não é muito fã dessa métrica.

Na prática, a ideia é você, como desenvolvedor, avaliar o resultado da métrica e ver se realmente faz sentido. Uma boa dica é buscar pelas classes que apresentam uma LCOM muito alta; essas provavelmente possuem alguma característica estranha.

Neste post, tentei mostrar como é possível medir coesão de classes, mesmo que seja de maneira bem abstrata e suscetível a falhas. Para calcular, você pode usar ferramentas como Eclipse Metrics ou Sonar. Se quiser ver a fórmula correta que é utilizada na LCOM HS (versão mais popular e aceita da métrica), você pode ver [aqui](http://www.computing.dcu.ie/~renaat/ca421/LCOM.html). Se você quer aprender mais sobre orientação a objetos e boas práticas, veja [nossos cursos online](http://www.caelum.com.br/online) sobre o assunto.
