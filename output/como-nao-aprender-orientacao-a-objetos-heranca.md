---
title: "Como não aprender orientação a objetos: Herança"
date: "2006-10-15"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Já falei sobre [os problemas que os getters e setters podem trazer](https://blog.caelum.com.br/nao-aprender-oo-getters-e-setters/), caso usados de maneira indiscriminada. A bola da vez é a herança.

Lembro quando comecei a programar profissionalmente em Java, no final de 2000. Estava em um projeto juntamente com o [Tiago Silveira](http://jroller.com/page/dukejeffrie), e algo que eu adorava fazer era que meus beans estendessem de `HashMap` quando tivessem propriedades dinâmicas. O Tiago vivia me criticando, dizendo que eu deveria **ter um** `HashMap` e não **ser um** `HashMap`. Eu não entendi o porquê de ter muito mais trabalho para escrever métodos que apenas delegariam, em vez de já herdar tudo! Tão simples, e herança parecia se encaixar tão bem nesse caso.

Depois de um tempo comecei a sofrer bastante: tipos errados eram passados como argumento, minha classe possuia métodos que eu não queria e também nao era para tratá-la como um mapa. Seria o eu o único a cometer essa atrocidade apenas para economizar um pouquinho de código? E a resposta vem do java 1.0: \[java\] class Stack extends Vector class Properties extends Hashtable \[/java\]

Claramente um `Properties` não deveria ser um `Hashtable`, afinal ela não mapeia objetos a objetos, mas acaba nos fornecendo um método `put(Object, Object)`, que se usarmos sem passar Strings vai causar problemas. Como resolver isso depois que já nos comprometemos com a herança? A única solução é colocar um [aviso grande no javadoc para ninguém usar determinados métodos herdados](http://java.sun.com/j2se/1.4.2/docs/api/java/util/Properties.html)! O mesmo vale para a `Stack` e o `Vector`. Uma `Stack` não é um `Vector`, definitivamente. Se você pensar direitinho, uma pilha tem comportamento **oposto** da implementação da `Vector`!

Se um gato possui `raça` e `patas`, e um cachorro possui `raça`, `patas` e `tipoDoPelo`, logo `Cachorro extends Gato`? Pode parecer engraçado, mas é o **mesmo** caso que os anteriores: herança por preguiça, por comodismo, por que vai dar uma ajudinha. A relação "**é um**" não se encaixa aqui, e vai nos gerar problemas.

Em vários itens do livro _Effective Java_, Joshua Bloch cita o uso de heraça e de membros _package-default_ e _protected_. O item 12 diz "_minimize o acesso a suas classes e membros_", o item 15 diz "_desenhe suas classes pensando no uso de herança, caso contrário proíba-a_" e o item 16 "_prefira interfaces a classes abstratas_".

Mas sem dúvida o principal é o item 14: "_prefira composição em vez de herança_", onde Joshua Bloch diz com todas as letras que **herança quebra o encapsulamento**. E isso não é novidade, essa conclusão é atribuída a Alan Snyder, no artigo entitulado [Encapsulation and Inheritance in Object-Oriented Programming Languages](http://citeseer.ist.psu.edu/513482.html), que data de 1986! Faz incríveis 20 anos que alguém percebeu que "_... na maioria das linguagens a introdução da herança compromete seriamente os benefícios do encapsulamento ..._" (traduzido livremente do abstract do artigo citado). Se você preferir uma opinião mais atual, pode ler [esse post](http://martinfowler.com/bliki/DesignedInheritance.html) do [Martin Fowler](http://martinfowler.com/), que tem menos de um mês.

No livro, Joshua Bloch da como exemplo a criação de uma classe filha de `HashSet`, e mostra que, sem conhecer profundamente o código fonte da classe mãe, fica impossível de que essa classe filha funcione corretamente ao reescrever um método que é aparentemente inofensivo. A partir do momento que você precisa conhecer o código fonte da sua mãe, você quebrou o encapsulamento. Mais ainda: quando a classe mãe precisar sofrer alguma modificação, o desenvolvedor precisa estar ciente que pode quebrar o funcionamento de várias classes filhas, que pressupunham determinado comportamento interno. Leitura recomendadíssima.

Existem outros exemplos mais sutis de mau uso de herança. Um deles é quando criamos um DAO que é mãe de todos os DAOs. Nesse meu [post sobre DAO genérico](https://blog.caelum.com.br/2006/08/26/ei-como-e-o-seu-dao-ele-e-tao-abstraido-quanto-o-meu/) o Fábio Kung fez um bom comentário sobre isso, onde usamos herança apenas com o intuito de economizar meia dúzia de linhas, sendo que a relação "**é um**" não existe.

O outro exemplo é `HttpServlet` e sua própria `Servlet`. Se você já estendeu de `HttpServlet` sentiu o problema do método `service`. Acontece que a implementação padrão do método `service` em `HttpServlet` chama o método `doGet`, `doPost`, `doPut`, etc de acordo com o método _http_ utilizado. Esses métodos possuem uma implementação padrão que lançam uma exception. Se você sobrescreve o método `service` de uma serlvet , você não pode chamar o `super.service`, pois caso o chame, ele chamará o `do` correspondente, jogando uma exception. Portanto jamais chame `super.service`. Já no caso dos métodos `init(ServletConfig)` e `destroy`, por exemplo, você **deve** chamar o `super` pois não sabemos se a sua classe pai tem algum recurso a ser iniciado e/ou liberado. Sendo assim, em alguns casos você deve, e em outros não deve chamar o `super`, e você só vai saber disso quando conhecer o código fonte de sua classe pai `HttpServlet` (ou ler a documentação sobre esse comportamento estranho), **quebrando o princípio de encapsulamento**. É fácil de reconhecer classes que apresentam esse mesmo problema: o javadoc delas costuma detalhar bastante como o método funciona, e muitas vezes até mostrar o código fonte deles!! Procure-as no jdk, elas não são poucas.

Quem conhece um pouco a API de servlets sabe que eles tentaram resolver o problema adicionando um outro método `init`, que não recebe parâmetro, e esse sim deve ser reescrito e não precisamos chamar o `super`. Em outras palavras, uma gambiarra.

Aqui cabe lembrar o que o Joshua Bloch falou: "_Crie suas classes pensando em herança, ou então proiba-a_". Parece que `HttpServlet` não cai nesse caso... Minha proposta para a `HttpServlet` seria bem simples: você poderia implementar alguma interface, digamos `PostProcessor`, `GetProcessor`, entre outras, e registra-la na `HttpServlet` (um método `setPostProcessor`, ou passando para o construtor, ou ainda no XML). A interface `PostProcessor` poderia ser parecida com:

\[java\] public interface PostProcessor { void init(ServletConfig config); void doPost(HttpServletRequest request, HttpServletResponse response); void destroy(); } \[/java\]

Se sua classe quisesse aceitar tanto `GET` quanto `POST`, bastaria implementar ambas interfaces. Interfaces! O caminho é desse lado.

Repare que sempre podemos substituir herança através de um refatoramento simples: extração de uma interface com os métodos herdados, somado a criação de uma implementação que simplesmente delega esses métodos para a antiga classe mãe. Quando então usar herança? Essa é uma questão difícil. Na minha visão particular, a resposta seria um enfático "quase nunca". Creio que a resposta aqui fique um pouco a critério de cada desenvolvedor, mas sempre com muita cautela!
