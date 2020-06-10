---
title: "Porque você não deveria ligar para o tempo do onload (ou: as métricas que importam para performance real na Web)"
date: "2015-07-07"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Um site rápido é um site que passa a **sensação de rapidez** para o usuário. No fundo, performance é UX e quem diz se o site é rápido ou não é seu usuário.

Mas como bons desenvolvedores com cabeças analíticas e lógicas, queremos uma resposta exata para _"Quanto meu site é rápido?"_ Ou ainda _"Como medir a performance do meu site?"_ ou _"Me dê uma nota de performance"_.

Na comunidade de performance front-end, **1 segundo** é tido como o número mágico. O santo graal. Se seu site carregar em menos de 1 segundo, ele é rápido. Mas como medir?

Você pode usar várias ferramentas de análise de performance para obter números interessantes. Eu gosto bastante do [WebPageTest](http://www.webpagetest.org/), mas há também o [PageSpeed](https://developers.google.com/speed/pagespeed/insights/?hl=pt-BR), YSlow e tantos outros. Você pode, por exemplo, medir o tamanho total da página em KB (e tentar diminuir o peso das coisas se a página estiver grande demais). Ou medir quantos requests estão sendo feitos (e concatenar arquivos pra aliviar).

[![Screenshot 2015-05-22 19.24.40](https://blog.caelum.com.br/wp-content/uploads/2015/05/Screenshot-2015-05-22-19.24.40.png)](http://www.webpagetest.org/result/150522_EA_18HK/)

Mas fato é que **nem tamanho da página nem número de resquests te dizem se a página é rápida ou não**. Claro, são métricas relacionadas a experiência final do usuário. Mas são números que, sozinhos, não dizem nada.

## Porque o onload não serve - e nem o DOMContentLoaded

Uma métrica historicamente usada para saber o tempo de carregamento é **esperar o evento onload**. O navegador dispara esse evento quando a página termina de carregar (seus CSS, JS, imagens, etc). Parece um bom número pra ficar de olho. Na imagem anterior, que mostra números do site da Caelum, o _Load Time_ indica 2.1s. Parece bem longe do 1s ideal.

Mas o onload não serve muito. Primeiro porque nos sites de hoje muita coisa acontece _depois_ do onload. Carregamos coisas assíncronas, Ajax diversos e coisas assim. Na imagem mesmo, note que há um outro número, o _Fully Loaded_, que é maior ainda - 2.4s - e indica quando acaba de carregar tudo, mesmo depois do onload.

O pior problema do onload, porém, é que ele **não está relacionado de forma alguma a experiência real do usuário**. Saber que todas as imagens acabaram de carregar em X segundos não interessa tanto, se você pensar que isso inclui até aquela imagenzinha no rodapé, pouco útil para o usuário.

Aliás, outro evento, o `DOMContentLoaded`, indica quando o DOM está pronto. Dispara antes do onload, afinal não espera os outros componentes chegarem. Mas também sofre dos mesmos problemas e não serve de indicativo.

## Observando quadro a quadro

Um aspecto que influencia bastante a experiência real do usuário é quanto tempo ele fica encarando uma tela em branco. Quanto mais tempo demora para aparecer _algo_ na tela, pior a sensação de lerdeza. Aliás, é por isso que [carregamos as coisas assincronamente](https://blog.caelum.com.br/otimizacoes-na-web-e-o-carregamento-assincrono/), por exemplo.

No WebPageTest você pode pedir para [gravar um vídeo](http://www.webpagetest.org/video/compare.php?tests=150522_EA_18HK-r:1-c:0) da experiência do usuário de acordo com o tempo:

<iframe src="http://www.webpagetest.org/video/view.php?id=150522_22dbdc072b06c12fc878ecdca7a1140a57fdc664&amp;embed=1&amp;width=520&amp;height=432" width="520" height="432"></iframe>

Parece rápido, mas podemos analisar também quadro a quadro de acordo com o tempo:

[![filmstrip](https://blog.caelum.com.br/wp-content/uploads/2015/05/filmstrip.jpg)](http://www.webpagetest.org/video/compare.php?tests=150522_EA_18HK-r:1-c:0)

Repare que o site começa a ser desenhado já em 0,6s. Antes disso, muita tela em branco.

## A métrica Start Render

Se olhar de novo a tabela dos números, vai reparar que existe uma métrica _Start Render_ indicando 0.596s. Esse número é justamente o que reparamos no vídeo anterior: que a página começa a ser renderizada na tela a partir daí.

[![Screenshot 2015-05-22 19.24.40](https://blog.caelum.com.br/wp-content/uploads/2015/05/Screenshot-2015-05-22-19.24.40.png)](http://www.webpagetest.org/result/150522_EA_18HK/)

O _Start Render_ é uma métrica bastante útil e reflete o início da experiência real do usuário. Esse número deve ser bem baixo. Poucas centenas de milissegundos. O valor de 600ms do exemplo do site da Caelum já é alto. Se der, diminua mais.

## O Speed Index

Claro que só olhar o _Start Render_ não diz tudo. Ele fala só do _início_ da percepção de performance. Mas se olhar o onload completo também não diz nada, o que devemos olhar? **Foque na experiência inicial do usuário.**

Pense: se eu abrir o site da Caelum sem cronometrar nada, qual indício me diz que a página carregou? Olhando no quadro a quadro acima, repare que em 0,9s o site **já parece usável**. Mesmo o onload demorando 2,4s, em apenas 900ms o usuário já tem a **sensação de carregamento total**.

Porque isso? Porque o conteúdo inicial da página, aquele antes da dobra (_above the fold_) já foi todo carregado. Visualmente ele já está desenhado na tela. Nem me interessa se o rodapé já carregou por exemplo. Ou se existe algum outro pedaço da página lá embaixo ainda a ser carregado. No site da Caelum, por exemplo, há um widget do Facebook duas scrolladas pra baixo que carrega assincronamente. Ele é o principal culpado do nosso onload ir para 2,4s. Mas, como usuário, você não liga pra isso quando abre a página. E fica feliz que tudo _parece carregado_ em apenas 900ms.

**Medir o tempo para o conteúdo antes da dobra aparecer na tela é o que faz o Speed Index.** Essa métrica, criada pelo WebPageTest, faz uns [cálculos complexos](https://sites.google.com/a/webpagetest.org/docs/using-webpagetest/metrics/speed-index) analisando as imagens da tela para chegar em um número exato. Ele indica, em milissegundos, quando a parte superior do site parece visualmente estável para o usuário.

É uma excelente métrica que diz mais sobre a experiência real do usuário na página. O ideal é ficar abaixo de 1000 sempre. O número mágico da performance. Carregar a página em menos de 1 segundo.

[Nesta análise do site da Caelum](http://www.webpagetest.org/result/150522_EA_18HK/) conseguimos 821. Isso quer dizer que após 821ms o usuário já vê o topo do site todo e pode interagir. Mesmo que haja conteúdo secundário ainda sendo carregado lá embaixo.

## Otimize as métricas certas

Se seu objetivo com performance é agradar o usuário, invista maior parte do tempo otimizando o _Start Render_ e o _Speed Index_. Não gaste tempo pensando no onload ou no número de requests em si. O tamanho da página é legal otimizar pensando em 3G, pra não acabar com o plano de dados do usuário. Mas a percepção de performance em si está no _Speed Index_.

Falando em mobile, é claro que um _Speed Index_ de 1000 seria ótimo. Mas é quase utópico. A comunidade de performance considera 1000 um número bom para Desktop e 3000 um número bom para mobile em 3G. .

No WebPageTest, você pode testar em dispositivos móveis reais (um Moto G, por exemplo) em redes com velocidade e latência iguais de 3G. Aliás, esse costuma ser um teste de abrir os olhos. É bem difícil fazer um site rodar bem no mobile. E muita gente não faz ideia de como seu próprio site é ruim no 3G.

O site da Caelum consegue 2300, com um _Start Render_ 2s ([veja o teste](http://www.webpagetest.org/result/150522_VV_19WF/1/details/)). É um site bastante otimizado já. Mas há espaço para melhorias.

E você, como mede a performance dos seus sites? Indica outra ferramenta? Qual seu _SpeedIndex_?
