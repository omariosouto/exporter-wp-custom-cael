---
title: "O que muda nas práticas de otimizações de performance na Web com o HTTP 2.0 e o SPDY"
date: "2014-06-18"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O HTTP 2.0 e o SPDY são a nova geração da Web. Trazem muitas novidades bacanas, como [vimos antes aqui no blog](https://blog.caelum.com.br/as-fantasticas-novidades-do-http-2-0-e-do-spdy/). Mas, principalmente, prometem **mudar a forma como desenvolvemos pra Web**, simplificando muita coisa.

## Otimizações de performance na Web

Performance Web não é um assunto novo por aqui. A importância é tão grande e desperta tanto interesse nos desenvolvedores, que [nosso post sobre o assunto](https://blog.caelum.com.br/por-uma-web-mais-rapida-26-tecnicas-de-otimizacao-de-sites/) é o mais acessado na história do blog da Caelum. Mostramos várias vezes como um site performático é melhor para o usuário e para as conversões, trazendo mais retorno para a empresa.

Na prática, quando falamos de **performance front-end**, as técnicas mais comuns giram em torno de:

- **Diminuir o número de requisições**
- **Diminuir o volume de dados nas requisições**
- **Paralelizar requisições**
- **Otimizar a renderização no navegador**

Agora com o HTTP 2.0 e o SPDY, várias técnicas dos tipos acima passam a ser _desnecessárias_. Às vezes, podem até ser _prejudiciais_. Antes de prosseguirmos, dê uma conferida nas [novidades do HTTP 2.0 e do SPDY](https://blog.caelum.com.br/as-fantasticas-novidades-do-http-2-0-e-do-spdy/) para que possamos analisar seus impactos.

## Requisições mais baratas

O HTTP 2.0 e o SPDY deixam as requisições em si mais baratas. Por exemplo, antes precisávamos habilitar o GZIP explicitamente no servidor para comprimir as respostas trafegadas. Agora, **GZIP é padrão no protocolo**, uma coisa a menos pra se preocupar.

Mais que isso: um alto custo de se fazer vários requests no HTTP 1.1 era o tráfego dos cabeçalhos (headers). Eles não era comprimidos mas enviados em texto puro. E, pra piorar, cabeçalhos tinham que ser mandados novamente a toda requisição.

No HTTP 2.0, os **cabeçalhos são binários e compactados**. Além disso, com os _header tables_, o navegador e o servidor podem mandar um certo header uma vez só e reaproveitar para as requisições seguintes. **Só é preciso mandar os cabeçalhos que mudam**. Isso deixa as requisições ainda mais baratas e justificam ainda mais a prática de usar vários requests ao invés de concatenar as coisas como fazíamos antigamente.

Aliás, uma prática que era recomendada no HTTP 1.1 de usar domínios sem cookies para servir imagens, CSS e JS, passa a ser irrelevante. Como os cookies são enviados em um header, sabemos que agora serão enviados uma vez só na conexão.

## Requests caros e blocantes no HTTP 1.1

No clássico HTTP 1.1, requisições são muito caras. O navegador faz um request e precisa esperar a resposta inteira chegar antes de mandar o próximo request. Não há paralelização de requisições no protocolo. A _gambiarra_ que os navegadores fazem é abrir várias conexões simultâneas na esperança de paralelizar mais os requests.

![requests seriais no http 1.1](https://blog.caelum.com.br/wp-content/uploads/2014/04/Screenshot-2014-04-14-15.13.04.png)

Em geral, os browsers modernos abrem de 4 a 8 conexões simultâneas com o mesmo hostname. Não abrem mais para não detonar o servidor na outra ponta e evitar saturar a rede. Mas nas páginas de hoje em dia é comum existir dezenas e até centenas de recursos que precisam ser requisitados. Paralelizar em 8 conexões não ajuda muito se você precisa baixar 100 coisas. Ainda há muito bloqueio e espera.

Então, no HTTP 1.1, a boa prática sempre foi diminuir os requests, _concatenando arquivos CSS e JS_, _criando sprites_ e até fazendo _inline do recurso_ direto no HTML da página. **Tudo isso é desnecessário no HTTP 2.0 e no SPDY.** Facilita muito a vida do desenvolvedor.

## Não se preocupe mais com o número de requisições

Com o [multiplexing](https://blog.caelum.com.br/as-fantasticas-novidades-do-http-2-0-e-do-spdy/), agora o navegador pode manter **apenas uma conexão aberta** e paralelizar as requisições e respostas dentro dela. Não há mais bloqueio e espera. **Podemos fazer várias pequenas requisições separadas e ter excelente performance**, o que não era possível no HTTP 1.1.

Ao usar várias requisições pequenas e separadas, **melhoramos bastante a capacidade do browser fazer cache** dos recursos individualmente. Imagina no mundo antigo de fazer sprites, o que acontece ao mudar um ícone; o browser precisa baixar a sprite inteira de novo e invalidar o cache. Agora não mais. Cada ícone pode ser um request separado e ser invalidado individualmente.

Outra vantagem é que o navegador consegue executar os arquivos individualmente, de maneira progressiva. O melhor exemplo disso acontece com o JavaScript. Se você junta todo o seu JS em 1 arquivo grandão, o browser precisa baixar o arquivo todo pra começar a executá-lo. Ou seja, o tamanho do arquivo pode retardar o início da execução do script. Agora você pode deixar vários arquivos JS separados e pequenos, e o navegador vai executando os arquivos conforme vai baixando, diminuindo o bloqueio.

## Não faça gambiarras para paralelizar requisições

A limitação nas requisições no HTTP 1.1 é tão grande que nasceram diversas técnicas para passar por cima disso. A mais comum era a utilização de múltiplos hostnames para aumentar o limite de conexões paralelas. O browser abria então 6 conexões com o hostname principal mas a página usava mais outros endereços para ganhar mais 6 conexões, como _imagens.meusite.com_. Isso é trabalhoso de fazer.

No HTTP 2.0 e no SPDY, **usar vários hostnames é uma má-prática**, principalmente por anular os benefícios do multiplexing. Agora, a conexão única do protocolo consegue paralelizar todos os requests e responses e não é mais necessário várias conexões.

## Enxergando o multiplexing na prática

Fizemos um página de testes com 200 arquivos JavaScript separados com tamanho médio de 13 KB cada um. Cada arquivo, quando executado, pintava um quadrado verde na tela assim conseguimos visualizar o carregamento individual. O primeiro teste, usando HTTP 1.1 mostrou esse resultado:

<iframe width="560" height="315" src="//www.youtube.com/embed/786v3xD9Wfw?rel=0" frameborder="0" allowfullscreen></iframe>

É possível ver claramente o navegador baixando 6 coisas ao mesmo tempo, que são as conexões paralelas que ele abre. Mas isso não ajuda muito quando precisamos baixar 200 arquivos. Há muita espera visível.

A boa prática do HTTP 1.1 então nos dizia para juntarmos tudo em um arquivo só. Fizemos o teste, veja a diferença:

<iframe width="560" height="315" src="//www.youtube.com/embed/jFIlP_JT7is?rel=0" frameborder="0" allowfullscreen></iframe>

O tempo total de carregamento cai absurdamente. Com os arquivos separados, nosso teste demorava mais de 40s e, agora, com tudo concatenado junto, demora apenas 7s. Mas nem tudo é bom. Se olharmos para a métrica do _tempo de execução do primeiro arquivo_, tivemos uma piora (de 1.2s para 7s). É o que discutimos: juntar os arquivos retarda a execução de tudo, apesar de diminuir o tempo total.

A mágica acontece quando testamos com SPDY usando os 200 arquivos separados:

<iframe width="560" height="315" src="//www.youtube.com/embed/U1c_h2rSzaE?rel=0" frameborder="0" allowfullscreen></iframe>

Repare como o multiplexing permite o download paralelo de muitas coisas, diminui a espera e praticamente junta o melhor dos 2 mundos. O tempo final continua baixo em 7s e o tempo do primeiro script fica em 1.2s. Ou seja, requests em paralelo, execução progressiva e tempo total excelente.

## Tchau inline de recursos, bem vindo server-push

No [último post](https://blog.caelum.com.br/as-fantasticas-novidades-do-http-2-0-e-do-spdy/), falamos bastante do novo **server push** do HTTP 2.0 e do SPDY. Eles são essenciais para suprir o que a prática de inline de recursos fazia no HTTP 1.1.

Apenas relembrando, no HTTP 1.1 costumava ser uma boa pegar recursos pequenos e essenciais e inserir seu conteúdo direto no HTML - uma prática chamada de inline. Isso é ruim pois os recursos não podem ser cacheados individualmente. No HTTP 2.0 e no SPDY, o novo server-push permite enviar requisições para o cliente antes mesmo de ele as requisitá-las. Na prática, são as vantagens do inline sem suas desvantagens.

## Revisitando as antigas técnicas de otimização e focando em novas coisas

Resumindo o que vimos até agora, várias práticas **param de fazer sentido** no HTTP 2.0 e no SPDY:

- Habilitar GZIP (não precisa, é automático)
- Concatenar arquivos CSS e JS
- Sprites de imagens
- Inline de recursos pequenos
- Domínios sem cookies
- Usar múltiplos hostnames

Reforçando: a lista acima é de coisas que você **não** precisa mais fazer se usar os novos protocolos.

Então, onde focar os esforços de otimização? Há várias práticas mais específicas e avançadas, mas que no geral seriam:

- **Diminuir tráfego:**
    - Minificação de CSS, JS e HTML
    - Configuração dos caches
    - Compressão de imagens

- **Melhorar renderização:**
    - Usar Server-push e Server-hint
    - Otimizar o critical path
    - Animações [jank-free](http://jankfree.org/) 60 FPS

- **Diminuir latência:**
    - Usar CDNs geograficamente distribuídos
    - (fique de olho no [QUIC](http://en.wikipedia.org/wiki/QUIC))

O importante é perceber que só a utilização do SPDY ou do HTTP 2.0 já vai fazer muita coisa pra gente. As outras otimizações são mais específicas e nem sempre aplicáveis.

Para saber mais sobre Web, Front-end e otimizações, não deixe de conhecer nosso [curso de HTML, CSS e JavaScript](http://www.caelum.com.br/curso-html-css-javascript/).
