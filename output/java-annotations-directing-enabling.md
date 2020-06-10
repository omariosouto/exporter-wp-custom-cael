---
title: "Java Annotations: directing, enabling?"
date: "2008-09-22"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Há algum tempo, quando [trabalhava na Alemanha](http://www.commworld.de), costumava ter diversas conversas e discussões extremamente produtivas com um grande amigo e desenvolvedor, [Tiago Silveira](http://www.jroller.com/dukejeffrie/). Como não poderia deixar de ser, desenvolvimento de software era o preferido entre diversos assuntos interessantes.

Em uma de nossas conversas, falávamos sobre o uso de anotações em Java. A discussão foi tão produtiva, que chegamos a concordar que o uso de anotações pode ser encarado como [atitude com relação ao desenvolvimento de software](http://martinfowler.com/bliki/SoftwareDevelopmentAttitude.html)!

Foi o próprio Tiago que começou a falar sobre [enabling](http://martinfowler.com/bliki/EnablingAttitude.html) vs [directing](http://martinfowler.com/bliki/DirectingAttitude.html) e começamos a fazer algumas comparações com o uso de anotações em projetos famosos.

Anotações são apenas metadados (ou [DecorativeData](http://www.softwarereality.com/programming/annotations.jsp), como alguns podem preferir); informações extras sobre o código que está escrito. Eu costumo sempre fazer analogia com um post-it pendurado no meio do seu código fonte: **anotações não executam código**.

Na minha opinião, o bom uso de anotações segue o estilo _"Enabling"_. Não te direcionam para nenhum caminho, não indicam a execução de nenhum código. Pelo contrário, como metadados, apenas possibilitam diversos usos desta informação extra. Um bom exemplo seria a anotação `@Entity`, da Java Persistence API:

\[java\] @Entity public class Carro { // ... } \[/java\]

Diversos frameworks e até outras partes do seu sistema podem usar esta informação extra como bem entenderem. O próprio hibernate usa esta informação para saber que se trata de uma classe persistente, que deve ter representação no banco relacional, um validador poderia usar esta informação para decidir que objetos desta classe devem ser validados, um framework de aspectos poderia decidir aplicar um aspecto nesta classe baseado nesta informação extra, entre diversas outras possibilidades. Um outro exemplo interessante poderia ser a anotação `@Transient`, da própria especificação JPA:

\[java\] public class Bicicleta { @Transient private double velocity; } \[/java\]

Você poderia usar esta informação para decidir não mostrar este campo na interface gráfica (web ou desktop). Pode usar esta informação extra até para decidir que este campo não será enviado em emails, nem incluso em logs. Frameworks de ORM como o hibernate aproveitam esta informação para ignorar o atributo e não incluí-lo em nenhuma tarefa de persistência.

Poderia dar mais inúmeros exemplos de bons usos de anotações. O mais importante é a idéia de que a anotação possibilita diversos usos e não **direciona** - não **força** - nenhum caminho. A anotação não está associada a nenhuma execução de código. Funciona mesmo como um simples post-it.

A **atitude** alternativa seria o pensamento _"Directing"_. Neste caso, a anotação está diretamente relacionada a execução de algum código e serve apenas para este fim. Exemplo:

\[java\] public class Sistema {

@EnviaEmail(Emails.CONFIRMACAO) public void cadastra(Usuario usuario) { //... }

} \[/java\]

Neste caso não há outro uso para esta anotação. Perceba que ela já pressupõe a execução do código de envio de emails. Te **limita** a um pensamento particular, para um uso específico. Talvez seja um caso exagerado, mas não há diferença alguma com a chamada direta do método que envia o email:

\[java\] public class Sistema { public void cadastra(Usuario usuario) { mensageiro.enviaEmail(Emails.CONFIRMACAO, usuario); // ... } } \[/java\]

Em alguns casos pode ser bastante difícil, ou até subjetivo demais, decidir se o uso de uma anotação é _directing_ ou _enabling_. Não acho que devemos tomar nada como verdade absoluta; o importante é ter consciência dos diversos pontos de vista diferentes. Assim podemos tomar as nossas decisões, sempre pesando vantagens e desvantagens.

E você, consegue lembrar de algum outro uso interessante de anotações? _Enabling_ ou _Directing_?
