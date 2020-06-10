---
title: "Usando o git add interativo"
date: "2016-11-29"
author: "philippeehlert"
authorEmail: "philippe.ehlert@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muitas vezes, quando estamos desenvolvendo alguma funcionalidade nova, precisamos criar novas classes, modificar outras e até mesmo mexer em algumas partes que não necessariamente são relacionadas com a funcionalidade nova que estamos implementando. Quando terminamos tudo, ficamos com um cenário assim no git:

\[code language="bash"\] ➜ sistema-cotacao git:(master) ✗ git status On branch master Your branch is ahead of 'origin/master' by 1 commit. (use "git push" to publish your local commits) Changes not staged for commit: (use "git add <file>..." to update what will be committed) (use "git checkout -- <file>..." to discard changes in working directory)

modified: src/main/java/br/com/quotation/dao/PModelDAO.java modified: src/main/java/br/com/quotation/dao/PumpDAO.java modified: src/main/java/br/com/quotation/dao/QuotationDAO.java modified: src/main/java/br/com/quotation/dao/SealDAO.java modified: src/main/java/br/com/quotation/dao/SpecialPriceDAO.java modified: src/main/java/br/com/quotation/model/Quotation.java modified: src/main/webapp/WEB-INF/views/quotations/request.jsp modified: src/main/webapp/WEB-INF/views/quotations/view.jsp modified: src/test/br/com/quotation/model/QuotationTest.java

Untracked files: (use "git add <file>..." to include in what will be committed)

src/main/java/br/com/quotation/controller/QuotationRequestController.java src/main/webapp/css/quotation-pdf.css src/main/webapp/css/quotation.css src/main/webapp/js/quotation.js

no changes added to commit (use "git add" and/or "git commit -a") \[/code\]

Como você pode ver, no desenvolvimento da funcionalidade de solicitar cotações em meu sistema, eu precisei modificar views, DAOs, modelos, criar novos arquivos css, javascript e um controller. Além disso, durante o desenvolvimento, eu arrumei alguns testes que outro desenvolvedor quebrou e precisei criar novos testes para o código que acabei de criar.

O caminho mais rápido pra commitar tudo isso seria fazer tudo em um único commit, com uma mensagem genérica:

\[code language="bash"\]git commit -am "fazendo um monte de coisas"\[/code\]

O problema dessa abordagem é que ela pode tornar mais difícil encontrar quando um bug começou a acontecer, pode atrapalhar outro desenvolvedor que está tentando entender o que aconteceu em cada arquivo e também pois perde boa parte do propósito do git, que é manter um histórico de todas as alterações do projeto. Será que existe alguma coisa que pode nos ajudar dessas situações?

## Usando o git add interativo

O git add interativo é uma ferramenta que nos ajuda a lidar com cenários como esse, ele nos permite adicionar arquivos específicos de forma mais rápida e até mesmo selecionar partes específicas de um arquivo pra commitar. Para usar ela, basta digitar o comando:

\[code language="bash"\]git add -i\[/code\]

\[code language="bash"\] ➜ sistema-cotacao git:(master) ✗ git add -i staged unstaged path 1: unchanged +0/-3 src/main/java/br/com/quotation/dao/PModelDAO.java 2: unchanged +0/-3 src/main/java/br/com/quotation/dao/PumpDAO.java 3: unchanged +0/-25 src/main/java/br/com/quotation/dao/QuotationDAO.java 4: unchanged +0/-6 src/main/java/br/com/quotation/dao/SealDAO.java 5: unchanged +0/-4 src/main/java/br/com/quotation/dao/SpecialPriceDAO.java 6: unchanged +0/-4 src/main/java/br/com/quotation/model/Quotation.java 7: unchanged +0/-26 src/main/webapp/WEB-INF/views/quotations/request.jsp 8: unchanged +1/-12 src/main/webapp/WEB-INF/views/quotations/view.jsp 9: unchanged +0/-11 src/test/br/com/quotation/model/QuotationTest.java

\*\*\* Commands \*\*\* 1: status 2: update 3: revert 4: add untracked 5: patch 6: diff 7: quit 8: help What now> \[/code\]

Logo de primeira ela nos mostra quais são as opções, para usar cada uma delas, basta digitar o número correspondente e dar enter.

`1. Status` Nos mostra essa mesma tela, listando os arquivos que foram modificados

`2. Update` Nos permite adicionar as mudanças que fizemos em arquivos já existentes, ou seja, passar arquivos de `not staged` para `staged`.

Por exemplo: ao digitar o número `2` e dar um enter, ele me mostra uma lista dos arquivos que foram modificados e um número em frente a cada um deles.

