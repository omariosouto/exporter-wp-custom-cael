---
title: "Conhecendo o Flutter, e uma visão do desenvolvimento mobile"
date: "2019-04-26"
author: "marcio.souto"
authorEmail: "mario.souto@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

## Introdução  

Eu trabalhei com desenvolvimento Web boa parte da minha vida, hoje estou transitando entre a Web o mundo mobile através do React Native e estou de olho nas tendências novas para o desenvolvimento móvel e ando explorando bastante um carinha ai chamado Flutter. A ideia aqui é compartilhar um pouco do que é essa ferramenta, quais as diferenças dele para as outras alternativas que temos hoje no mercado.  

A ideia desse post é fazer uma visão geral sobre as alternativas que temos hoje no mercado para trabalhar com mobile e entender um pouco mais sobre como o Flutter funciona e a ideia por trás dele, coisas boas, ruins… enfim bora pro post!  

## **O cenário do desenvolvimento mobile hoje**  

Hoje existem diversas formas de criar aplicativos mobile, a padrão é usar Java/Kotlin para codar em Android e Swift/Objetive-C (dependendo do legado que você tem) para programar para o mundo iOS. Embora isso seja o padrão, para desenvolver assim é necessário ter profissionais que demandam conhecimentos das duas plataformas e existem outras dificuldades como sincronizar as features que existem na app de uma plataforma exista na outra e conseguir evoluir a codebase de forma conjunta.  

Uma alternativa para isso seria ter uma base de código só que rodasse em ambas as plataformas, as pessoas pensam isso desde muito tempo, sempre que alguém precisa distribuir uma aplicação parecida/igual para mais de uma plataforma surge alguém tentando resolver esse problema e no mundo dos aplicativos surgiu o Apache Cordova e a ideia de usar WebViews

## **Por dentro das WebViews**  

