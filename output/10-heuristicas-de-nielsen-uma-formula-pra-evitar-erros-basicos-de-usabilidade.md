---
title: "10 Heurísticas de Nielsen. Uma fórmula pra evitar erros básicos de usabilidade."
date: "2016-02-02"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Recentemente alteramos o Design da **seção interna** (onde os alunos fazem os cursos) da [Alura](https://www.alura.com.br/), sem criar novas funcionalidades. Mas infelizmente acabamos nos esquecendo de colocar o botão de editar um post no fórum do Alura.

[![Esquecemos do botão editar no forum do Alura.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-5.53.05-PM-1024x454.jpg)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-5.53.05-PM.jpg)

Esquecer do botão de editar não foi um erro só nosso. Eu não me esqueço quando eu estava utilizando o app do Facebook e adicionava um comentário com algum erro. Eu tinha que correr pra versão desktop pra conseguir editar o comentário (tristeza no coração).

Se a gente for pensar, é um erro bem simples de resolver e também de esquecer de implementar. Pensando nesses problemas o **grande pai da usabilidade**, [Mr. Jakob Nielsen](https://www.nngroup.com/people/jakob-nielsen/) criou o que chamamos de: **10 heurísticas de Nielsen**, algo que vemos bastante no [curso de UX presencial da Caelum](https://www.caelum.com.br/curso-ux-usabilidade-mobile-web). Bora entender cada uma delas pra ver se a gente consegue errar um pouco menos. :-)

1. [Visibilidade de qual estado estamos no sistema](#primeiraHeuristica)
2. [Correspondência entre o sistema e o mundo real](#segundaHeuristica)
3. [Liberdade de controle fácil pro usuário](#terceiraHeuristica)
4. [Consistência e padrões](#quartaHeuristica)
5. [Prevenções de erros](#quintaHeuristica)
6. [Reconhecimento em vez de memorização](#sextaHeuristica)
7. [Flexibilidade e eficiência de uso](#setimaHeuristica)
8. [Estética e design minimalista](#oitavaHeuristica)
9. [Ajude os usuários a reconhecerem, diagnosticarem e recuperarem-se de erros](#nonaHeuristica)
10. [Ajuda e documentação](#decimaHeuristica)

## 1\. Visibilidade de qual estado estamos no sistema

É responsabilidade do sistema informar o que está acontecendo em _real time_ pro usuário.

Quando estamos assistindo/ouvindo uma playlist do Youtube, do lado direito fica bem claro: qual vídeo estamos assistindo; qual é próximo; quais assistimos ou não.

[![Exemplo de visisbilidade do sistema](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-6.53.04-PM-1024x347.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-6.53.04-PM.png)

## 2\. Correspondência entre o sistema e o mundo real

Em relação ao **mundo real** podemos considerar: sons, visual e o tom de escrita que usuário utiliza para se comunicar. Implementamos bastante esta [heurística](https://pt.wikipedia.org/wiki/Heur%C3%ADstica) quando utilizamos uma seta, ícones e utilizamos a cor vermelha para elementos negativos.

Um software que usa essa heurística é o Photoshop em sua barra de ferramentas.

[![images.duckduckgo.com](http://blog.caelum.com.br/wp-content/uploads/2016/01/images.duckduckgo.com_.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/images.duckduckgo.com_.png)

## 3\. Liberdade de controle fácil pro usuário

Essa foi a heurística que me motivou a fazer esse post. Nesta heurística, a preocupação é de passar pro usuário a liberdade de ele fazer o que quiser dentro do sistema com exceção das regras que vão contra o negócio ou interferem em outra funcionalidade.

Por exemplo quando criamos um _tweet_ é bacana poder deletá-lo se estivermos afim. Mas não dá pra editar um _tweet_. Imagina se você dá um _retweet_ e depois o usuário que fez o _tweet_ muda o texto pra uma coisa que você não acha legal. Sacanagem né!

[![Exemplo da liberdade de controle do usuário.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-8.15.08-PM.png)](https://twitter.com/olhardigital/status/689511421357363201)

## 4\. Consistência e padrões

É importante manter a consistência e padrão visual (texto, cor, desenho do elemento, som e etc).

Por exemplo, no _fórum_ [GUJ](http://www.guj.com.br/) (Grupo de usuário Java), quando vamos responder um _post_ o botão para enviar a resposta sempre é da mesma cor, tamanho e texto. O elemento para cancelar a resposta sempre tem seus padrões:

[![Exemplo de consistência e padrões.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-8.59.29-PM-1024x241.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-8.59.29-PM.png)

## 5\. Prevenções de erros

Não é uma boa ideia deixar seu usuário errar sem explicar previamente o motivo do erro. Melhor do que isso, tente criar um interface que permite o usuário não errar.

A busca do Google faz isso de uma forma muito inteligente. No momento que começamos escrever nossa busca ele já te entrega algumas sugestões, mesmo se a gente escrever a busca com uma ortografia errada ele realiza a busca e pergunta se estamos procurando outra informação com a ortografia correta.

Realizando uma **busca no Google**:

[![Exemplo de prevenções de erros.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-11.13.36-PM.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-11.13.36-PM.png)

Resultado da busca com **erro de ortográfica**:

[![Exemplo de prevenções de erros.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-11.14.04-PM.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-11.14.04-PM.png)

## 6\. Reconhecimento em vez de memorização

O usuário não tem obrigação de decorar qual foi o caminho que ele fez pra chegar até a página.

Por exemplo, quando você entra em um produto do site da Locaweb é disponibilizado o caminho que você fez pra chegar até ele. Nós chamamos isso de _breadcrumb_.

[![Exemplo de reconhecimento em vez de memorização.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-9.42.53-PM.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-9.42.53-PM.png)

## 7\. Flexibilidade e eficiência de uso

É importante deixar uma experiência boa com seu sistema desde o usuário mais leigo até o mais avançado.

Por exemplo, dentro do [Trello](https://trello.com/) (kanban de tarefas online com base em colunas free) quando você está com o foco em um cartão você pode utilizar o mouse para navegar em outros cartões, as teclas direcionais (setas pra cima e pra baixo) ou as letras **j** (para baixo) e **k** (para cima). Teclas que são por sua vez utilizadas no VIM, editor de texto famoso entre os desenvolvedores.

[![Exemplo de flexibilidade e eficiência de uso.](http://blog.caelum.com.br/wp-content/uploads/2016/01/key1.gif)](http://blog.caelum.com.br/wp-content/uploads/2016/01/key1.gif)

## 8\. Estética e design minimalista

Por favor não encha linguiça. Toda informação extra que você deixar pro seu usuário pode na verdade adicionar mais uma dúvida, ou seja, deixe o seu layout e o conteúdo o mais simples e direto possível.

Um app que gosta muito dessa heurística é o da [Nubank](https://nubank.com.br/). Pra gerar o boleto de pagamento da sua fatura só são necessárias duas telas e elas são bem simples e realmente objetivas.

[![Exemplo de estética e design minimalista.](http://blog.caelum.com.br/wp-content/uploads/2016/01/nubank-app-3.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/nubank-app-3.png)

[![Exemplo de estética e design minimalista.](http://blog.caelum.com.br/wp-content/uploads/2016/01/nubank-app-4.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/nubank-app-4.png)

## 9\. Ajude os usuários a reconhecerem, diagnosticarem e recuperarem-se de erros

As mensagens de erros tem que ser claras e próximas do conteúdo ou ação que causou o erro.

No formulário de cadastro do Spotify caso você não preencha os dados necessários ele deixa bem claro quais campos estão faltando, com um mensagem clara e objetiva. [![Exemplo de ajude os usuários a reconhecerem, diagnosticarem e recuperarem-se de erros.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-11.03.54-PM.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-11.03.54-PM.png)

## 10\. Ajuda e documentação

É uma boa não precisar dessa heurística. Implementar documentação e sistema de ajuda sempre é chato e muitos usuários têm o costume de ignorar ambos mas, se for realmente necessário, deixe a documentação próxima do usuário e do elemento ou ação que tenha necessidade de uma explicação mais detalhada.

Por exemplo, no formulário de pagamento do Walmart tem um campo pra preencher o código de segurança do cartão. Como não é algo muito óbvio, tem uma imagem próxima ao campo mostrando onde fica o código de segurança do cartão. Essa é uma boa forma de fazer uma documentação feliz.

[![Exemplo de documentação próxima do usuário.](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-10.57.22-PM.png)](http://blog.caelum.com.br/wp-content/uploads/2016/01/Screen-Shot-2016-01-19-at-10.57.22-PM.png)

Com o erro que cometemos no novo layout do Alura vimos que é uma boa passar pelas 10 heurísticas de Nielsen antes de subir um novo layout. Em que momento você acha bacana utilizar as heurísticas no seu projeto?
