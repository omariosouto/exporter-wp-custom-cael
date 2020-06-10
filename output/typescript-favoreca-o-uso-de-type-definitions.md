---
title: "TypeScript, favoreça o uso de Type Definitions"
date: "2017-05-24"
author: "flavio.almeida"
authorEmail: "flavio.almeida@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O [TypeScript](https://www.typescriptlang.org/) é um superset do JavaScript que fornece principalmente tipagem estática opcional, classes e interfaces. Um dos grandes benefícios é permitir que IDEs forneçam um ambiente mais rico para detectar erros comuns à medida que escrevemos nosso código.

Contudo, o navegador não entende TypeScript e para que nosso código seja um código válido, ele precisa ser compilado para JavaScript. São os arquivos resultantes da compilação que são importados pelo navegador. Frameworks como Angular e Ionic utilizam TypeScript como linguagem padrão.

Agora que já temos uma visão geral sobre esta poderosa linguagem, vejamos um simples exemplo que prepara o terreno do tópico abordado neste post:

\[code language="javascript"\] let nome: string = 'Flávio'; nome = 12; // \[ts\] Type '12' is not assignable to type 'string'. \[/code\]

TypeScript é ainda capaz de inferir o tipo, sem a necessidade de indicá-lo:

\[code language="javascript"\] let nome = 'Flávio'; // inferiu o tipo string nome = 12; // \[ts\] Type '12' is not assignable to type 'string'. \[/code\]

Além disso, a linguagem traz por padrão **type definitions** para funções e API's do DOM, permitindo que a IDE detecte erros de sintaxe em nosso código ao usarmos esses recursos, inclusive fornecendo autocomplete:

[![](https://blog.caelum.com.br/wp-content/uploads/2020/05/typescript-autocomplete-300x163.png)](https://blog.caelum.com.br/typescript-favoreca-o-uso-de-type-definitions/typescript-autocomplete/)

Mas como proceder para utilizarmos bibliotecas de terceiros que não foram criadas com TypeScript?

## Utilizando bibliotecas que não foram criadas com TypeScript

Podemos pegar como exemplo o jQuery, uma das bibliotecas mais populares e que ainda invade o coletivo imaginário dos desenvolvedores. Contudo, se tentarmos usar o global `$` em nosso código, teremos um erro de compilação do TypeScript:

\[code language="javascript"\] let elemento = $('p'); // \[ts\] Cannot find name '$' \[/code\]

O erro de compilação ocorre porque o TypeScript não entende que `$` é uma variável do escopo global e definida pelo jQuery. Faz todo sentido, porque para o TypeScript variáveis não declaradas podem resultar em erros em nosso programa. E agora?

## Solução 1: declare var, "calando a boca" do compilador

Uma solução para "calar a boca" do compilador e deixar nosso código compilar é a seguinte:

\[code language="javascript"\] declare var $: any; let elemento = $('p'); // deixou o compilador feliz \[/code\]

No entanto, essa solução só deve ser usada em último caso, pois apesar de ter silenciado o compilador, a variável `$` foi definida como o tipo `any`. Como o próprio tipo já indica, o valor da variável pode ser qualquer coisa e o TypeScript não saberá se os métodos e atribuições feitas com ela são válidos. Sendo assim, a solução que vimos tira um dos grandes benefícios da linguagem.

## Solução 2, mais indica: baixando type definitions

A boa notícia é que mesmo o jQuery não sendo uma biblioteca criada com TypeScript, é possível criar seu type definition, mais ainda, ele pode ser disponibilizado pelo autor da biblioteca ou por terceiros.

Neste caso, para que possamos tipar a variável `$` com o tipo `JQuery` e nos beneficiarmos do autocomplete e checagem estática do TypeScript podemos baixar seu type definition através do comando:

\[code language="javascript"\] npm install @types/jquery --save-dev \[/code\]

Como o compilador do TypeScript depende do Node.js para funcionar, temos acesso ao `npm`, seu gerenciador de pacotes. Por padrão, o compilador TypeScript procura essas definições baixadas dentro da pasta `node_modules`, local no qual o type definition que acabamos de baixar estará disponível.

Agora, podemos reescrever nosso código assim, sem o declare var:

\[code language="javascript"\] let elemento = $('select'); // TypeScript infere o tipo JQuery \[/code\]

[![](https://blog.caelum.com.br/wp-content/uploads/2020/05/jquery-type-300x66.png)](https://blog.caelum.com.br/typescript-favoreca-o-uso-de-type-definitions/jquery-type/)

Ou se for a propriedade de uma classe:

\[code language="javascript"\] class Controller {

private elemento: JQuery; // definindo o tipo!

constructor() {

this.elemento = $('select'); /\* se fizermos this.elemento, teremos acesso à todas as funções do jQuery, inclusive validação dos tipos de parâmetros \*/ } } \[/code\]

[![](https://blog.caelum.com.br/wp-content/uploads/2020/05/jquery-auytocomplete-300x168.png)](https://blog.caelum.com.br/typescript-favoreca-o-uso-de-type-definitions/jquery-auytocomplete/)

## Conclusão

Baixar o definition type da sua biblioteca favorita é um caminho mais adequado do que simplesmente silenciar o compilador com `declare var`. No entanto, pode ser que existam mais de uma definição criada por diferentes autores e cabe ao desenvolvedor baixar aquela mais atualizada, o que requererá uma verificação detalhada de sua parte.

Por fim, há o repositório [DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped) que centraliza vários typings, quem sabe aquele da sua biblioteca preferida existe lá, ou, caso não exista em nenhum lugar, que tal contribuir com a comunidade sendo o primeiro a criar sua type definition?

## Twitter: @flaviohalmeida