![](https://i0.wp.com/blog.caelum.com.br/wp-content/uploads/2019/04/image.png?fit=1024%2C467&ssl=1)

A ideia é maravilhosa, escrevemos com as tecnologias da Web, criamos as interfaces e é possível rodar um código só, cross plataform. Com os adventos das SPAs, frameworks front end que facilitaram trazer páginas mais dinâmicas baseadas em dados vindos de APIs e transições de tela mais próximas dos aplicativos nativos, isso foi ganhando cada vez mais popularidade principalmente para projetar MVPs ou mesmo aplicativos pra valer. Além dessas, outras tecnologias foram surgindo no ecossistema das Webviews dando um suporte melhor para tudo isso como Phonegap, Ionic e outros.

Essa praticidade toda acaba tendo um custo:  

![](https://lh6.googleusercontent.com/1QOaIpj6RTr6N_YyLSF0G6zqDGR6jXu6HEJd99eGSTl2fxmtbpSP5qHycqUKRmVH0JOsYzz9Y1qmCoDeJ2IDgv8yCU6ZRzr8jp95172xVd-ah7ZyLMeLqF_OfEfm07jLCQxdqwoE)

Fluxo de como é a execução de uma app em webview

  
Nossa aplicação é carregada por meio de uma WebView dentro da casca de um aplicativo. Ou seja nossa app é basicamente um site e para o usuário final, quando a aplicação inteira é feita dessa forma, em alguns casos, pode acabar gerando uma estranheza em algumas animações ou interações, pois a diferença de performance em vários casos é gritante. Sem contar que não temos o acesso direto a recursos nativos como câmera, sensores, bluetooth… sempre ficamos a mercê de plugins que a comunidade cria que conforme o tempo vai passando, novas atualizações das APIs nativas como a permissão em runtime para acessar a câmera que se não for adaptada simplesmente vai quebrar o App. O maior problema é que do dia pra noite, podemos acabar ficando órfãos de alguns deles, a menos que seu time possa contar com alguém que consiga abrir a lib e adaptar alguma coisa.

## O tal do React Native

Hoje em dia, quanto falamos de aplicativos com uma codebase só (rodar o mesmo código em diferentes plataformas) a primeira coisa que vem na cabeça de muitas pessoas para o mundo mobile é o React Native. Ele possui uma série de vantagens:  

- Possibilidade de criar telas no estilo React Way of Life, onde você tem um state com seus dados e a partir de alterações nele a sua interface é alterada. A mesma ideia que veio com a lib do [React pra web](https://reactjs.org)
- Ter componentes reutilizáveis
- Hot Reloading, não precisar recompilar tudo para ver alterações (fiquei chocado quando desenvolvi pra android a primeira vez e isso não existia).
- Possibilidade de uma única linguagem para escrever interfaces em sua empresa, [o Banco do Brasil tem um ótimo case disso.](https://www.infoq.com/br/presentations/react-native-no-banco-do-brasil-como-rn-nos-faz-poupar-1500-horas-por-projeto)
- Reaproveitar os conhecimentos do mundo Web com React, várias libs como o [Formik](https://jaredpalmer.com/formik/docs/guides/react-native), [Axios](https://github.com/axios/axios) que são mega comuns para quem desenvolve com React rodam no React Native com algumas variações mas a essência mantida.
- [Codepush](https://medium.com/reactbrasil/react-native-codepush-atualiza%C3%A7%C3%B5es-r%C3%A1pidas-sem-burocracia-1a880490aabc), essa sem dúvida pra mim é uma core feature do React Native, poder subir uma atualização da sua app sem precisar lançar uma nova versão traz uma possibilidade que nunca tivemos antes no mundo dos aplicativos e junto com essa possibilidade temos também um pouco mais de independência das empresas por trás das lojas, após subir uma vez vários updates podem ser feitos via codepush. (Muita gente tem medo de fazer o processo, mas ainda sim a ideia de ter a possibilidade de lançar updates sem esperar pelas lojas é fantástica).

Para isso tudo funcionar existe uma estrutura que podemos simplificar em: **JavaScript, Bridge, Nativo**.  

![](https://lh5.googleusercontent.com/jYoA97FUNtypRN3QI1Yp6zvsA7WKrLabtzT8JDtlc7BNk9pOW4Su_S5YTsAP-nLj7f1AONp1kNaf_B8jJvz5ch7RYeQF1XI-O16Ss-PpHgTLPnEg9g_samRJ2-2vEmUe5fgbsFRI)

Nossa aplicação é basicamente um arquivo JavaScript gigante que interage com uma ponte que faz a conversão de tudo o que fazemos em JavaScript e as alterações feitas no lado React para o lado Nativo interagindo com Widgets Nativos das plataformas (se você quiser uma [visão bem mais hacker desse funcionamento, recomendo assistir essa talk](https://www.youtube.com/watch?v=_3nu7z_xweM) fantástica do [@tadeuzagallo](https://twitter.com/tadeuzagallo)). Funciona super bem e atende diversos casos, tanto que existem muitas apps usando como o Nubank, Facebook, Gympass… Mas como nem tudo são flores o bridge pode causar alguns problemas de performance, como no caso de animações onde ele pode chegar a ser acessado 60 vezes em animações, swipes e outras interações do gênero. [Se você quiser ver exemplos de algumas limitações, deixo aqui um link para uma futura leitura sobre o tema](https://medium.com/@talkol/performance-limitations-of-react-native-and-how-to-overcome-them-947630d7f440), mas por hora vamos continuar que o próximo carinha que vamos comentar é o tal do Flutter.

## **Conhecendo o novo Hype/Flutter**

Agora sim, chegamos no objetivo do post, vamos começar sendo justos e falando quais as vantagens de usar o tal do Flutter

- Possibilidade de criar telas no React Way of Life, onde você tem um state com seus dados e a partir de alterações nele a sua interface é alterada.
- Ter componentes reutilizáveis
- ...

De início é isso mesmo, o que o React Native trouxe com os componentes e o conceito de state acaba meio que sendo uma das inspirações do time do Google por trás do Flutter para criar a nova ferramenta, ele começa a ter mais vantagens quando vamos para a forma como ele funciona. Diferente do React Native o Flutter não possui a bridge de JavaScript como um meio de campo para acessar os widgets nativos de cada plataforma, o Flutter cria os widgets diretamente onde as plataformas criam os widgets nativos:  

![](https://lh6.googleusercontent.com/ip6UVEnPSR3e8LIYHANAOoGtJjIdeRPwIYAySD9y4xP9iIjESrnS1_0xewXonfi42Uu5HweDpwof5Z3S4oBZ_Wn9UGSG-7ik-QKGzNC_oDplqKxTDCrw3LbMROcw67GRqSuoSRyt)

Fluxo de como o Flutter funciona

Isso é possível graças ao fato de que para trabalhar com o Flutter usamos uma linguagem chamada Dart. O fato não se deve só a linguagem, o ponto aqui é que ela é compilada inicialmente inicialmente “ahead of time” (AOT) para múltiplas plataformas (como binário e como código no caso do JavaScript) e quando nosso aplicativo é aberto os widgets que criamos e foram compilados interagem diretamente com a API de Canvas das plataformas, essa por sua vez, é a responsável por desenhar o que vemos na tela. Vale ressaltar que o fato do Dart ser compilado para o código nativo diminui o tempo de boot das aplicações (Como o React Native precisa processar o código do JavaScript todo em alguns casos isso pode ter uma diferença e mesmo que pequena vale comentar).  

Além do que eu achei legal, acho super importante deixar bem claro o que não gosto do Flutter:

- Embora usar [Dart](https://www.dartlang.org/) traga a ideia do código compilado pro nativo, me sinto mais atraído pela ideia ideia do React Native de usar JavaScript e conseguirmos reaproveitar os conceitos da Web e aplicar no mundo mobile.
- Não termos uma solução para CodePush (depois de me acostumar no React Native é bem sofrido), mas pelo que tenho acompanhado no Roadmap da evolução do Flutter isso vai ser possível e em breve: https://github.com/flutter/flutter/wiki/Roadmap#dynamic-updates
- O jeito como ele lida com a separação de estilizações simples como Padding e deixar uma imagem arredondada que vou falar de uns exemplos mais abaixo ainda nesse post. No fim eu por ter um background Web acabo ficando enviesado a gostar mais do formato das Stylesheets do React Native.

### **E por que valeria a pena usar o Flutter?**  

A experiência de desenvolver com uma ferramenta aparentemente estável é bem legal, eu digo isso, pois ele já está em sua versão 1 que foi lançada em 4 de dezembro de 2018. Não cheguei a fazer milhares de integrações mas no que diz respeito a acessar recursos externos, gerenciar navegação, debugging entre outros tópicos consegui fazer tudo sem problemas. A integração com VSCode, Android Studio, IntelliJ e xCode está bem bacana e funcional (tive bem menos crashs por segundo do que quando comecei com React Native).  

![](https://lh3.googleusercontent.com/yp1Bfc-vYkZ0S6m1UT_JE39rzUDYIVwBTwQJopWTBc0WshuQF96XvJjfOF9lIrcgyDx8RL-aNuYGm9l9HwMBHF4-TD_zmPcM-tFUWMc9I-OeMU5XtYYCdg-Dklk6QrNkfpgq4fIV)

Projeto flutter rodando no VsCode

O suporte para trabalhar com erros roda bem até quando algum problema ocorre com tudo rodando via terminal, o que gera uma independência bem grande de ter um editor como o VSCode ou alguma IDE (embora eu aconselhe você usar sempre um sempre que for trabalhar com Flutter ou outra tecnologia).  

![](https://lh5.googleusercontent.com/_dgvbWDHK1PGxdNJKxpWjJUi3GTXnoFr4e4s-VqAybHDn86JSitpwp_FFWyKJqI-6UVrCPFvznsHeAEXy-UbszeZA6PwZWzi7sDWDSP1PWBR9oIEkfmVtU6PKAn6xQ16z1z-O4G1)

Feedback de erros do flutter

Falando em erros, uma feature sensacional que nos ajuda a encontrar algum bug ou erro de forma bem rápida é o suporte ao hot reloading que existe graças a compilação "JIT" (Just in time) que o Flutter usa e abusa da Virtual Machine do Dart em modo de debug, e nos ajuda a identificar alterações na interface de forma mais rápida:  

- ![](https://i1.wp.com/blog.caelum.com.br/wp-content/uploads/2019/04/hotreload.gif?fit=1024%2C561&ssl=1)
    

E esse formato de hot reload tem uma integração fantástica com o VsCode (onde eu mais codei até agora), que nos permite fazer breakpoints sem precisar executar nenhum comando adicional:  

- ![](https://i1.wp.com/blog.caelum.com.br/wp-content/uploads/2019/04/breakpoints.gif?fit=1024%2C561&ssl=1)
    

### **Mas e essa sintaxe de criar tela ai, cade as tags?**  

O Flutter todo é baseado em layouts programáticos/view code, ou seja, aqui não temos JSX ou tags que nos ajudam a escrever o código, tudo são classes e sempre que vamos trabalhar com alguma alteração de valor existe uma outra classe com as configs necessárias para fazermos as operações. Um exemplo legal/triste disso é para fazermos um padding por exemplo, precisamos colocar um componente Padding com a propriedade padding em volta do ícone:  

![](https://lh3.googleusercontent.com/4ef-27ithFEKq8utXQVf-412ApndDniVCfRm7IDnTech0MwXr2C8V0EiXMPc5WKC50qfFw64ss79c78vyZlEq8sfNJxInnJJuthB7ua-9UkNFyX-AEUxm623n-_cfAsIJ5ePyua9)

Enquanto em outros lugares como quando estamos utilizando um componente Text, podemos passar um atributo style que recebe um TextStyle com algumas customizações visuais, ao meu ver todos os componentes poderiam ter um atributo style, me incomoda sempre ser um mistério que eu descubro via documentação ou via Ctrl + Espaço no editor o que é customizável em determinado elemento.  

![](https://lh6.googleusercontent.com/TFUNMmIKMIb0ShiQajaFN8SMDa3kmF3Uj3O2oZU8z50W5v936sd5yaQlJAo9GA5K8rtJw40JPKliZuVj4g-59sSL9XRayPLXF2JGV1c7PTBFTT-2zJlgXsPfVCCq7zntfYz_v8hY)

Achei bem legal que para trabalhar com imagens existe um componente, até semelhante com a tag img que temos no HTML  

![](https://lh6.googleusercontent.com/OL5nSpCo6VW8Dp4tGfJGRsVTYmy9ENJ-xmjFKNKkr8OexWZD38kOb37vf73dflaAyEFZugpw4QM6mkRoaiccNijwMcxDBgUXV6ZzL-G7wMCaLWFFpg7z5P6grDYlUcb-nxMP9qI2)

Porém quando quis deixar ele redondinho para usar no header do instafake eu precisei usar um componente que nem sabia que existia, o BoxDecoration:  

![](https://lh6.googleusercontent.com/VeJgHOouRy0xhUJSaQiZMOMKvZq3MtjxfCJsECiRmVOjK4NLlK2eKONW5GlMI3zlcUVzzmoRwNPdDlCe93Y6P3lz1qUWufEVrWgZWWDYenJv4HjZQJ3jWE4OvJRkz-14TdiCGLN9)

Talvez exista alguma forma mais fácil que eu não descobri ainda, mas achei bem bizarro não poder só dizer que a imagem é circular ou algo assim.  

Para quem vem do Front como eu isso tudo pode gerar uma estranheza #saudadesCSS, mas o Flutter tem uma página dedicada a explicar como nós podemos aprender a converter a forma de pensar do front end [https://flutter.dev/docs/get-started/flutter-for/web-devs](https://flutter.dev/docs/get-started/flutter-for/web-devs) e todos os componentes que ele nos libera são altamente customizáveis e super bem documentados.

Eu também escrevi um [post que pode ti ajudar no get started com a ferramenta, vale a pena dar uma olhadinha :)](https://blog.alura.com.br/como-criar-um-projeto-com-flutter-hello-world/)

## **Conclusão**  

Bom, eu estou fazendo altos experimentos com o Flutter e vários de seus recursos e estou tirando ainda novas conclusões e até mesmo descobrindo, não vou dizer que ele é melhor nem pior que o React Native ou outra solução, ainda existe muito chão pra rolar e muita coisa para ver no projeto que venho trabalhando com ele.

Já tenho bastante material para compartilhar e ao longo das próximas semanas vou compartilhar minhas pesquisas e trazer mais conteúdos, então se você curtiu, não deixe de seguir minhas redes sociais e dar uma olhadinha no meu site que toda semana ta saindo algo novo por lá [https://mariosouto.com.br](https://mariosouto.com.br)  

Até a próxima :)  

**Referências:**  

- [https://hackernoon.com/whats-revolutionary-about-flutter-946915b09514](https://hackernoon.com/whats-revolutionary-about-flutter-946915b09514)
- [http://www.vensi.com/benefits-of-phonegap-cross-platform-mobile-app-development/](http://www.vensi.com/benefits-of-phonegap-cross-platform-mobile-app-development/)
- [https://www.youtube.com/watch?v=\_3nu7z\_xweM](https://www.youtube.com/watch?v=_3nu7z_xweM)
- [https://hackernoon.com/whats-revolutionary-about-flutter-946915b09514](https://hackernoon.com/whats-revolutionary-about-flutter-946915b09514)
- [https://www.youtube.com/watch?v=fq4N0hgOWzU](https://www.youtube.com/watch?v=fq4N0hgOWzU)
