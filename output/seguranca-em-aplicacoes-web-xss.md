---
title: "Segurança em aplicações Web: XSS"
date: "2008-11-25"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Bons tempos aqueles quando os computadores não eram ligados em rede, não existia Internet e as aplicações eram todas Desktop. Em tempos de Web 2.0, Mashups e Aplicativos Web, as dores de cabeça são absurdamente maiores assim como o número de aplicações expostas a ataques. Seu sistema é seguro?

Ultimamente tenho notado como poucas pessoas se preocupam de verdade com segurança na web, ou pior, como os desenvolvedores desconhecem os problemas envolvidos na programação pra web. É preciso entender como o HTTP funciona, como os browsers funcionam, o que o JavaScript é capaz de fazer e até as implicações de segurança dos _ingênuos_ HTML e CSS.

Pensando nisso, resolvi escrever uma série de artigos sobre segurança em aplicações Web e começo falando do famoso **Cross-site Scripting**, carinhosamente chamado de **XSS**.

**Samy is my hero**

Em 2005, Samy Kankar, insatisfeito com sua pequena quantidade de amigos no [MySpace](http://www.myspace.com), escreveu um worm (depois batizado de **JS.Spacehero**) para turbinar sua lista de amigos.

O script rodava quando alguém simplesmente visitava o perfil de Samy, adicionava o visitante à sua lista de amigos e colocava no perfil do infectado a frase "**samy is my hero**". Além disso, o script se propagava para o perfil do usuário infectado, possibilitando que esse infectasse mais pessoas.

Em menos de 20h, Samy tinha um milhão de amigos e o MySpace foi tirado do ar. A aventura ingênua de Samy (com a falha que ele descobriu, poderia ter feito algo bem pior) foi [narrada de forma muito cômica no site dele](http://namb.la/popular/). E, em 2007, lhe rendeu uma condenação nos tribunais com direito à multa, serviço comunitário e afastamento de computadores.

Mas o que o bravo Samy descobriu?

**Não confie nos inputs de seus usuários**

O MySpace é conhecido pela papagaiada permitida nos perfis dos usuários. Diferente do Orkut, por exemplo, o dono do perfil do MySpace pode mudar praticamente todas as características visuais do site (fundo, cores, letras, imagens, vídeos etc). Para isso, o MySpace aceita que o usuário digite HTML e CSS para personalizar sua página, mas com limites para a coisa não virar zona.

O que Samy fez? Descobriu uma falha no filtro do MySpace que limita os inputs e conseguiu colocar em seu perfil um código JavaScript que seria executado por cada visitante do seu perfil. Com técnicas de Ajax e um pouco de tempo livre, Samy fez um JavaScript que executava as ações descritas acima.

**XSS - Cross-site scripting**

Um ataque **[XSS](http://en.wikipedia.org/wiki/Cross-site_scripting)** é aquele que permite a injeção de scripts no site atacado, em geral por meio de algum campo de input do usuário. Note que um ataque XSS é um ataque ao usuário do site e não ao sistema servidor em si. Isso porque o script injetado nunca será executado no servidor, mas sim nos navegadores dos clientes que visitarem a página infectada.

A regra de ouro de qualquer sistema com input de usuários é que **não devemos confiar jamais naquilo que o usuário digitou**. Além de XSS, podemos ser alvos de muitos outros ataques, como SQL Injection, injeção de parâmetros e outros (quem sabe abordamos num artigo futuro).

Embora pareça simples, a [OWASP](http://www.owasp.org/) (Open Web Application Security Project) diz que nada menos que 9 em cada 10 sites estão vulneráveis a esse tipo de ataque. Anualmente, eles fazem um [estudo](http://www.owasp.org/index.php/Top_10_2007) das 10 falhas mais encontradas na Web, e no ano passado, [XSS foi o campeão](http://www.owasp.org/index.php/Top_10_2007).

**Como se proteger**

Todo input do usuário deve ser _sanitizado_ antes de qualquer coisa. Isso significa passar algum tipo de filtro que consiga remover tags potencialmente perigosas.

Se o seu site não deve ser customizado pelo usuário de nenhuma forma e o input dele deveria ser texto puro, basta remover toda e qualquer tag encontrada. Mas o caso mais complicado (aqui entra o MySpace) é quando você deseja permitir certas tags (negrito, por exemplo) e qual abordagem seguir para filtrar as tags não desejadas.

É considerado, hoje, total insanidade mental e causa da maioria das vulnerabilidades de XSS, tentar escrever esse filtro de input sozinho. Ele é _extremamente_ complexo. Pense no caso simples, aquele onde não desejamos aceitar nenhuma tag. 99% das pessoas pensariam que o código abaixo faz um excelente trabalho: `String limpo = input.replaceAll("<", "").replaceAll(">", "");`

É um código Java que transforma: `<script>alert('XSS');</script>`

...nisso (claramente inofensivo): `scriptalert('XSS');/script`

E o código abaixo então? `+ADw-script+AD4-alert('XSS')+ADw-/script+AD4-`

Esse código é executado por qualquer navegador atual usando codificação UTF-7. E obviamente aqueles nossos "replaceAll" não resolvem o problema. Há [centenas de outros exemplos](http://ha.ckers.org/xss.html) que usam encodings estranhos, html entities, carateres especiais no meio etc.

E isso com o objetivo de tirar todas as tags da frente. E quando queremos que algumas sejam aceitas? Quais tags são seguras? E os atributos nessas tags? Até um <img> com atributo src apenas está vulnerável se não tomarmos cuidado.

Moral da história: fazer os replacezinhos na mão é insano. Não fazer nada a respeito é suicídio.

**Arrumando o problema**

Precisamos sanitizar o input dos usuários e precisamos de algo pronto que faça isso. Há trocentos projetos por aí que fazem esse tipo de serviço. Em especial, o [AntiSamy](http://code.google.com/p/owaspantisamy/) do pessoal da OWASP que tem versões em Java e .Net. A Microsoft tem uma [API anti-XSS](http://msdn.microsoft.com/en-us/library/aa973813.aspx) para .Net. Há ainda o [HtmlPurifier](http://htmlpurifier.org/) para PHP, o [sanitize](http://api.rubyonrails.com/classes/ActionView/Helpers/SanitizeHelper.html) do Ruby on Rails e muitos outros em outras plataformas.

O [AntiSamy](http://code.google.com/p/owaspantisamy/) é bastante customizável, permitindo que definamos níveis diferentes de purificação através de regras em um XML. Ele ainda é capaz de mostrar mensagens de erros amigáveis para os usuários. Usá-lo é bem simples:

`AntiSamy as = new AntiSamy(); CleanResults cr = as.scan(suspeito); String limpo = cr. getCleanHTML()`

É considerada boa prática também purificar a saída dessas strings, por precaução. Com JSP, isso é muito fácil:

`<c:out value="${suspeito}" escapeXml="true" />`

**Conclusão**

XSS é um problema real e **muitas** aplicações estão vulneráveis. [Samy atacou o MySpace em 2005 e conseguiu 1 milhão de amigos](http://namb.la/popular/). [A busca do Google tinha as falhas do UTF-7 comentadas acima em 2005](http://www.securiteam.com/securitynews/6Z00L0AEUE.html). [Apoiadores do Firefox sequestraram as maiores comunidades do Orkut em 2005 e mudaram os logos para o do Firefox](http://www.cocadaboa.com/archives/004126.php). [Eleitores de Hillary Clinton redirecionavam visitantes do site de Barack Obama para o site de sua adversária nas prévias da eleição americana em abril desse ano](http://blogs.zdnet.com/security/?p=1042). [E milhares de outros casos públicos](http://xssed.com/).

Proteja sua aplicação!

**Referências**

- [Wikipedia](http://en.wikipedia.org/wiki/Cross-site_scripting)
- [OWASP](http://www.owasp.org/index.php/Cross-site_Scripting_%28XSS%29)
