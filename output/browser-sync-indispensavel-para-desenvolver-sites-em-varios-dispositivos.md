---
title: "Browser-sync, indispensável para desenvolver sites em vários dispositivos"
date: "2015-06-17"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O mundo front-end é inundado de novas ferramentas todos os dias. Mas poucas realmente se destacam. O **Browser-sync** é uma delas. Uma ferramenta que é absolutamente **indispensável** no meu dia a dia desenvolvendo pra Web em vários dispositivos.

Ele resolve um problema bem chato. Você está desenvolvendo e precisa testar a página no navegador. Abre lá no seu navegador preferido no Desktop e vai fazendo. Mas daqui a pouco você precisa abrir no IE, no Opera, Safari, Firefox, Edge. Precisa testar em vários browsers. No mundo mobile responsivo então, vai abrir no seu celular, vai abrir no emulador que tiver na máquina, vai testar no seu iPad, pegar o celular emprestado da esposa pra ver também, e aproveitar pra ver na smart TV da sala.

No fim, você tem um cenário de dezenas de browsers e aparelhos pra testar. Acha um bug no CSS, edita o arquivo e precisa ver se arrumou em todos os lugares. Vai dar um **F5** em cada um dos 25 aparelhos que está usando. E assim para cada mudança que fizer no seu código.

Resolve testar se a página interna do site funciona? Vai ter que abri-la nos vários browsers de novo. Quer ver se o formulário de contato funciona? Tem que digitar o email e a mensagem nos 25 aparelhos pra ter certeza. É de ficar louco.

**O browser-sync sincroniza o código e as ações de vários navegadores simultaneamente.** Teste um, teste em todos.

Ele é uma evolução de uma ideia de _live reload_ que surgiu um tempo atrás. Você edita seu código - CSS, HTML, JS, imagens etc - e o navegador é automaticamente atualizado com o novo código. Sem F5. Está usando 25 navegadores de testes? Ele atualiza o código em todos automaticamente sem intervenção. O browser-sync faz live reload pra você.

https://www.youtube.com/watch?v=907K7nqYesg

Mas a beleza do browser-sync é não parar aí. Ele sincroniza outras ações também. Você clica num item do menu no seu browser principal e ele propaga esse clique em todos os dispositivos conectados. Você dá scroll na página em um browser, e ele dá scroll em todos. Você digita algo no formulário de contato e ele preenche o formulário em todos os aparelhos.

É quase mágico. E facilita muito o desenvolvimento.

## Usando Browser-sync

Ele é uma ferramenta em Node.JS. Para instalá-lo, basta um `npm install -g browser-sync`. Você pode rodá-lo na linha de comando de forma bem simples. Ou, se preferir, há plugins para o Grunt, Gulp e outras ferramentas de build.

O browser-sync tem dois modos. Se estiver fazendo um site estático, ele sobe um servidor HTTP pra você na pasta que estiverem os arquivos. Ou se estiver num cenário mais comum que envolva back-end também (PHP, Java, .NET, qualquer coisa), ele roda em modo proxy.

Para o modo estático, abra o terminal, entre na pasta do projeto e rode:

`browser-sync start --server`

Ele vai executar seu projeto em http://localhost:3000. Basta abrir essa URL (ou seu IP na rede local) nos vários browsers. Ele vai sincronizar suas ações (cliques, scroll, formulários etc).

Para usar o recurso de livereload de arquivos modificados, passe pra ele que tipo de arquivo deve observar mudanças:

`browser-sync start --server --files "css/*.css, *.html"`

Agora, se mudar qualquer arquivo CSS ou HTML, ele será recarregado automaticamente em todos os navegadores.

A outra opção, proxy mode para sites dinâmicos, é bem simples também:

`browser-sync start --proxy localhost:8080 --files "css/*.css, *.html"`

Neste exemplo, ele vai proxyar um servidor rodando em http://localhost:8080. Você acessa ainda pela porta do browser-sync em http://localhost:3000 e ele delega para o servidor na 8080.

Mais uma facilidade bem interessante: rode com `--tunnel` no final e ele gera uma URL pública pelo serviço [localtunnel.me](https://localtunnel.me/). Muito útil pra usar no celular em 3G por exemplo, ou outros aparelhos fora da sua rede local.

## A interface do browser-sync

Além de subir o servidor principal, você ainda pode acessar http://localhost:3001 para acessar uma interface com usuário com **muitos recursos**.

![Screenshot 2015-05-22 14.57.14](https://blog.caelum.com.br/wp-content/uploads/2015/05/Screenshot-2015-05-22-14.57.14-1024x923.png)

Através dela, você pode:

- Ver o histórico de URLs e reexecutá-las
- Controlar que tipos de ações devem ser sincronizadas (cliques, scroll, formulários)
- Debugar remotamente com weinre
- Abrir uma nova aba em todos os navegadores
- Forçar o reload de todos os navegadores
- E outras opções

Uma bem interessante é o **debug remoto**. No desenvolvimento mobile, é bem comum você abrir o site no celular e debugá-lo no Desktop, usando Inspetor de elementos, Console de JavaScript etc. A maioria dos navegadores modernos possui um modo nativo de Debug Remoto bem simples de usar. Use o Chrome no Desktop pra debugar o Chrome Mobile só plugando um cabo USB. Use o Safari no Desktop pra debugar iOS, ou o Firefox Desktop pra debugar Firefox Mobile.

Mas muitos cenários ainda não tem uma solução simples e nativa. Não há como debugar remotamente o IE no Windows Phone, ou Android anterior a 4.4, ou browsers de smart TVs, video games e smart watches. Mesmo o iOS é possível só se você tiver um Mac. Não dá pra debugar seu iPhone no Windows por exemplo.

Para todos esses cenários, uma das soluções mais usadas é o [weinre](http://people.apache.org/~pmuellr/weinre-docs/latest/), um debugador remoto plugável em qualquer plataforma. Ele é baseado em JavaScript e resolve todos esses cenários citados.

Você pode usar o _weinre_ independentemente, mas ele **já vem integrado ao browser-sync**. Vá até a aba _Remote Debug_ na interface, clique um botão e comece a debugar todos os dispositivos conectados.

Existem vários outros recursos no browser-sync, que é uma ferramenta que melhora a cada versão. Existem alternativas pagas famosas, como o [Adobe Edge Inspect](https://creative.adobe.com/pt/products/inspect) ou o [Ghostlab](http://vanamco.com/ghostlab/). Mas o browser-sync é open source, gratuito e bastante avançado.

Nos nossos [cursos de Front-end, HTML, CSS e JavaScript](https://www.caelum.com.br/cursos-web-front-end/), falamos de ferramentas que facilitam o desenvolvimento Web, como o Grunt, e uso de live reload.

E você, que outras dicas de ferramentas tem para ajuda no desenvolvimento Web?
