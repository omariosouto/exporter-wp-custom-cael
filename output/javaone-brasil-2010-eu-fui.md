---
title: "JavaOne Brasil 2010: eu fui!"
date: "2010-12-13"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Nos dias 7 a 9 de dezembro aconteceu em São Paulo o **Oracle Open World** que, dessa, vez teve o acréscimo do **JavaOne**. Foi o primeiro JavaOne no Brasil, evento que ocorre anualmente em São Francisco desde 1996.

As últimas semanas foram bastante agitadas: desde [a aprovação do Java SE 7 e 8](http://www.guj.com.br/posts/list/226894.java) até [a saída da Apache do JCP](http://www.guj.com.br/posts/list/227179.java). As novas versões do Java foram amplamente discutidas nas palestras, já a saída da Apache e afastamento do Google eram assuntos no lobby, almoços e happy hours. O evento foi organizado pela Oracle junto ao seu conhecido OpenWorld e depois de um segundo dia de palestras com as salas superlotadas, a organização ouviu o público e conseguiu mover algumas palestras para o auditório principal além de reapresentar outras. A Oracle também se demonstrou bastante aberta a conversar com a comunidade e grupos de usuários, surpreendendo a muitos.

\[gallery link="file" columns="3"\] #gallery-1 .gallery-item {width: 25%;}

Fotos de @loiane: http://www.flickr.com/photos/loiane

Sobre o evento, a pergunta que todos queriam fazer era sobre o futuro do Java e quais seriam as intervenções da Oracle para dar continuidade à plataforma. Algumas pessoas da comunidade Java ficaram receosas pós processo de aquisição da Sun pela Oracle, assim como o pai da tecnologia [James Gosling, que discorreu sobre alguns tópicos em uma forma muito bem humorada recentemente](http://www.youtube.com/watch?v=9ei-rbULWoA). E acredito que a maioria dos participantes do evento se surpreenderam com o tratamento que a empresa está demonstrando com a tecnologia. Todos os sistemas da empresa são baseados em Java, logo a possibilidade deles estragarem com algo diminuiu bastante.

Os keynotes do primeiro dia retrataram bem o que seria o evento, uma cobertura completa das plataformas SE, EE e, por incrível que pareça, a ME. Muitos notaram uma preocupação da Oracle com o ambiente Java ME, quase que calando um pouco os críticos que já a taxavam como morta. Infelizmente, para quem desejava saber um pouco mais sobre o posicionamento da Oracle em relação ao Android (Dalvik) e ao processo contra a Google, não foi possível, seus funcionários não podiam nem responder perguntas sobre o assunto.

Adam Messinger, vice-presidente  e responsável pelos investimentos na tecnologia, deixou bem claro a ideia que já vem batida há algum tempo: Java como plataforma e não somente como linguagem. O VP deixou claro que algumas evoluções na tecnologia vieram não para melhorar o desempenho da linguagem Java, mas sim para melhorar a performance de linguagens dinâmicas que rodam na JVM, citando o [bytecode invokedynamic](http://www.jcp.org/en/jsr/detail?id=292) como exemplo.

Danny Coward foi o representante do ambiente SE. Sim, o [Java SE 7](http://openjdk.java.net/projects/jdk7/features/) vai ser lançando em julho de 2011 e, posteriormente, o Java SE 8, em julho de 2012. Essa divisão veio pois se eles fossem implementar tudo que estava proposto para a versão 7 iria demorar mais ainda.

Sobre as alterações, Danny ressaltou sobre o [Project Coin](http://openjdk.java.net/projects/coin/), Multi-catch, [DaVinci Machine](http://www.jcp.org/en/jsr/detail?id=292), modularização e por fim ele até fez uma wish-list do que ele gostaria que fosse mudado na plataforma futuramente. O foco das mudanças para a versão 7 é manter o código mais limpo e consequentemente torná-lo mais fácil de ler.

Uma das pequenas mudanças que já ajuda muito é o operador diamante, que possibilita escrever instanciações com generics de maneira mais curta com inferência do tipo no lado direito da expressão:

\[java\] Map<Integer, List<String>> meuMap = new HashMap<>(); \[/java\]

Outra novidade é o novo try que trata de fechar os recursos de maneira segura e garantida. Um uso bem comum é com java.io:

\[java\] try (InputStream file = new FileInputStream(new File("input.txt")); OutputStream output = new FileOutputStream (new File("output.txt")); ) {

//seu código aqui } \[/java\]

Pronto, esse código já vai tentar fechar corretamente todos os seus recursos. Caso queira que o Java feche algum recurso de algum objeto que você criou, basta que sua classe implemente a interface `java.lang.AutoClosable`.

Dentre os elementos da sua wish-list, o mais curioso talvez seja uma implementação `default` para as interfaces. Isso evitaria a quebra de contratos e ajudaria a colocar métodos em uma interface sem quebrar as classes que a implementam, mas muitos consideram uma forma de herança múltipla:

\[java\] public interface Collection<E> extends Iterable<E>{ public T filter(Predicate p) default Collections.filter(); } \[/java\]

Por fim, mencionou também o projeto [Lambda](http://www.jcp.org/en/jsr/detail?id=335), que infelizmente vai ficar para o Java 8.

Arun Gupta foi o representante do ambiente Java EE, mostrando algumas alterações no GlassFish 3.1, que agora incluirá suporte a cluster. A propósito, vale ressaltar o tempo curtíssimo de redeploy do GlassFish. Alguns exemplos em outras palestras envolviam colocar EJB em uma Servlet, e o tempo de atualização chegou a ser ínfimo.

Gupta ganhou a simpatia de todos, era um dos únicos que não usava terno e participou pacientemente de todas as discussões. Inclusive uma que aconteceu no segundo dia sobre frameworks web. Ele defendeu o uso de JSF 2 com CDI, mas não fechou as portas para outras tecnologias como Wicket, Spring e VRaptor. Salientou também a maturidade do GlassFish, já que a versão 6 já está há 1 ano no mercado e o GlassFish continua sendo o único servidor conhecido a ter full compliance.

Vimos também menções ao JSF 2.1, 2.2 e JPA 2.1, inclusive ao ambiente Java EE 7 e 8 com inclusão de WebSockets e uma melhoria também no JMS, esse que não recebe uma atualização faz um bom tempo.

Haverá também uma reformulação ainda não definida para a API que trata de REST - JAX-RS (definitivamente o assunto da moda). Inclusive o VRaptor foi citado como referência para como a nova versão da tecnologia deveria ser encaminhada. Conventions Over Configuration e uma programação baseada em Objetos ao invés de String e Annotations são os principais focos.

Outro ponto interessante de ser salientado foi a postura da Oracle com relação as IDE's para desenvolvimento. Para quem estava acostumado a ouvir só sobre NetBeans nos tempos de Sun, agora a situação foi diferente. Os live demos, principalmente quando se tratava de ambiente EE, eram feitos no Eclipse, InteliJ e NetBeans. Indubitavelmente uma postura a ser admirada. Além disso, com o recente lançamento do Netbeans 7 beta, podemos ver que a Oracle continua a investir na ferramenta.

Jerome Dochez falou sobre a API mais interessante do Java EE 6, o [CDI](http://jcp.org/en/jsr/detail?id=299). Ele explicou elementos como `@Produces`, `@Typed`, `@Named`, `@Model`, `@Inject` e `@Stereotype`. Todos eles abordados [no nosso curso FJ-26](http://www.caelum.com.br/curso/fj-26-java-web-jsf2-cdi/).

Greg Bollela falou sobre o ambiente ME, uma frase do Adam Messinger foi: "_We wanna bring back Java roots, write once, run anywhere_". Na palestra falou-se sobre dispositivos móveis e toda uma reformulação que está sendo feita em sua API. O [lwuit](http://www.java.net/forums/mobile-embedded/lwuit) foi um dos mais citados.

Foi um evento que tranquilizou muitos em relação ao futuro da tecnologia e mais uma vez mostrou a força que tem comunidade Java, que há tempos saltou de focar na linguagem para abordar mais firmemente a plataforma. Essa visão multilinguagem da plataforma é algo que temos visto na comunidade há tempos com iniciativas como o [ProgramadorPoliglota](http://www.programadorpoliglota.com.br) e eventos como o [QCon](http://www.qconsp.com), mas é ótimo ver a Oracle caminhando também nessa direção. Acompanhemos os próximos passos da plataforma Java e até o JavaOne Brasil 2011!
