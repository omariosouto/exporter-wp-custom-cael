---
title: "O que é Modelo Anêmico? E por que fugir dele?"
date: "2014-04-03"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

**Pensar em design e arquitetura** é sem dúvida o grande desafio de um desenvolvedor mais experiente. Todos nós já trabalhamos com linguagens orientadas a objetos há algum tempo, e já tivemos tempo suficiente para perceber que, quando a interação entre as classes do sistema não é clara o suficiente, temos diversos problemas: repetição de código, dificuldade de manutenção, propagação de bugs, e etc.

Por esse motivo, é que tanto ouvimos falar sobre diferentes tipos de arquitetura. E com certeza, o termo mais popular sobre isso é a tal da **arquitetura em camadas**. Quem nunca viu o sistema dividido em 3 camadas diferentes, a camada de _modelo_, a camada de _regras de negócio_, e a _camada de acesso a dados_?

A ideia dessa arquitetura é justamente separar as regras de negócio em um canto, do conjunto dos atributos da entidade em outro, do acesso aos dados. E, com isso, facilitar a manutenção. O código abaixo é um exemplo dessa separação:

\[code language="java"\] // camada de modelo class NotaFiscal { private int numero; private double valor; private String razaoSocial; // getters e setters }

// camada de regra de negocios class NotaFiscalBLL { public void encerraNota(NotaFiscal nf) { nf.setEncerrada(true); } }

// camada de acesso a dados class NotaFiscalDAO { public void insere(NotaFiscal nf) { // insert into nota fiscal (...) } } \[/code\]

**O problema é que, na prática, acontece exatamente o contrário!** Veja, no momento que separamos dados de um lado, e comportamento de outro, voltamos a programar de maneira procedural. Nesse tipo de arquitetura, é comum vermos imensas classes com as regras de negócios repetidas e espalhadas. A consequência disso? Um código dificílimo de ser mantido e evoluído. Tanto é que, hoje, esse tipo de arquitetura, é conhecida por **modelo anêmico**.

Muita gente já falou sobre isso: o Paulo Silveira comentou dos [problemas da criação de getters e setters sem qualquer cuidado](https://blog.caelum.com.br/nao-aprender-oo-getters-e-setters/), o Martin Fowler tem um texto só sobre [modelos anêmicos](http://www.martinfowler.com/bliki/AnemicDomainModel.html) no seu site, e o Philip Calçado também já [brigou bastante com os BOs e VOs](http://philcalcado.com/2007/01/02/vo-bo-e-tudo-mais-que-voce-nao-deveria-utilizar/) (nomes que as pessoas do mundo Java dão aos objetos que guardam as regras de negócio e aos que guardam os dados de uma entidade). É, com certeza, um assunto bem popular na comunidade.

**Estamos jogando fora todos os benefícios que a orientação a objetos nos dá!** Não preciso defender aqui as vantagens da OO: reutilização de código, polimorfismo, herança, etc.

Mas se os problemas são claros, por quê as pessoas fazem assim? Por vários motivos diferentes.

- É mais fácil programar de maneira procedural do que de maneira orientada a objetos. Afinal, fazer bom uso de polimorfismo, herança, encapsulamento e abstrações, não é fácil.
- Tanto a Sun quanto a Microsoft já divulgaram "catálogo de boas práticas", onde a arquitetura sugerida era parecida com essa. A diferença é que em ambos os textos, a ideia era discutir arquitetura para sistemas distribuídos. Mesmo nesse caso, esse tipo de arquitetura não é a melhor, mas essa discussão fica para um próximo post.
- Por que, por algum motivo estranho, ter essas camadas dá uma falsa sensação de facilidade de manutenção, afinal "tudo está bem isolado".

**Fuja de modelos anêmicos.** Tenha objetos ricos, isto é, objetos que tenham atributos, e métodos que manipulam esses atributos. Faça uso de abstrações, polimorfismo e herança nos momentos que precisar de flexibilidade. Encapsule tudo para que as mudanças sejam facilmente propagáveis e não haja código espalhado.

Como fazer isso? Orientação a objetos... Não há mágica. Onde aprender? Nos diversos cursos que temos qualidade de código no Alura, como [Orientação a Objetos](http://www.alura.com.br/cursos-online-java/orientacao-objetos-java), [refatoração de código](http://www.alura.com.br/cursos-online-java/refatoracao-de-codigo), e [TDD](http://www.alura.com.br/cursos-online-agile/tdd).