\[code language="bash"\] What now> 2 staged unstaged path 1: unchanged +0/-3 src/main/java/br/com/quotation/dao/PModelDAO.java 2: unchanged +0/-3 src/main/java/br/com/quotation/dao/PumpDAO.java 3: unchanged +0/-25 src/main/java/br/com/quotation/dao/QuotationDAO.java 4: unchanged +0/-6 src/main/java/br/com/quotation/dao/SealDAO.java 5: unchanged +0/-4 src/main/java/br/com/quotation/dao/SpecialPriceDAO.java 6: unchanged +0/-4 src/main/java/br/com/quotation/model/Quotation.java 7: unchanged +0/-26 src/main/webapp/WEB-INF/views/quotations/request.jsp 8: unchanged +1/-12 src/main/webapp/WEB-INF/views/quotations/view.jsp 9: unchanged +0/-11 src/test/br/com/quotation/model/QuotationTest.java Update>> \[/code\]

Para adicionar um arquivo, eu posso tanto digitar apenas o número dele, como `8` ou, se eu quero adicionar mais de um arquivo, posso digitar um intervalo: `1-5`

\[code language="bash"\] staged unstaged path \* 1: unchanged +0/-3 src/main/java/br/com/quotation/dao/PModelDAO.java \* 2: unchanged +0/-3 src/main/java/br/com/quotation/dao/PumpDAO.java \* 3: unchanged +0/-25 src/main/java/br/com/quotation/dao/QuotationDAO.java \* 4: unchanged +0/-6 src/main/java/br/com/quotation/dao/SealDAO.java \* 5: unchanged +0/-4 src/main/java/br/com/quotation/dao/SpecialPriceDAO.java 6: unchanged +0/-4 src/main/java/br/com/quotation/model/Quotation.java 7: unchanged +0/-26 src/main/webapp/WEB-INF/views/quotations/request.jsp \* 8: unchanged +1/-12 src/main/webapp/WEB-INF/views/quotations/view.jsp 9: unchanged +0/-11 src/test/br/com/quotation/model/QuotationTest.java Update>> \[/code\]

Como você pode ver, os arquivos que eu adicionei ficam com um `*` do lado. Assim que eu terminar de adicionar todos os arquivos, é só dar mais um enter sem inserir nenhum número para voltar ao menu principal.

\[code language="bash"\] ➜ sistema-cotacao git:(master) ✗ git status On branch master Your branch is ahead of 'origin/master' by 1 commit. (use "git push" to publish your local commits) Changes to be committed: (use "git reset HEAD <file>..." to unstage)

modified: src/main/java/br/com/quotation/dao/PModelDAO.java modified: src/main/java/br/com/quotation/dao/PumpDAO.java modified: src/main/java/br/com/quotation/dao/QuotationDAO.java modified: src/main/java/br/com/quotation/dao/SealDAO.java modified: src/main/java/br/com/quotation/dao/SpecialPriceDAO.java modified: src/main/webapp/WEB-INF/views/quotations/view.jsp

Changes not staged for commit: (use "git add <file>..." to update what will be committed) (use "git checkout -- <file>..." to discard changes in working directory)

modified: src/main/java/br/com/quotation/model/Quotation.java modified: src/main/webapp/WEB-INF/views/quotations/request.jsp modified: src/test/br/com/quotation/model/QuotationTest.java

Untracked files: (use "git add <file>..." to include in what will be committed)

src/main/java/br/com/quotation/controller/QuotationRequestController.java src/main/webapp/css/quotation-pdf.css src/main/webapp/css/quotation.css src/main/webapp/js/quotation.js \[/code\]

Pronto, eu adicionei apenas 6 de meus 9 arquivos que foram modificados e agora eu posso escrever uma mensagem de commit bastante detalhada sobre quais modificações eu fiz.

### Mais opções do git add interativo

`3. Revert` Permite voltar arquivos que estão `staged` para `not staged`. (o mesmo que `git reset HEAD caminho/para/o/arquivo.java`)

`4. Add untracked` Permite adicionar novos arquivos no projeto. (equivalente a `git add caminho/para/o/arquivo.java`)

`5. Patch` Permite escolher um arquivo e adicionar pedaços específicos dele.

`6. Diff` Nos mostra um diff dos arquivos que estão `staged` mostrando exatamente as diferenças de cada um dos arquivos que foram modificados. (equivalente ao `git diff --cached`)

As opções `3 - revert`, `4 - add untracked` e `6 - diff` funcionam de forma idêntica à opção `2 - update`, já a opção `5 - patch` que nos permite escolher pedaços específicos de nossos arquivos para commitar, será tema de um próximo post.

Se você usa bastante o git e quer aprender mais dicas como essa, dê uma olhada em nosso [curso online de git](https://www.alura.com.br/curso-online-git)
