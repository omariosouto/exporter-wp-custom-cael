---
title: "Como estimar a janela inicial de uma nova conexão TCP com Wireshark"
date: "2017-03-14"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Quando abrimos uma nova conexão TCP, entra em ação um mecanismo chamado de **TCP Slow Start**. Isso quer dizer que, não interessa a banda do servidor e do cliente, novas conexões TCP sempre começam enviando poucos pacotes de dados e vão aumentando a vazão com o tempo conforme as duas pontas aguentam.

Isso tem implicações bem importantes pra sua página Web: toda nova conexão costuma mandar poucos pacotes de início e, se sua página for grande demais, vamos precisar de muitos RTT pra obter todos os dados. Na especificação original do TCP, o recomendado era que, inicialmente, novas conexões deviam mandar até 4 segmentos TCP apenas (~6KB). É a janela inicial de uma nova conexão TCP.

No [curso avançado de Performance Web](https://www.alura.com.br/curso-online-performance-http2-critical-path) discutimos os detalhes de tudo isso. E mostramos que mais recentemente houve um movimento pra se atualizar o tamanho inicial da janela TCP pra 10 segmentos (~14KB). **E isso é um grande ganho pra performance na Web.**

A maioria dos servidores recentes já usa já esse padrão. Mas como ter certeza? Bom, dá pra olhar nas configurações do kernel do Linux por exemplo, mas como saber se você não cuidar do servidor? Foi essa pergunta que o Jefferson Pereira fez pra gente no [fórum da Alura](https://cursos.alura.com.br/forum/topico-tcp-slow-start-31450)!

A janela inicial do TCP, por definição, não é anunciada pro cliente. Se o servidor for seu, você pode apenas olhar a configuracao do kernel e saber, mas como cliente é mais dificil.

O que dá pra fazer é usar algum inspecionador de pacotes TCP e _estimar_ essa janela a partir dos pacotes transmitidos.

Pra enxergar esse tipo de informação, precisamos analisar os pacotes TCP e isso é um pouco mais complicado. As ferramentas de alto nível tipo Chrome DevTools e WebPageTest não fazem. A mais usada (apesar de não muito simples) é o **Wireshark**: [https://www.wireshark.org](https://www.wireshark.org)

Você instala o wireshark, clica em gravar e ele grava todos os pacotes de rede transmitidos e recebidos pela sua máquina. Aí tem um campo filtro que permite ver só os que você tem interesse. Por exemplo esse teste abrindo a globo.com:

![](https://blog.caelum.com.br/wp-content/uploads/2017/03/Screenshot-2017-01-30-10.19.21-1024x668.png)

1) Cliquei em gravar no wireshark e abri www.globo.com no meu navegador

2) Ele gravou um monte de pacotes mas aí filtrei só os que chegaram da globo: `ip.src_host contains www.globo.com`

3) Observando os tempos de chegada de cada pacote (coluna **TIME**) você consegue ver que a primeira leva de de pacotes (**\[TCP segment\]**) tem 10 segmentos (pacotes 263 a 272 na imagem). É só ver que a diferença de tempo entre esses primeiros 10 é relativamente nula. Aí depois desses 10 tem um aumento significativo no tempo, indicando que ele esperou os ACK desses 10 antes de mandar os próximos.

Ou seja, nesse caso a gente pode chutar com bastante probabilidade que a janela inicial do TCP está em 10 segmentos.
