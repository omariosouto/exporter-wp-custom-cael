---
title: "Domain-Driven Design no Falando em Java 2008"
date: "2008-05-27"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

No [Falando em Java 2008](https://blog.caelum.com.br/2008/05/20/falando-em-java-2008-eu-fui/), apresentei uma palestra introdutória sobre [Domain-Driven Design](http://www.domaindrivendesign.org/). Apesar do tempo curto, os comentários foram ótimos! Muito obrigado a todos os que comentaram: pessoas no evento, blogs e GUJ. Falar de DDD em 40 min foi meu maior desafio e acabou faltando um pouquinho de tempo no final, mas deu para passar a mensagem.

### Domain e Ubiquitous Language

O ponto fundamental do DDD é o primeiro D, o **Domain**. Tudo gira em torno desse tal de Domínio. O domínio é, em poucas palavras, o problema que queremos resolver com o programa que estamos desenvolvendo. _Alguém_ (um cliente) tem um _problema_ na área de atuação dele (geralmente nada a ver com informática) e contrata uma equipe de programação para ajudá-lo (nós :).

Segundo o DDD, é **impossível** resolver esse problema satisfatoriamente sem entender direito o que acontece no domínio do cliente. Não basta os desenvolvedores saberem mais ou menos: é necessário entrar fundo no domínio do cliente.

Mas é claro que nosso objetivo não é se tornar um especialista completo na área do cliente, mas apenas compreendê-la. A palavra-chave para isso acontecer é **Conversa**. Conversa constante e profunda entre os especialistas de domínio e os desenvolvedores.

Aqueles que conhecem o domínio em detalhes devem conversar com aqueles que conhecem programação em detalhes. Juntos, tentarão chegar a uma **língua comum** em que todos consigam se entender e que será usada em todas as conversas. É o que o DDD chama de **Ubiquitous Language**: uma língua baseada nos termos do domínio, não totalmente aprofundada neste, mas suficiente para descrever o problema satisfatoriamente.

### Construção do Domain Model

Durante a conversa constante, todos juntos chegarão a um consenso sobre o Domínio. Os especialistas de domínio eventualmente criarão simplificações para facilitar a conversa; e os desenvolvedores podem introduzir conceitos técnicos simples.

Com isso, todos criam um _modelo do domínio_. É uma abstração do problema real, desenvolvida em conjuntos pelos especialistas do domínio e desenvolvedores. No DDD, é chamado de **Domain Model**.

**É esse modelo que os desenvolvedores vão implementar em código**. Literalmente. Item por item, como foi acordado por todos. Será desenvolvido um código limpo, com palavras do domínio, que representa, na programação, o domínio em discussão.

Usando DDD, seu programa orientado a objetos deve expressar a riqueza do domain model. Qualquer mudança no modelo (e, acredite, isso é muito comum) deve ser refletida imediatamente no código. Se algo do modelo torna-se inviável de se implementar tecnicamente, não se faz um "ajustezinho" no código; o modelo deve ser mudado para ser mais fácil de se implementar.

Ou seja, **sempre seu código será expressão do modelo**, que por sua vez é baseado totalmente no domínio.

### Implementando o Domain Model

O DDD define uma série de patterns para facilitar a implementação do modelo em código. **Mas, com absoluta certeza, esse não é o ponto principal do DDD**. São apenas ferramentas que facilitam essa implementação.

Na palestra, mostrei alguns patterns de forma bem simples e rápida, como Entity e [Value Object](http://martinfowler.com/bliki/ValueObject.html). E mostrei o tão discutido, debatido e mal-compreendido [Repository](http://martinfowler.com/eaaCatalog/repository.html).

O cliente descreve ao desenvolvedor o seguinte problema: _"preciso saber todos os peixes que são da cor azul"_. (na palestra, usei o exemplo de uma loja de peixes) Para o cliente, é natural em seu domínio, que se consiga "buscar" coisas. A idéia é recuperar "objetos" do domínio (entities) previamente conhecidos, baseado eventualmente em algum critério.

A noção de **repositório** surge justo dessa necessidade: chegar nos objetos de conhecimento do domínio. Na palestra, eu levantei a questão de que o nome repositório não deve ser algo interno ao código, mas **deve fazer parte da Ubiquitous Language**, deve aparecer nas conversas e no Domain Model. Ou seja, repositório deve ser um conceito que o especialista de domínio também entende e, por que está no Model, é ele que vai para o código.

Não há problema em trazer palavriado técnico para a Ubiquitous Language, desde que o príncipio da UL seja mantida: todos entendem o conceito. E, se, eventualmente, no contexto do domínio sendo tratado, outro nome faça mais sentido que repositório, esse nome deve ser usado (mesmo que nós técnicos saibamos que no fundo aquilo é um repositório).

**Repositório como interface? Classe concreta delegando? DAO implementa Repository?** Tanto faz. Um outro ponto fundamental do DDD é: nada tem resposta definitiva. Se você entende a questão toda do Domain Model e aplica essa noção na programação, pode usar diversas formas diferentes de implementar tudo isso.

Na palestra, eu representei o Repository como uma interface dentro do Model. E a implementação (que, do ponto de vista do DDD, _não importa_) era um DAO com Hibernate na camada de infraestrutura.

### Concluindo

Meu ponto principal na palestra foi mostrar a Ubiquitous Language e o Domain Model, que são o coração do DDD. Vou escrever um segundo artigo com códigos e mais comentários da palestra, mas paro esse artigo gigante por aqui.

Termino [linkando para um excelente post do Philip Calçado](http://blog.fragmental.com.br/2008/05/22/domain-driven-design-e-simples/) que ele publicou essa semana (parece até que combinamos) sobre DDD falando justo que o que conta no DDD é o Domínio e não os Patterns. Ele conta uma historinha de um projeto onde todos "entendiam" DDD, usavam Repositórios, Entities etc, mas infelizmente não falavam a mesma língua do domínio.
