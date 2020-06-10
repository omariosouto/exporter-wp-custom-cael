---
title: "As três principais teclas de atalho do Eclipse"
date: "2011-03-29"
author: "lacerdaph"
authorEmail: "lacerdaph@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

**Atualização 02/2012:** Veja MAIS atalhos e truques AVANÇADOS no novo curso online [Produtividade Extrema com Eclipse](http://www.caelum.com.br/curso/online/eclipse/).

Na [apostila do curso FJ-11](http://www.caelum.com.br/curso/fj-11-java-orientacao-objetos/) passamos diversas teclas de atalho importantes para o desenvolvedor iniciante, mas damos especial destaque a um pequeno grupo, para não atrapalhar o aprendizado. Vamos diminuir ainda mais esse grupo e mostrar como podemos praticamente nos livrar da interrupção do mouse com apenas três teclas de atalho: `Control+Espaço`, `Control+1` e `Control+3`.

## 1\. Acha que já conhece o `Control + Espaço`?

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.10.06-AM.png "CP autocomplete simples")

Sim, ela te ajuda a autocompletar nome de classes e métodos, mas podemos ir muito além. Ao declarar uma variável do tipo `ContaCorrente` basta, logo após dar o espaço, pressionar `Control + Espaço` e possibilidades de nome serão sugeridas:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.10.35-AM.png "CP completar nome variavel")

O `Control + Espaço` pode ativar um dos muitos templates do Eclipse, como o `foreach`. Basta digitá-lo e pressionar `Control + Espaço` que será estruturado um enhanced-for, sugerindo utilizar a array ou collection do escopo mais próximo. O funcionamento é análogo para outros templates, como para gerar o método main:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.07.03-AM.png "CP foreach")

Existem várias formas de se gerar os getters e setters e a mais rápida é com `Control + Espaço`. Dado qualquer atributo, basta digitar `get+Control + Espaço` e pronto, ele dará opções para a geração do getter. O funcionamento é análogo para o setter:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.15.35-AM.png "CP getter")

## 2\. O `Control + 1` vai além da correção:

Esta talvez seja a combinação mais eficiente, porém menos conhecida. Ela não serve apenas para dar sugestões quando algo há algum erro de compilação (_quickfix_), indicado pela pequena lâmpada, como no tratamento de exceções checked:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.21.05-AM.png "CP trycatch")

Ou para criar classes que ainda não existem:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.35.03-AM.png "c1 criacao classe")

Quando declaramos um atributo de uma classe, o `Control + 1` vai oferecer e geração de getter e setter:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.24.23-AM.png "C1 getter")

Mas o uso mais interessante é na criação de váriáveis e atributos. Quando vamos criar uma váriável, começamos declarando-a, mas isso não é necessário. Podemos simplesmente digitar `new ContaCorrente()` e pressionar `Control + 1`:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.32.40-AM.png "CP criar variavel")

Podemos também utilizar `Control + 1` para remover cada import desnecessário, porém o `control+shift+o` é mais eficiente.

## 3\. O `Control + 3` pode fazer todo o restante.

Ele é o atalho que busca um comando ou opção de menu baseado no que você escreve. Ao digitar _generate_, por exemplo, temos:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.38.17-AM.png "C3 Generate")

Com isso podemos gerar construtores, getters e setters ou buscar por todo código. Até mesmo abrir a tela de _preferences_ ou fazer um _new class_. Mais: conforme você usa o `Control + 3`, o Eclipse memoriza suas principais escolhas, de tal forma que da próxima vez elas estarão ordenadas de acordo com sua utilização. Também é possível escrever apenas as primeiras letras das palavras que constituem o comando, como **G**enerate **G**etters **A**nd **S**etters (GGAS):

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.47.10-AM.png "gcuf")

Pode gerar construtores de acordo com seu atributos, através do GCUF (**G**enerate **C**onstructor **U**sing **F**ields), entre muitos outros. O céu é o limite.

Ou rodar sua aplicação, ao digitar _run_:

![](https://blog.caelum.com.br/wp-content/uploads/2011/03/Screen-shot-2011-03-29-at-2.56.34-AM.png "c3 run")

Para os iniciante em Eclipse, isso é bem mais fácil que guardar na memória as estranhas teclas de atalho para rodar uma aplicação: agora ela está a apenas um `Control + 3 run` de distância.

E você, quais atalhos mais utiliza? Dominar uma IDE pode te deixar muito produtivo, e vemos que não é necessário memorizar muitas teclas para já ter um considerável desempenho com o Eclipse. Fica o agradecimento a diversos desenvolvedores e instrutores da Caelum pelas dicas: Adriano Almeida, Jose Donizetti, Lucas Cavalcanti e Fabio Kung.

**Atualização 02/2012:** Veja MAIS atalhos e truques AVANÇADOS no novo curso online [Produtividade Extrema com Eclipse](http://www.caelum.com.br/curso/online/eclipse/).
