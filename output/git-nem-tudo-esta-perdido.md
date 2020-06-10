---
title: "Git: nem tudo está perdido"
date: "2016-05-02"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quem já fez um `reset --hard` e depois percebeu que voltou mais do que devia? Humm, tá bom, eu fiz! Semana passada fui reverter algumas mudanças locais que commitei em uma branch do [Alura](http://alura.com.br), mas acabei resetando um commit a mais. É claro que eu só percebi isso **depois de dar um git push**.

A pergunta foi: e agora? O sentimento foi: medo. Mas a verdade é que se você commitou, no geral, da pra recuperar. Nesse post vou mostrar a forma que eu resolvi, que foi bem simples, e mais algumas possibilidades legais que acabei aprendendo no meio do caminho.

### Entendendo o problema na prática

Pra reproduzir o acontecido, vou dar um `git log` na master do repositório do Alura e um `reset --hard` voltando a apontar pro penúltimo commit. Repara que tem um commit meu, adicionando um alarme de 80% de CPU em nosso servidor, e depois do reset a master volta a apontar pro commit do Philippe Ehlert, que antecedia:

<script type="text/javascript" src="https://asciinema.org/a/cmn0wgrnahtcqqnn8f232dcau.js" id="asciicast-cmn0wgrnahtcqqnn8f232dcau" async></script>

Pronto, agora é só fazer um `push -f origin master` e se arrepender 2 segundos depois.

### Em busca do commit perdido

A notícia boa é que mesmo que você tenha feito um reset, o commit continua lá... ele não é apagado! Ou seja, você pode recuperar qualquer commit se você souber o **SHA1** dele. Se você não está acostumado com esse termo, SHA1 nada mais é do que esse hash bonitão (único) que identifica um commit:

![exemplo de SHA1](https://blog.caelum.com.br/wp-content/uploads/2050/02/SHA1-sample.png)

Mas como eu vou saber o SHA1 se eu já fiz um reset e ele não aparece mais no log? A resposta é simples: toda mudança que você faz na HEAD de uma branch fica registrado em um lugar conhecido como **reflog**, que pode ser consultado com o comando `git log -g`:

<script type="text/javascript" src="https://asciinema.org/a/36ds8ug1x7hmutezus5akv4am.js" id="asciicast-36ds8ug1x7hmutezus5akv4am" async></script>

Repara que primeiro eu fiz um `git log` normal, meu commit não estava lá... depois um `git log -g` e voilà, tenho o registro de tudo o que aconteceu na minha branch. Legal, não acha? O reflog tem muito mais informações que podem ser úteis, se você for curioso como eu, já deve estar [googlando pela sua documentação](https://git-scm.com/docs/git-reflog).

### Voltando o commit pra master

Agora que eu já tenho o SHA1 do meu commit eu posso, entre outras opções, fazer um [cherry-pick](https://git-scm.com/docs/git-cherry-pick) desse commit de volta pra master. Não tem segredo, basta rodar `git cherry-pick [SHA1]` e pronto:

<script type="text/javascript" src="https://asciinema.org/a/502b607xqg6rfyf40f551eenn.js" id="asciicast-502b607xqg6rfyf40f551eenn" async></script>

Ufa, problema resolvido.

Essa é uma regra valiosa do Git: se você commitou alguma coisa, provavelmente dá pra recuperar. Se você não commitou, bem... já foi! Por isso é importante fazer commit e se possível push de tudo com a maior frequência possível, falamos bastante disso [no curso de Git do Alura](https://www.alura.com.br/curso-online-git) e no curso de [práticas ágeis daqui da Caelum](https://www.caelum.com.br/curso-praticas-ageis/).

E você, o que mais tem feito de legal com Git? Já perdeu algum commit e não conseguiu recuperar?
