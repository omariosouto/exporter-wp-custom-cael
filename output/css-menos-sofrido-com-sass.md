---
title: "CSS menos sofrido com Sass"
date: "2015-11-04"
author: "natan.souza"
authorEmail: "natan.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Artigo para quem não conhece ou estava adiando os estudos dessa poderosíssima linguagem pré-processador. Mexa com **Sass** (no Windows também) e seja mais produtivo! Nesse post abordo rapidamente duas features dessa linguagem e algumas perguntas que já me fizeram nas aulas que ministro aqui na Caelum.

O **Sass** é uma linguagem baseada em CSS (opa, aí já facilita, não é mesmo?) que depois de compilada gera o bom e velho CSS.

Possui duas sintaxes diferentes, o SASS e o SCSS. Essa última é a que acho mais bacana pela semelhança com CSS normal, a outra é atrelada a indentação.

 

Duas coisas bacanas que o Sass possui, que você pode ir testando no [SassMeister](http://sassmeister.com):

## Variáveis

Sabe quando você repete a mesma cor no seu CSS umas 500 vezes, aí depois precisa mudar, aí tem que ficar procurando ou dar um _Replace_? Seria bom se o CSS tivesse como criar variável, atualmente não é possível, mas no Sass é!

Código em Scss:

\[code language="css"\] // Crio a variável $cor-padrao: #069; .btn-primario { // aqui eu chamo a variável: background: $cor-padrao; } .texto-principal { // chamo aqui também: color: $cor-padrao; } \[/code\]

Código compilado, já em CSS:

\[code language="css"\] .btn-primario { background: #069; }

.texto-principal { color: #069; } \[/code\]

E para quem reparou, sim, no Sass dá para você comentar com //, mas esse comentário não vai para o CSS final.

## Reutilizando código com Mixins

Tem aquele trecho de código que é repetido no CSS inteiro, aí se você precisar colocar uma declaração a mais, teria que alterar em vários lugares.

\[code language="css"\] /\* CSS normal \*/ .desfoque { -webkit-filter: blur(20px); filter: blur(20px); } \[/code\]

No Sass você pode reutilizar esse código em vários lugares **criando um mixin**:

\[code language="css"\] // Scss

// Crio um mixin com o '@' e dou um nome para ele : @mixin desfoque { -webkit-filter: blur(20px); filter: blur(20px); } // Agora para incluir : .painel img { @include desfoque; max-width: 100%; } \[/code\]

O CSS compilado fica:

\[code language="css"\] .painel img { -webkit-filter: blur(20px); filter: blur(20px); max-width: 100%; } \[/code\]

 

Agora algumas perguntas e respostas:

 

### Bacana, mas há outros pré-processadores além do Sass?

Sim! Os mais conhecidos além do Sass são o [LESS](http://lesscss.org/) e o [Stylus](https://learnboost.github.io/stylus/). Temos até esse artigo aqui onde o [Sérgio Lopes comenta sobre o LESS](https://blog.caelum.com.br/css-facil-flexivel-e-dinamico-com-less/).

 

### Mas qual é o melhor?

Essa pergunta é meio [polêmica](https://www.youtube.com/watch?v=PORknrd-cv8). Conheço só o Sass e o LESS, mas prefiro o primeiro por achar que é um pouco mais “certinho”. Por exemplo, enquanto no Sass para criar um mixin você usa o `@mixin` e o inclui dando um `@include`, no LESS você cria uma classe CSS comum e chama ela lá meio da sua regra CSS.

 

### Preciso aprender todos?

Diria que se você aprende um, você automaticamente sabe 80% de todos. Então, não. Segura na mão de um deles e segue a vida. Mas acho válido ao menos conhecer os outros.

 

### Besteira, prefiro o LESS

E eu prefiro Windows. São só ferramentas, muita calma, seu cliente ou chefe não se importa com qual ferramenta você utiliza, ele quer é o resultado.

Outro ponto pró-Sass:

![Bootstrap fechando o caixão do LESS](https://blog.caelum.com.br/wp-content/uploads/2055/10/last-nail-in-less-coffin.jpg)

 

O [Bootstrap](http://getbootstrap.com/) na versão 4, atualmente em Alpha, trocou o LESS pelo Sass. Quando anunciaram essa troca, muita gente comentou que esse seria o último prego no caixão do LESS. Acredito que concorrência é sempre bom para todo mundo.

 

### Pré-processador é só isso? Uma maneira mais bacana de escrever CSS?

Sim! Só as variáveis já deixam tudo mais produtivo, aí tem os mixins, extends, placeholder, funções, etc. Tudo lindo.

 

### Sass então deixa meu CSS melhor! Que legal!

_Não!_ O Sass não vai deixar seu CSS melhor ou mais rápido, boas práticas no CSS valem para o Sass também! Coisas como não repetir código e tentar não ficar sobrescrevendo declarações.

 

### Preciso usar Linux ou Mac para trabalhar com Sass?

Também não! Se for interessante um tutorial de **[como trabalhar com Sass no Windows](https://blog.caelum.com.br/tutorial-instalando-usando-sass-no-windows/)** posso criar outro artigo depois. Só dê um "alô" nos comentários. _(Edit: a galera pediu, fizemos!)_

 

### Outros recursos do Sass?

Há muitos recursos interessantes que o Sass possui como:

- Funções de cor;
- Extend;
- Aninhamento de seletores CSS;
- Operações matemáticas;
- Etc.

Existe até um framework chamado [Compass](http://compass-style.org/), que ficou bem conhecido por criar _sprites CSS_ de forma automática, é realmente mágico quando você usa isso na primeira vez.

 

Eu mostro o Compass e alguns desses recursos no **curso online de Sass** no Alura, [dá uma olhada no conteúdo](https://www.alura.com.br/curso-online-sass) do curso depois. Se gostar de ler, escrevi um [livro de Sass](https://www.casadocodigo.com.br/products/livro-sass) em que mostro todos esses recursos também.

Você usa/usava qual pré-processador? Não utiliza nenhum? Por qual motivo? E para você que já trabalha com Sass, prefere qual das sintaxes?
