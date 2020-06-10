---
title: "Internet Explorer, testes cross browsers e a política de compatibilidade"
date: "2013-02-18"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Quais versões de que browsers você deve suportar no seu website? Qual política deve ser adotada pela sua empresa?

Decisões difíceis, dado que tempo e verba são escassos. Muitos desistiam de suportar versões antigas do Internet Explorer, cortando uma fatia significativa de possíveis consumidores.

O fantasma do Internet Explorer assombrou a Web durante muito tempo. Navegadores ruins, não cumprimento dos padrões, problemas de segurança, péssima performance, e uma chatice pra testar os sites. **Não mais.** O mundo é outro hoje em dia e o tempo de fazer piada com o Internet Explorer está ficando pra trás.

O problema do IE está se resolvendo de várias maneiras:

- As versões mais novas são realmente muito boas - IE9 e IE10.
- A Microsoft fez um update automático para todos os usuários de IE levando à versão mais recente disponível. Isso fez IE6 e IE7 praticamente sumirem.
- Testar os sites no IE está muito fácil, inclusive no Mac e no Linux.
- Muita gente migrou para outros browsers - [Chrome já é o browser mais usado no Brasil há bastante tempo](http://gs.statcounter.com/#browser-BR-monthly-201106-201301)!

![internetexplorer9logo](https://blog.caelum.com.br/wp-content/uploads/2013/02/internetexplorer9logo.png)

## Como testar no Internet Explorer no Windows, Mac e Linux?

Primeiro, só um serviço remoto de screenshots não resolve. Você precisa **testar na sua máquina**, usar o site. Pra isso, precisa instalar várias versões juntas. E o problema é que o IE vem embutido no Windows de tal forma que isso é impossível.

Existem algumas gambiarras que tentam fazer vários IEs rodarem ao mesmo tempo. Ou tem gente que só instala a versão mais nova e usa o modo de compatibilidade nas ferramentas pra emular os IEs velhos. **Não faça isso**, nada disso serve. Pra realmente testar o IE, você precisa de uma instalação limpa de cada um deles. Ou seja, você precisa de vários Windows.

Como fazer? **Use máquinas virtuais**, claro. Mas, até mês passado, isso significava comprar um Windows de cada versão (XP, Vista, 7 e 8). Não mais.

A Microsoft lançou **máquinas virtuais oficiais** com IE 6, 7, 8, 9, 10, no XP, Vista, Windows 7 e 8. Tudo **legalizado**, focado nos testes pra desenvolvedores Web. E mais: tem versões pra Windows, Mac e Linux, rodando em várias plataformas de virtualização: VMWare, VirtualBox e VirtualPC (Parallels a caminho). [É só baixar e rodar](http://www.modern.ie/virtualization-tools).

Há o trabalho de abrir a VM, claro, toda vez que for testar. Mas, por favor, **faça isso**. Dica bônus: se você colocar 8GB de memória e um SSD na sua máquina, consegue rodar fácil 3 Windows ao mesmo tempo, além do seu sistema normal.

## Quais versões do IE preciso testar?

Depende bastante do seu público. Mas já é meio consenso que [IE6 e IE7 já eram](http://gs.statcounter.com/#browser_version_partially_combined-BR-monthly-201208-201301). Claro, pode ser que seu público ainda use, então veja suas estatísticas.

O IE8 é um caso mais particular, já que é a última versão disponível no Windows XP, então muita gente ainda tem - 7,5% no Brasil em Janeiro/2013. Mesmo assim, muita gente já não suporta mais, como [o Google](http://googleappsupdates.blogspot.com.br/2012/09/supporting-modern-browsers-internet.html).

## Qual a política da Caelum?

Analisando os números do [site da Caelum](http://www.caelum.com.br/), vemos uma distribuição bem parecida com a população geral do Brasil: 60% de Chrome, 25% Firefox e 7,5% de todos os IEs.

![stats-caelum-jan2013-browser](https://blog.caelum.com.br/wp-content/uploads/2013/02/stats-caelum-jan2013-browser.png)

Se observarmos as versões do IE então, vamos ver que IE9 e IE8 dominam, com o IE10 crescendo rápido. Mesmo assim, se calculasse com relação a total de usuários, teríamos: 4,3% de IE9, 2,4% de IE8, 0,5% de IE10, 0,24% de IE7 e 0,03% de IE6.

![stats-caelum-jan2013-ie](https://blog.caelum.com.br/wp-content/uploads/2013/02/stats-caelum-jan2013-ie.png)

(E você pode comparar a evolução com [esse post nosso de 2011](https://blog.caelum.com.br/evolucao-dos-navegadores-e-estatisticas-de-acesso/), quando mostramos que 21% das pessoas usavam IE, sendo 16,8% só de IE8.)

Com isso, a **política hoje da Caelum em 2013** é:

- Suportar IE8+ nos sites de audiência mais aberta ([site principal](http://www.caelum.com.br), [GUJ](http://www.guj.com.br) etc).
- Suportar IE9+ nos sites mais específicos (como [plataforma Online](http://online.caelum.com.br))

## Uma nota final sobre o IE10

O IE10 é um **excelente navegador** - até a Microsoft tá [tirando sarro com isso](http://browseryoulovedtohate.com/), de tão _inusitado_, mas é verdade.

Um ponto importante: teste no IE10 também. Reparem nas estatísticas que IE10 já passou IE7+IE6 e já já passa IE8. A questão com o IE10 é que ele traz alguns desafios. Ele roda por enquanto só no Windows 8 e se integra de tal forma a ser o único browser disponível na versão tablet - e o "Metro Mode". Quando sair a versão pra Windows 7 (esse semestre), deve passar todos os outros facilmente.

Hoje, pra testar o IE10, você precisa do Windows 8 (tem de graça lá no site da Microsoft de VMs). E existem 3 modos do IE10 que você precisa testar: Desktop-mode (IE normal), Metro mode, e Snap Mode. A boa notícia é que o IE10 é muito bom em padrões web, performance e usabilidade; não deve dar muito trabalho.

É isso. O cenário de testes e suporte ao IE tem melhorado a cada momento. As últimas versões são bastante boas, as versões antigas perdem market share rapidamente, e as novas VMs gratuitas disponibilizadas pela Microsoft facilitam muito o desenvolvimento. Por isso, considere fortemente incluir as versões antigas.

E qual é sua política de suporte a browsers? Alguma dica para facilitar esse trabalho? A gente discute esses e outros pontos sobre desenvolvimento Web no [curso de front-end](http://www.caelum.com.br/curso-html-css-javascript/) da Caelum.

No desenvolvimento mobile os testes podem ser ainda mais complicados e a variação de resolução, viewport e fabricantes deixa tudo desafiador. Participe do [MobileConf Brasil](http://www.mobileconf.com.br/), que acontecerá dia 5 de abril e conheça mais!
