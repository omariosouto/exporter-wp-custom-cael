---
title: "Redesign do site da Caelum: saindo dos anos 90 direto para os Hipsters Years"
date: "2017-05-03"
author: "natan.souza"
authorEmail: "natan.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Encontrar certos cursos no site da Caelum **era** uma tarefa bem complicada. Os cursos de NodeJS (back-end) e UX, estavam no menu de Front-end. O curso de PHP era o único item no menu que o click caia direto na página do curso.

Pensando na encontrabilidade de informações e da vontade de dar uma reformulada no layout, construímos o [novo site da Caelum](https://www.caelum.com.br).

## Como era antes

A falta de consistência funcional e da usabilidade foram os pontos principais sim, mas convenhamos que o layout estava meio _vintage_ demais:

![Site da Caelum aberto em PC antigo](https://blog.caelum.com.br/wp-content/uploads/2017/04/site-caelum-em-pc-antigo.jpg)

Se você não conhecia, dê uma navegada aqui pelo [Web Archive](https://web.archive.org/web/20170209084539/www.caelum.com.br). Dúvido você achar o [curso de Lógica de Programação](https://www.caelum.com.br/curso-logica-de-programacao).

Então os problemas principais eram:

- Falta de consistência funcional
- Encontrabilidade de cursos complicada
- Layout datado
- Pacotes de cursos (que chamamos de [Formações](https://www.caelum.com.br/formacoes)) não tinham um "cantinho" só para eles

## Como ficou

![](https://blog.caelum.com.br/wp-content/uploads/2017/04/caelum-novo-site.jpg)

Você pode [navegar](https://www.caelum.com.br/) no novo site ou ver a diferença da home acima da dobra nesse GIF também:

[![](https://blog.caelum.com.br/wp-content/uploads/2017/04/comparativo-caelum-antes-depois.gif)]( com)

## Como foi o começo do projeto e o primeiro problema

Deixar claro para o time envolvido no projeto o público que queremos atingir é importante, por isso que nas turmas do [curso de UX](https://www.caelum.com.br/curso-ux-usabilidade-mobile-web) da Caelum sempre falamos de personas e [proto-personas](https://blog.caelum.com.br/entendendo-usuario-proto-persona/). Fiz três proto-personas ([1](https://blog.caelum.com.br/wp-content/uploads/2017/04/persona-bianca-novaes-adm-01.jpg), [2](https://blog.caelum.com.br/wp-content/uploads/2017/04/persona-ivo-falcao-designer-43.jpg), [3](https://blog.caelum.com.br/wp-content/uploads/2017/04/persona-leticia-bakanovas-dev-45-35.jpg)) focando em alunos que normalmente dou aula, como o perfil que quer entrar na área de tecnologia e perfil designer desejando aprender front-end.

Não precisei fazer uma vasta pesquisa nessas proto-personas, justamente por conhecer bem o perfil dos alunos, mas cometi um erro: fiz sozinho. A ideia da persona é alinhar o usuário e o time, mas como o time não participou do processo de criação, rapidamente foi tudo ignorado. Primeira lição aprendida então é: **se for fazer persona, faça com o time**.

Mesmo as lindas personas sendo deixadas de lado _suspiro_, em toda reunião semanal fomos alinhando os perfis de alunos que focaríamos naturalmente. O fato de boa parte do time tratar com alunos diariamente ajudou bastante nisso:

- Aluno que tem um objetivo como fazer um site, aprender java, desenvolver um app;
- Aluno que sabe exatamente qual tecnologia precisa

As categorias na home e na página de [cursos](https://www.caelum.com.br/todos-os-cursos) matam o primeiro caso. E para o segundo usamos o conceito de ter logo de cara, as informações que o usuário precisa para chegar onde precisa, conceito esse chamado de "3W", _what_, _why_ e _what's next_? (o quê, por que, e agora?).

![](https://blog.caelum.com.br/wp-content/uploads/2017/04/3w-site-caelum.jpg)

 

- What: uma rápida apresentação para quem não sabe **o que** a Caelum oferece (Cursos de desenvolvimento, web...)
- Why: **por qual motivo** o aluno deveria conhecer a Caelum (Projetos práticos...)
- What's next: imagine o aluno pensando "me convenceu, agora o que eu preciso fazer nessa página?" (normalmente um _call to action_, no nosso caso a busca)

## Quais ferramentas foram usadas no processo

Usamos muitas ferramentas no processo todo, umas online, outras de UX, entre elas vale citar:

- [Hotjar:](https://www.hotjar.com/) antes de rabiscarmos qualquer coisa, analisamos algumas gravações e mapas de calor no Hotjar, recomendo fortemente a ferramenta para ter _insights_ interessantes e tentar entender como o usuário;
- [Slack:](https://slack.com/) foi nosso principal meio de comunicação, se não o conhece, por favor leia [Encontrando melhores formas de trabalhar com o Slack;](https://blog.caelum.com.br/encontrando-melhores-formas-de-trabalhar-com-slack/)
- [Trello:](https://trello.com/) conosco desde o princípio também para organizar nossas ideias e algumas referências, foi usado massivamente antes deixar o site no ar para organização e priorização de bugs funcionais e de design;
- Card sorting (aberto): técnica colaborativa de UX para organizar e categorizar itens, como menu ou mesmo telas, usamos internamente bem no comecinho quando pensávamos em categorizar os cursos por profissões, e não por objetivos. Usamos também o app Post-it Plus (só [iOS](https://itunes.apple.com/br/app/post-it-plus/id920127738?mt=8) :/)![Card sorting por profissões](https://blog.caelum.com.br/wp-content/uploads/2017/04/card-sorting.jpg)
- [Figma](https://www.figma.com/): similar o Sketch, só que online. Usamos no começo para montar wireframes e discutir a estrutura do conteúdo, além de servir como ferramenta principal para começar a fazer os protótipos juntamente com o...
- [Marvel](https://marvelapp.com/) - ferramenta online e free para você linkar as telas feitas no seu editor de imagem preferido e fazer seu protótipo.
- [Sketch](https://www.sketchapp.com/) + [Zeplin](https://zeplin.io/): o primeiro para o designer do time fazer as telas, e o segundo foi essencial para agilizarmos o desenvolvimento front-end do site. Basicamente coloca o arquivo do design no Zeplin, e ele facilita para pegar coisas como paddings e hexadecimais;
- Editores de texto/IDEs: não temos um padrão, cada um usa o seu. No começo eu estava usando o Brackets, mas depois precisei da performance do Sublime;
- Sistemas operacionais: maioria aqui usa Mac e eu sofro bullying por insistir em usar Windows ¯\\\_(ツ)\_/¯

## Antes de codar, desenhar

Aí você está doido pra codar, e já começar a montar toda  a build do projeto, Sass, Gulp, etc. Começa a fazer tudo na loucura e sangue nos olhos, para chegar lá na frente e o layout não tem nada a ver com o que você tinha pensado. Para evitar isso, por favor, **rabisque antes de codar.**

Rabiscamos algumas coisas no papel, mas logo jogamos tudo no Figma. Você pode ver algumas telas [aqui](https://www.figma.com/file/adcjOv3opwZW9kTfTqOUUe1l/Caelum-Site) ou nesse print:

![Wireframes Caelum Mobile](https://blog.caelum.com.br/wp-content/uploads/2017/04/wireframes-caelum.png)

Essa é a maior prova que pensamos primeiro em mobile (_mobile first_) para montar o site, nos preocupando desde o início que a experiência do aluno no celular seja tão confortável quanto no desktop.

E por qual motivo fazer de baixa fidelidade? Como a estrutura nem tinha sido fechada, não tínhamos nenhum _styleguide_ e também queríamos pegar problemas de usabilidade logo no começo, optamos primeiramente por focar mais na estrutura do conteúdo (arquitetura de informação). O layout seria desenvolvido um pouco mais adiante, com as principais questões de estrutura já discutidas e testadas.

## Clicou aqui e aparece qual tela mesmo?

Você pode montar suas telas e colocar o usuário para ir passando no app de fotos do seu celular por exemplo, mas como que ele usa um site normalmente? Clicando e navegando. Logo seria bacana que as telas tivessem um comportamento bem parecido com um site já no ar, por isso é interessante prototipar, para conseguir testar coisas como o fluxo de navegação e depois alguns erros de usabilidade. Que seja protótipos de baixa, média ou alta fidelidade. Para isso usamos o Marvelapp, o mesmo que os alunos utilizam no [curso presencial de UX](https://www.caelum.com.br/curso-ux-usabilidade-mobile-web).

Então ficou assim: telas estáticas feitas no Figma e link entre essas telas, agora exportadas, no Marvel.

Fizemos diversos protótipos, e conforme o layout foi sendo desenvolvido, fomos aplicando as cores e tipografia neles. Dá uma olhada no primeiro protótipo:

<iframe src="https://marvelapp.com/chhgge9?emb=1&amp;iosapp=false&amp;frameless=false" width="390" height="755" allowtransparency="true" frameborder="0"></iframe>

Nem sabíamos o que colocar no menu nessa hora, mas já foi bacana para testar a encontrabilidade de certas informações, antes semi-escondidas, agora mais visíveis e de fácil visualização. Chegamos até a testar o famoso _burguer icon_ ([veja o protótipo com ele aqui](https://marvelapp.com/4hd5j8e)), mas acabamos eliminando a ideia para enxugar o máximo possível o menu principal.

No final dessa primeira fase dos testes de usabilidade, o protótipo já tinha uma cara bem mais parecida com um site real e bem mais fiel ao layout final. Digo "primeira fase" pois o ideal é sempre testar seu projeto, não importa em que ponto está.

 

<iframe src="https://marvelapp.com/533271f?emb=1&amp;iosapp=false&amp;frameless=false" width="402" height="823" allowtransparency="true" frameborder="0"></iframe>

No futuro próximo farei um post exclusivo sobre o tema de teste de usabilidade e contando como foi a experiência. Enquanto isso você pode ouvir o [Hipsters.tech sobre prototipação](http://hipsters.tech/prototipacao-erre-cedo-para-acertar-cedo-hipsters-28/) e o sobre [Design Sprint](http://hipsters.tech/design-sprint-hipsters-23/).

## Decisões e problemas da vida

Mudamos de _weekly_ para reuniões 2x por semana (segundas e quintas), mais rápido para ter feedback do andamento das coisas e deixar tudo alinhado.

No lado do back-end, foi discutido três opções:

- Manter a estrutura toda atual em Java
- Fazer tudo do zero em Java
- Fazer tudo do zero em PHP

Em altos papos "desenvolvedorísticos" o time optou por manter o back-end em Java do site antigo, visando ganhar tempo pois muitas decisões de negócio e de performance já tinham sido pensadas e desenvolvidas. Eu no entanto por não ter conhecimento técnico em back-end, [não consegui opinar](http://geradormemes.com/media/created/lva8ir.jpg).

Layout definido, pelo menos as páginas principais, começamos a desenvolver o front-end, enquanto uns foram arrumando a infra, outros codando HTML e CSS. E quase que paralelamente esses HTMLs desenvolvidos foram passados para JSP (Java things...). Uma opção que tínhamos de infra era continuar usando pré-processador (LESS como no antigo ou colocar o Sass, por exemplo), mas optamos por fazer tudo com CSS normal (componetizado, mas normal), e caso sentíssemos necessidade no correr da carruagem voltaríamos a discutir. E até que surgiu essa necessidade, mas estávamos ligeiramente atrasados, então iremos rever essa decisão mais pra frente, levando em conta o uso do PostCSS também.

E foi mais ou menos nessa época de começar o front-end, que começaram as "tretas da vida". Dar aula em outros estados, férias já programadas, feriados e doenças (duas viroses em questão de duas semanas) foi a Lei de Murphy aplicada num projeto.

Interessante dizer que não demos a devida importância para diversas páginas, como a página de [Cursos In-Company](https://www.caelum.com.br/cursos-in-company) e fomos alertados por pessoas diferentes de dentro da empresa, como instrutores de outras unidades da Caelum ou mesmo do departamento comercial. Falha nossa por ter envolvido o comercial somente no final dessa primeira fase.

## Site subiu

![Its happening gif](https://blog.caelum.com.br/wp-content/uploads/2017/04/its-happening.gif)

Era pra ser lindo, mas logo notamos problemas de performance, o site estava extremamente lento. Tinha alguma agulha que não estávamos vendo. No caso, uma agulha de pé meio inclinada para o lado. Por conta de regras de redirect + código Java antigo + código Java novo, em todo _request_ de uma URL que não existia, acabava consumindo CPU loucamente o que deixava todo o site lento, tudo por conta de uma barra. Mas agora todas as URLs estão sem a ordinária barra no final "/" e o time até voltou a falar comigo.

## Trabalho finalizado

Será? Não é porque o site subiu que o trabalho acabou. Depois de uns dias com o site no ar ainda aparecem páginas não previstas que nem são indexadas pelo Google, como páginas de algumas parcerias que a Caelum possui. Há um bom número de bugs de design para serem corrigidos também (um card de bug por dia, deixa o site bonito e com alegria). Ainda há muita coisa para testar, faremos mais testes de usabilidade a fim de colocar a prova teorias e ideias que tivemos, e outras que vamos ter também.

Agradecimentos a toda a _staff_ que trabalhou diretamente com o projeto:

- [Alberto Souza](https://www.caelum.com.br/instrutores/alberto-souza)
- [Art Diniz](https://www.caelum.com.br/instrutores/artur-diniz)
- [Eric Vinicius](https://www.linkedin.com/in/eric-vinicius-09a95659/)
- [Gui Lombardi](https://cursos.alura.com.br/user/guilherme-lombardi)
- [Joviane Jardim](https://www.caelum.com.br/instrutores/joviane-jardim)
- [Yuri Padilha](https://www.caelum.com.br/instrutores/yuri-padilha)

E toda a diretoria e pessoal que ajudou indiretamente também.
