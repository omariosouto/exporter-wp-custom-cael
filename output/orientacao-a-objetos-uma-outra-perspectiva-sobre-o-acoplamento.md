---
title: "Orientação a objetos: uma outra perspectiva sobre o acoplamento"
date: "2012-10-24"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Todo mundo que já viu orientação a objetos alguma vez na vida conhece a frase "**classes devem ter alta coesão e baixo acoplamento**". Uma classe altamente acoplada pode ser sim um problema. Veja o diagrama abaixo, por exemplo, onde temos a classe _GerenciadorDeNotaFiscal_, que depende de outras 3 classes: um DAO, uma classe que envia e-mail e uma classe que se comunica com um serviço externo da empresa.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/10/acoplamento-1-300x166.png "acoplamento-1")](https://blog.caelum.com.br/wp-content/uploads/2012/10/acoplamento-1.png)

Acoplar-se a uma classe significa depender dela. E o problema de depender de outras classes é que, caso elas mudem, a classe principal pode sofrer mudanças também. No diagrama acima, se a classe Dao mudar sua interface ou mesmo o que ela faz, isso impactará na classe _GerenciadorDeNotaFiscal_. O mesmo aconteceria se as classes _ServiçoExterno_ ou _Email_ mudassem! Ou seja, quanto maior for a quantidade de classes dependentes, maior a chance de uma delas mudar e impactar em mudança na classe principal!

A ideia então é reduzir o acoplamento entre nossas classes o máximo possível. Mas sabemos que é impossível desenvolver sistemas com zero acoplamento. **Mas será que todo e qualquer acoplamento é realmente problemático?**

Por exemplo, a grande maioria dos sistemas em Java no mundo são acoplados com a interface **List** (afinal a necessidade de guardar conjuntos de elementos é presente na maioria das aplicações). E fazer uso dessa interface geralmente não é problema para os desenvolvedores. Esse é um acoplamento "que não ligamos muito". A grande charada é entender a diferença entre o acoplamento com _List_ e com o _DAO_, por exemplo.

**A diferença é que a interface List não muda**! Se ela não muda, isso significa que ela não forçará mudanças na classe que a usa! Ou seja, apesar de estarmos acoplados, o problema é menor, já que ela nunca provocará mudanças em nossos códigos!

Mas porque **List** não muda? Justamente por causa do acoplamento... o "outro lado do acoplamento"! Estamos sempre acostumados a ver o acoplamento como _"a quantidade de classes que uma classe depende"_; isso é conhecido como _acoplamento eferente_. Mas nunca vemos "a quantidade de classes que dependem de uma classe" (conhecido como **acoplamento aferente**).

A interface **List**, por exemplo, não depende de nada; mas muitas classes dependem dela. Veja a quantidade implementações da interface existentes: _AbstractList, AbstractSequentialList, ArrayList, AttributeList, CopyOnWriteArrayList, LinkedList, RoleList, RoleUnresolvedList, Stack, Vector_.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/10/acoplamento-2.png "acoplamento-2")](https://blog.caelum.com.br/wp-content/uploads/2012/10/acoplamento-2.png)

Isso sem contar a quantidade de classes que a referenciam hoje. Se algum engenheiro da Sun alterar essa interface, ele terá um trabalho imenso para propagar a mudança em muitos pontos. Portanto, provavelmente ele não fará isso! Isso torna essa interface **estável**!

_Classes ou interfaces estáveis são aquelas que não tendem a mudar. Acoplar com classes ou interfaces com essa característica é interessante, pois sabemos que a chance dela mudar é baixa._

É por isso que interfaces são tão populares em códigos orientados a objeto. [Interfaces tendem a ser estáveis](http://www.objectmentor.com/resources/articles/stability.pdf), já que não possuem atributos ou implementação (coisas que tendem a mudar muito dentro de uma classe), e possuem muitas implementações embaixo dela (diminuindo a chance de algum programador mexer nela).

Entender bem as duas formas de acoplamento nos dá pontos de vista diferentes sobre a mesma classe. Se uma classe possui acoplamento eferente alto, isso significa que ela depende de muitas classes. E, para saber se esses acoplamentos são problemáticos, você pode medir o acoplamento aferente dessas dependências; se esse número for alto, essa classe tem a alta chance de ser estável, diminuindo o risco do acoplamento.

Evitar acoplamentos perigosos é tarefa importante. Você pode aprender mais sobre isso em nossos cursos online de [Padrões de Projeto](http://www.caelum.com.br/curso/online/design-patterns/), no de [Boas práticas em Orientação a Objetos](http://www.caelum.com.br/curso/online/orientacao-objetos-java/) ou em nosso [livro de Test Driven Development](http://www.casadocodigo.com.br/products/tdd).
