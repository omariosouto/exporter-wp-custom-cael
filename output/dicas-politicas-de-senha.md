---
title: "Algumas dicas importantes sobre políticas de senha"
date: "2018-07-18"
author: "yan.orestes"
authorEmail: "yan.orestes@alura.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Recentemente, realizei alguns testes de segurança em uma aplicação web que mantenho online.

Ao final, consegui acesso a uma grande parte das contas de diferentes usuários. Mesmo assim, os resultados do teste me mostraram que meu sistema, no geral, estava seguro. Não havia uma vulnerabilidade no meu banco de dados, nem uma falha própria do sistema. O problema, na verdade, estava nas senhas dos usuários!

O fato é que, por algum motivo, meus usuários estavam usando **senhas fracas** para suas próprias contas. E por que isso? Ademais, por que eu deveria me importar com isso? Afinal, aparentemente o problema de segurança não está do lado da aplicação, mas sim dos usuários… certo? **Na verdade, não!**

Desconsiderar esse problema (e é um grande problema!) pode se mostrar ser um grande erro. Em primeiro lugar, temos que levar em conta que o comprometimento das contas dos nossos usuários implica em um comprometimento da segurança da aplicação em si, o que é diretamente prejudicial para nós do lado do desenvolvimento.

Complementando isso, é tão ingênuo quanto malicioso acreditar que a responsabilidade de se ter senhas seguras está apenas nas mãos do usuário. É claro, não podemos (ou ainda, não devemos) garantir que a senha do usuário seja ideal e segura quanto desejamos. Mesmo assim, nossa participação nessa escolha de senha pode ser importantíssima.

Por conta disso, a maioria dos serviços online, hoje em dia, tem algumas políticas de senha que ditam regras sobre elas para o usuário. Dentre estas:

- "_Sua senha deve conter pelo menos 8 caracteres_";
- "_Sua senha deve conter um número, um símbolo, letras maiúsculas e minúsculas_";
- "_Você deve alterar sua senha a cada 90 dias_";
- "_Sua senha não pode conter caracteres repetidos_"

**e mais um monte que encontramos por aí!**

A questão é: quais dessas regras todas são realmente úteis e contribuem de fato para a segurança de nossa aplicação? Para alguns, a resposta pode parecer clara, mas a coisa vai mais fundo do que aparenta.

Sendo assim, quais são os tópicos que devemos levar em consideração para desenvolver nossa política de senhas? Com essa questão, bolei uma lista de algumas dicas que todo desenvolvedor deve considerar para construir as políticas de senha de sua própria aplicação:

- [Evite regras estáticas (ou a farsa da senha complexa)](#regras-estaticas)
- [Adote um limite mínimo no comprimento das senhas](#tamanho-minimo)
- [Não limite um tamanho máximo de senha](#tamanho-maximo)
- [Não se prenda à tabela ASCII](#unicode)
- [Calcule a força da senha dinamicamente](#zxcvbn)
- [Considere os casos locais](#casos-locais)
- [Dando suporte a gerenciadores de senha](#gerenciadores-de-senha)
- [Não imponha políticas de expiração de senha](#expiracao)
- [Seja sincero com o usuário](#seja-sincero)
- [Entenda os limites de segurança da senha](#limites-da-senha)

## Evite regras estáticas (ou a farsa da senha complexa)

Infelizmente, o que encontramos de mais comum nas políticas de senha existentes hoje em dia é um dos pontos que mais machuca toda a segurança - regras excessivas e estáticas.

A falsa crença de que podemos classificar senhas "boas" em um escopo com definições permanentes, junto com a [síndrome dos chimpanzés na Internet](http://blogs.tedneward.com/post/chimpanzees/), acabou nos levando a complexidades como:

- Sua senha deve conter pelo menos 8 caracteres
- Sua senha deve conter pelo menos uma letra maiúscula (A-Z) e um número (0-9)
- Sua senha deve conter pelo menos um símbolo (`@`, `!`, ...)
- Sua senha não pode conter uma sequência de 3 caracteres iguais

Todas essas regras podem parecer boas, e isso é porque elas realmente **podem** ser boas! Seguindo-as, poderíamos, por exemplo, chegar em uma senha como `8|hOy7!.G\`, que é uma senha relativamente boa (apesar de facilmente quebrável em um [ataque de força bruta](https://en.wikipedia.org/wiki/Brute-force_attack) offline).

O grande problema que essas regras ignoram é que os usuários, no geral, não querem memorizar senhas como `8|hOy7!.G\`; o usuário é esperto e quer simplicidade.

Assim, se atrapalhamos de cara a usabilidade de nossa aplicação, obrigando o usuário a seguir nossas regras estritas, é quase certo que ele encontrará uma brecha nisso tudo para fazer do jeito dele. Seguindo essas mesmas regras, chegamos em uma senha como `Senh@123`, que é terrivelmente insegura, como já discutimos no post sobre [criação de boas senhas](http://blog.alura.com.br/como-criar-uma-boa-senha/).

Pensando nisso, devemos **sempre** levar em conta a [Regra de Usabilidade do AviD](https://security.stackexchange.com/a/6116) - **"segurança às custas da usabilidade vem às custas da segurança"**. Não adianta desconsiderar o aspecto humano se nossa aplicação é feita para humanos!

À medida do possível, então, devemos abandonar essas políticas de senha estáticas e proibitivas, que, no geral, não contribuem com a segurança da aplicação (chegando a atrapalhar, muitas vezes) e ainda irritam o usuário. Mas será que não precisamos de nenhuma regra, então?

## Adote um limite mínimo no comprimento das senhas

Já vimos que limites restritivos não são muito legais nas políticas de senha. Afinal, devemos permitir qualquer senha? Bem, assim voltaríamos para o ponto que começamos, não é?

Liberdade para o usuário conseguir criar sua senha é importantíssimo, mas a liberdade total pode levar a senhas tão inseguras como `abc`, `01`, dentre outras aberrações.

Considerando isso, podemos voltar [àquela famosa tirinha do xkcd](https://xkcd.com/936/), que também discutimos no post sobre [criação de boas senhas](http://blog.alura.com.br/como-criar-uma-boa-senha/).

Na tirinha, `correto cavalo bateria grampo` ganha de `Tr0ub4dor&3` por dois motivos: o aspecto humano (no quesito memorização), que já vimos que é importante, e o próprio aspecto de segurança - `Tr0ub4dor&3`, por mais complexa que pareça, é menos segura.

Nesse caso, a segurança se baseia, principalmente, no comprimento da senha, o que pode ser demonstrado através da matemática.

Pensando em dois exemplos, podemos construir duas funções matemáticas para calcular quantas possibilidades de senha existem, um com uma senha de _n_ caracteres, mas que devem estar no grupo de letras do alfabeto (ou seja, 26 possibilidades, como `corretocavalobateriagrampo`):

`possibilidades = 26^numero_de_caracteres`

e outro com uma senha de 11 caracteres fixos (como `Tr0ub4dor&3`) que podem ser de um grupo de _n_ caracteres distintos:

`possibilidades = caracteres_possiveis^11`

Como o crescimento exponencial é mais acelerado que o crescimento polinomial, montando ambas as funções em um gráfico, temos o seguinte:

[![Crescimento exponencial vs crescimento polinomial](https://blog.caelum.com.br/wp-content/uploads/2018/07/growth.png)](https://blog.caelum.com.br/wp-content/uploads/2018/07/growth.png)

Com o gráfico, podemos facilmente ver que, por volta de 6 caracteres (de opção, na função vermelha, e de tamanho, na função azul), o crescimento da função azul, com variação de tamanho, é bem mais rápido. Isso indica, com clareza, que o tamanho da senha é um fator que não pode ser desconsiderado!

Sabendo disso, será que não seria melhor impormos uma restrição quanto a isso?

A princípio, podemos definir um tamanho mínimo que uma senha de nosso usuário deve ter, para garantir uma segurança mínima. Esse número vai de acordo com o que nós achamos necessário.

Considerando que quanto maior, mais seguro, mas mais difícil para o usuário e, quanto menor, menos seguro, mas mais fácil para o usuário - então um mínimo entre 10 e 15 pode ser o ideal.

Ok, definimos um tamanho mínimo. Devemos também definir um tamanho máximo, para padronizar?

## Não limite um tamanho máximo de senha

Sempre me disseram para delimitar um tamanho máximo que uma senha pode ter, mas eu nunca entendi muito bem. Depois de muito perguntar, consegui algumas respostas.

### "Muito espaço para ocupar no banco de dados"

A primeira explicação que me deram foi que se não limitássemos o tamanho das senhas dos usuários, poderíamos acabar com o banco de dados lotado, por conta de alguma(s) senha(s) excessivamente longa(s).

A questão que essa explicação desconsidera é de que **jamais podemos armazenar uma senha em texto plano!**

**Todas** as senhas em nosso sistema precisam ser criptografadas, para serem [armazenadas como hashes](http://blog.alura.com.br/autenticacao-de-forma-segura-com-criptografia/) no banco de dados. Assim, mesmo em caso de vazamento de nossos dados, as senhas ainda estarão, de certa forma, protegidas.

O ponto é que os resultados de uma [função hash](https://pt.wikipedia.org/wiki/Fun%C3%A7%C3%A3o_hash), por definição, sempre terão o mesmo comprimento. O [bcrypt](https://pt.wikipedia.org/wiki/Bcrypt), por exemplo, sempre gera um hash de 60 caracteres. Desse modo, não precisamos nos preocupar com o tamanho que o usuário decida para a senha - pelo menos não por conta disso.

### "Mas e esse monte de requisições?"

Outra questão que me trouxeram foi a respeito do perigo de sobrecarga do servidor - "_e se alguém tentar enviar uma senha de 2 GB?_".

Realmente, esse tipo de atitude por parte do cliente é preocupante e pode causar problemas para o servidor, em forma de ataques de [negação de serviço](https://pt.wikipedia.org/wiki/Ataque_de_nega%C3%A7%C3%A3o_de_servi%C3%A7o), mas será que esse perigo está só nas senhas?

Repare que assim como alguém pode enviar uma senha de 2 GB para o servidor, também pode enviar qualquer outro valor de entrada, com qualquer outro significado semântico, tendo esse mesmo tamanho.

Ou seja, essa questão não pode ser resolvida com políticas de senha, mas, principalmente, com definições e fortificações no próprio servidor.

### A limitação do bcrypt

O terceiro e último ponto que me trouxeram tinha o foco em uma função hash específica - o bcrypt, que [é um dos algoritmos mais recomendados para esse tipo de uso](https://security.stackexchange.com/a/6415).

A questão é que o bcrypt, por definição, tem um limite de 72 caracteres. Por isso que diversos sites e serviços online tem essa limitação explícita, como é o caso do [GitHub](https://github.com/):

[![Limite de tamanho de senha no GitHub](https://blog.caelum.com.br/wp-content/uploads/2018/07/githublimit.png)](https://blog.caelum.com.br/wp-content/uploads/2018/07/githublimit.png)

Mas e aí? Devemos impedir nossos usuários de usarem senhas mais longas (e, portanto, possivelmente mais seguras) por conta de uma limitação do algoritmo de criptografia? Ou será que devemos abandonar esse algoritmo de ótima reputação? O ideal seria não precisar fazer nenhum dos dois… mas como?

Se conseguirmos transformar a senha longa do usuário em uma string menor que 72 caracteres, isso será resolvido.

Como existem diversas funções hash por aí, poderíamos, inclusive, usar uma delas para isso! O [SHA-256](https://pt.wikipedia.org/wiki/SHA-2), por exemplo, transforma a senha em um hash de 32 caracteres. Assim, então, podemos passar esse hash para o bcrypt. Isso resolveria o problema do limite de tamanho do bcrypt sem quebrar, de forma alguma, a segurança do sistema.

> Apesar de algumas pessoas utilizarem o SHA-256 como função hash principal, ele [não é considerado seguro](https://dusted.codes/sha-256-is-not-a-secure-password-hashing-algorithm) sozinho, por ter um tempo de computação muito rápido.

Bacana! Definimos algumas regras em relação ao tamanho das senhas, mas isso é o suficiente? O que mais podemos organizar claramente nas políticas de senha?

## Não se prenda à tabela ASCII

Já cuidamos das questões relacionadas ao tamanho da senha, mas quais são os outros fatores que também importam? Afinal, uma senha como `订ش导书✌︎🚖池`, de 7 caracteres, pode ser segura, mesmo que curta? A resposta é sim! Por usar caracteres variados do padrão [Unicode](https://pt.wikipedia.org/wiki/Unicode), que contém mais de 137 mil caracteres ao total, ela é uma senha segura, já que existem muito mais combinações!

Infelizmente, se tentarmos agora em diversos sites e serviços online atuais, provavelmente acabaremos nos deparando com várias mensagens do tipo: "**somente caracteres ASCII permitidos**". Mas por quê? Qual o sentido disso?

Tirando o fator da síndrome do chimpanzé, não há muito o que defender nessa regra. Entendendo o [básico de Unicode](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/) e suas codificações, e fazendo o certo na hora de armazenar as senhas - ou seja, [usando o bcrypt devidamente, como é recomendado](https://wiki.mozilla.org/WebAppSec/Secure_Coding_Guidelines#Password_Storage) -, não deve haver problema nenhum em usar caracteres Unicode.

Assim, a dica é para abandonarmos esse modo ASCII de pensar e melhorarmos a segurança de nossas aplicações simplesmente sendo menos restritivos e permitindo caracteres Unicode nas senhas!

Cada vez mais está ficando claro o quanto regras estáticas e restritivas podem ser prejudiciais nas políticas de senha. Mas quais são as alternativas?

## Calcule a força da senha dinamicamente

Já entendemos que tentar garantir a força das senhas de nossos usuários com regras estáticas não funciona, podendo até prejudicar a segurança da aplicação. Então como podemos calcular a força de uma senha?

O ideal parece ser tentar garantir a força das senhas dinamicamente - isto é, com alguma espécie de cálculo para cada senha separada. Mas como?

É difícil apontar para uma senha e dizer com certeza que ela é segura. Por outro lado, muitas vezes conseguimos com clareza distinguir que uma senha é, de fato, fraca!

Sendo assim, poderíamos verificar se a senha enviada pelo usuário é fraca, checando se ela é construída a partir de algum padrão calculável ou até se faz parte de alguma [lista de senhas mais utilizadas](https://en.wikipedia.org/wiki/List_of_the_most_common_passwords).

Mas, hum… Vamos ter que criar esse algoritmo do zero? Felizmente, não! Os desenvolvedores do [Dropbox](https://www.dropbox.com/) vieram com um projeto de estimador de força de senhas, baseado nos próprios [crackers de senha](https://en.wikipedia.org/wiki/Password_cracking) utilizados pelos hackers, bastante seguro, flexível e simples de usar - o [zxcvbn](https://github.com/dropbox/zxcvbn)!

O **zxcvbn** consegue reconhecer padrões como de repetição, de datas e de sequências, além de testar a senha para vários dicionários, como de nomes comuns e palavras populares em inglês.

O resultado que o `zxcvbn` nos passa também é de grande ajuda, chegando até a conter um _feedback_ verbal de como a senha pode ser melhorada, para senhas consideradas bem ruins.

Podemos testar o zxcvbn com uma senha que parece difícil de memorizar e ver se é de fato uma senha segura: **P@55w0rD123**.

Esse algoritmo olha nossa senha e nos retorna um JSON com a análise. No JSON temos alguns pontos bem interessantes como o **tempo que demoraria para essa senha ser quebrada com ataques online e offline:**

```

  "crack_times_display": {
    "online_throttling_100_per_hour": "11 days",
    "online_no_throttling_10_per_second": "43 minutes",
    "offline_slow_hashing_1e4_per_second": "3 seconds",
    "offline_fast_hashing_1e10_per_second": "less than a second"
  }
```

Outras informações bacanas são a nota (`score`) que o `zxcvbn` dá para a senha, que varia de **0** (muito fácil e arriscada) até **4** (difícil e forte), e o feedback verbal com sugestões e avisos:

```
  
"score": 1,
  "feedback": {
    "warning": "This is similar to a commonly used password.",
    "suggestions": [
    "Add another word or two. Uncommon words are better.",
    "Predictable substitutions like '@' instead of 'a' don't help very much."
    ]
  }
}
```

Em relação a essa senha, o zxcvbn avisou que ela é muito comum e ainda deu duas dicas - de adicionar uma ou duas palavras, preferencialmente incomuns, e de que substituições previsíveis como `@` no lugar de `a` não ajudam muito. Assim, o usuário estará ainda mais bem instruído para criar uma boa senha!

Legal! Desse modo, o `zxcvbn` pode nos ajudar imensamente. Mas e se tentarmos outras combinações? Por exemplo, passando meu próprio e-mail (`yan.orestes@alura.com.br`):

```

  "crack_times_display": {
    "online_throttling_100_per_hour": "centuries",
    "online_no_throttling_10_per_second": "centuries",
    "offline_slow_hashing_1e4_per_second": "centuries",
    "offline_fast_hashing_1e10_per_second": "centuries"
  },
  "score": 4,
```

Olha só! Eu usei meu próprio email e o zxcvbn identificou como uma senha boa e que demoraria séculos (literalmente) para quebrar! Bom, ele realmente não tinha como saber meu email… mas e aí?

## Considere os casos locais

O zxcvbn é de incrível utilidade e pode funcionar muito bem, mas confiar totalmente no uso padrão dele pode trazer alguns grandes problemas para a gente, como acabamos de ver.

O motivo disso é claro - não há como o zxcvbn saber, nativamente, informações internas da aplicação ou do usuário.

Além disso, os desenvolvedores dessa ferramenta se concentram, no geral, nos Estados Unidos. Por essa razão, os dicionários com os quais as senhas são testadas têm maior relevância para usuários nativos na língua inglesa, o que muitas vezes não será nosso caso. Então o que podemos fazer?

Uma alternativa para esse problema é fazer nossos próprios testes para senhas locais, além do zxcvbn, considerando a língua mãe da maior parte dos usuários, as informações sobre a aplicação, e até as informações dos usuários (como o email ou o _username_).

Na verdade, podemos até usar uma própria funcionalidade do zxcvbn, que nos permite enviar junto à verificação nosso próprio dicionário de senhas, que auxiliará na estimativa da ferramenta. Para isso, é só usar o parâmetro `user_inputs`. Olha só a diferença agora:

```

  "crack_times_display": {
    "online_throttling_100_per_hour": "1 minute",
    "online_no_throttling_10_per_second": "less than a second",
    "offline_slow_hashing_1e4_per_second": "less than a second",
    "offline_fast_hashing_1e10_per_second": "less than a second"
  },
  "score": 0,
  "feedback": {
    "warning": "",
    "suggestions": [
    "Add another word or two. Uncommon words are better."
    ]
  }
}
```

Agora sim! Já conseguimos um bom progresso, mas ainda podemos fazer mais. Considerando uma senha ideal, sabemos que uma ferramenta que pode facilitar muito são os gerenciadores de senha. Que atitudes podemos tomar, como desenvolvedores, para aumentar o suporte a esse instrumento?

## Dando suporte a gerenciadores de senha

A princípio, pode ser intuitivo pensar que o usuário deve sempre digitar sua senha, afinal ela tem que ser lembrada.

Entretanto, no post sobre [criação de boas senhas](http://blog.alura.com.br/como-criar-uma-boa-senha/), concluímos que, no geral, a melhor senha é justamente aquela que não conseguimos lembrar - uma senha aleatória, em conjunto com um gerenciador de senha seguro.

Apesar disso, vemos muitos sites hoje em dia que chegam até a bloquear a função de colar da [área de transferência](https://pt.wikipedia.org/wiki/%C3%81rea_de_transfer%C3%AAncia), mais conhecida como _clipboard_, dificultando bastante o livre uso desse tipo de senha. E por que isso?

Um argumento que vem à tona, às vezes, é de que o _clipboard_ pode estar comprometido, inseguro.

De fato, pode, mas se assumirmos isso, também precisamos considerar que o teclado pode estar comprometido com um _[keylogger](https://pt.wikipedia.org/wiki/Keylogger)_, e que até o movimento do mouse pode estar sendo capturado por um _malware_ no computador do nosso usuário. E aí?

Fica claro que precisamos de algum limite de confiança com o usuário e seu computador. Bloquear a ação de colar texto, enfim, extrapola esse limite.

Obrigando o usuário a digitar a senha, o estamos forçando ou a lembrá-la (o que exigiria uma senha teoricamente mais fraca que uma aleatória e longa), ou a anotá-la em algum ambiente que é possivelmente inseguro - não vale a pena, nem faz sentido!

No geral, enquanto pudermos dar liberdade aos usuários, daremos!

## Não imponha políticas de expiração de senha

Era muito comum, há algum tempo, que as senhas tivessem datas de expiração - o usuário deveria alterar a senha antes dessa data ou a conta seria bloqueada.

Para muita gente isso era uma medida que aumentava a segurança. Cada vez mais, estamos deixando esse hábito para trás, entendendo sua (no geral) falta de sentido.

[![Aviso de expiração de senha](https://blog.caelum.com.br/wp-content/uploads/2018/07/expirar.png)](https://blog.caelum.com.br/wp-content/uploads/2018/07/expirar.png)

Rapidamente, conseguimos pensar em uma situação onde obrigar o usuário a alterar a senha de tempos em tempos **não é útil** - tentativas de invasão por força bruta online.

Se o atacante não tiver a senha do usuário e estiver tentando se autenticar com um amontoado de senhas diferentes, a probabilidade de que a próxima senha a ser tentada seja a correta continua a mesma independentemente de o usuário alterar ou não. Isso é certo, mas e se o atacante conseguir acesso interno ao sistema?

Em uma situação dessas, o atacante poderia entrar no nosso banco de dados e, assim, capturar os hashes das senhas dos usuários.

Sabendo o algoritmo de hash usado, com um ataque de força bruta offline é bem possível que, eventualmente, o hacker conseguisse descobrir as senhas que estavam armazenadas no banco de dados. E aí, o que aconteceria?

Se a senha continuar a mesma, o hacker vai rapidamente conseguir acessar a conta de nossos usuários. Assim, parece óbvio que podemos cortar o acesso (seja precipitado ou prolongado) do hacker se obrigarmos o usuário a mudar a senha periodicamente, certo? Bom… na realidade, não é bem assim.

O ponto principal que quebra a idealização do conceito de senha periódica é, como muitas outras vezes, o fator humano!

Quando forçamos o usuário a mudar sua senha a cada determinado tempo, podemos imaginar que ele irá bolar uma senha forte e completamente diferente da anterior, mas dificilmente isso é o que vai acontecer.

O usuário é esperto e quer conforto no uso da aplicação, e isso é algo que não podemos nos dar ao luxo de ignorar (lembrando da Regra de Usabilidade do AviD!).

Assim, no geral, o que vemos são mudanças irrelevantes e que enfraquecem a senha do usuário - em abril, `senha04`, em maio, `senha05`, em junho, `senha06`. Frequentemente, [são mudanças facilmente calculáveis que acabam atrasando muito pouco o atacante](http://www.cs.unc.edu/~fabian/papers/PasswordExpire.pdf).

Além do mais, caso o hacker já tenha conseguido acesso à conta de um usuário, mudar a senha não diminui as chances de alguma _[backdoor](https://pt.wikipedia.org/wiki/Backdoor)_ ter sido preparada, de modo que o acesso seja contínuo mesmo com a mudança de senha. Considerando isso tudo, geralmente não há sentido em forçar essa mudança periódica.

De todo o modo, tratar como se um atacante já tivesse acesso completo ao nosso sistema também não é correto. A possibilidade sempre existe e é por isso que devemos combatê-la com segurança, mas assumir que já é certo acaba por desestimular a proteção nessa camada.

Afinal, se um atacante está há quase 90 dias (ou seja qual for o prazo de expiração de senha) com acesso ao nosso sistema, ou à conta de algum usuário, talvez o problema não esteja só na senha escolhida, mas em nossa segurança geral e, principalmente, em como nos comunicamos com nossos usuários.

## Seja sincero com o usuário

Apesar de todos os nossos esforços, imprevistos podem acontecer. Contanto que lidemos bem, de acordo com a situação específica, e nos preparemos para evitar futuros imprevistos do tipo, estamos tratando o problema da melhor forma. Agora, além disso, uma coisa é importantíssima - contar a verdade.

Com todo o [escândalo recente envolvendo o Facebook](https://pt.wikipedia.org/wiki/Esc%C3%A2ndalo_de_dados_Facebook%E2%80%93Cambridge_Analytica), fica claro o quão prejudicial pode ser para uma marca, para uma comunidade e para os próprios usuários, esconder informações do público.

Assim, caso o sistema de nossa aplicação fosse invadido, por exemplo, é essencial que possamos dizer isso para nossos usuários, mesmo (e especialmente) se a questão já foi resolvida.

No geral, é importante manter uma relação de sinceridade com os usuários de nossa aplicação, mesmo não se tratando de ocasiões tão sérias como um tremendo vazamento de dados, ou uma invasão ao sistema.

Um exemplo simples (e, mesmo assim, real) pode ser tirado a partir do ponto **_Não limite um tamanho máximo de senha_**. Nele, usamos de exemplo o GitHub, que limita o tamanho da senha para 72 caracteres **e deixa essa limitação explícita**.

Entretanto, há alguns sites (alguns ainda bem grandes, como o Discord ou o GitLab), que também sofrem dessa limitação, mas escondem do usuário! Como escondem? Eles **truncam** a senha para que ela tenha no máximo 72 caracteres.

Faça o teste você mesmo - em um desses sites, defina uma senha aleatória maior que 72 caracteres.

A princípio, não haverá problema nenhum. Depois tente logar com apenas os 72 primeiros e _voilà_, você estará dentro da sua conta sem ter enviado a senha que definiu.

Apesar de prático, é uma atitude bastante negativa que pode afetar a confiança do usuário quanto à segurança da aplicação.

A dica aqui é clara - se possível, conte a verdade. Se não, talvez seja o caso de evitar essa ação.

## Entenda os limites de segurança da senha

Em 2004, Bill Gates, o fundador da Microsoft, [predisse a extinção das senhas](https://www.cnet.com/news/gates-predicts-death-of-the-password/), alegando que elas não conseguiriam vencer o desafio de manter informações críticas em segurança.

Já fazem 14 anos e a senha continua sendo a ferramenta mais utilizada quando se trata de segurança e autenticação. E aí, qual o sentido disso tudo?

Em primeiro lugar, senhas são confortáveis. Para desenvolvedores e usuários, é uma ferramenta simples de usar e de entender, que consegue cumprir, até certo nível, o que promete - segurança.

Apesar disso, e sem precisar entrar em muitos detalhes, a segurança das senhas é limitada - no final, toda senha é quebrável através da força bruta, por menos prático que esse processo seja.

Além disso, muitas vezes nem precisamos de muita tecnologia para descobrir a senha de alguém, como o xkcd tira sarro na [tirinha 538 sobre segurança](https://xkcd.com/538/):

[![Tirinha 538 do xkcd](https://blog.caelum.com.br/wp-content/uploads/2018/07/xkcd538.png)](https://blog.caelum.com.br/wp-content/uploads/2018/07/xkcd538.png)

Entendendo que senhas podem não ser tão seguras, estamos um passo à frente de qualquer ataque à nossa aplicação. Basta, agora, focar em implementar alternativas.

Se livrar completamente das senhas talvez não seja uma boa escolha, considerando que, mesmo muito antiga, ainda está em processo de amadurecimento.

Entretanto, quando possível, é importante avaliar o uso de ferramentas alternativas, como biometria, [que também tem seus problemas](https://betanews.com/2016/08/24/unsafe-biometrics/), ou até algumas mais simples, como [autenticação em múltiplos fatores](https://en.wikipedia.org/wiki/Multi-factor_authentication), em que se usa uma senha, mas só ela não basta.

Além disso, existem técnicas que grandes serviços, como Facebook e Google, já usam, que ajudam a garantir a autenticidade de um login, ou seja, que tentam verificar se é você mesmo quem está entrando com sua senha. Para isso, monitora-se algumas informações dos logins usuais, como local, IP e navegador, para que se compare com as do login atual.

Quando entramos em nossa conta do Facebook em um computador que nunca usamos, por exemplo, provavelmente vamos receber um alerta, o que já ajuda bastante a garantir a segurança para o usuário.

## Colaborando com o usuário em prol de uma melhor segurança

Acima de tudo, o importante é conquistar o usuário e o ajudar a agir em favor da segurança de sua conta e de nossa aplicação, seja com senhas, ou além, o que pode ser uma tarefa bem difícil.

Invadir demais as decisões e opções do usuário e o forçar a tomar certas atitudes simplesmente não funciona. Dar liberdade total, porém, pode também não ser a melhor escolha. Com essas dicas, espero que obtenha um bom resultado em relação à segurança da aplicação, assim como eu obtive!

O que achou dessas dicas? Deixe aqui seu comentário com a sua opinião e vamos continuar o debate ;)!
