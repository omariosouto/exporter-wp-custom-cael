---
title: "Lendo dados em C"
date: "2016-03-16"
author: "joviane"
authorEmail: "joviane.jardim@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando aprendemos uma linguagem de programação, uma das primeiras coisas que queremos fazer é conseguir entrar com dados e utilizá-los em nosso programa. Utilizando a linguagem C não poderia ser diferente.

Uma das formas de lermos caracteres a partir do teclado em C é utilizando a funcão **getchar**. Imagine um programa em C que obtém a entrada e imprime o caracter digitado:

\[code language="c"\] #include<stdio.h> int main() { int opcao = getchar(); printf("Opção escolhida: %d\\n", opcao);

return 0; } \[/code\]

Compilamos o código (no Terminal se Linux/MacOS ou CMD se Windows) usando o comando:

\[code\] gcc leitura.c -o leitura.out \[/code\]

E executamos com:

\[code\] ./leitura.out \[/code\]

Porém, ao executarmos este código e digitarmos a opção "2" por exemplo, obtemos a seguinte saída:

\[code\] Opção escolhida: 50 \[/code\]

Para conseguirmos imprimir exatamente o caracter digitado, precisamos usar uma outra função do C chamada **putchar**. Isto porque a função **getchar** devolve um inteiro correspondente ao código ascii do caracter e não seu próprio caracter digitado.

\[code language="c"\] #include<stdio.h> int main() { int opcao = getchar(); printf("Opção escolhida: "); putchar(opcao); printf("\\n");

return 0; } \[/code\]

Agora obtemos a saída desejada:

\[code\] Opção escolhida: 2 \[/code\]

Outra forma ainda seria mudarmos o **printf** para imprimirmos o valor como **char** ao invés de imprimirmos como **int** utilizando o _placeholder_ **%c**:

\[code language="c"\] #include<stdio.h> int main() { int opcao = getchar(); printf("Opção escolhida: %c\\n", opcao);

return 0; } \[/code\]

Para imprimir tudo ok, mas e se precisarmos comparar o resultado do caracter digitado? Qual valor teremos que usar como base? O **50**, que representa a conversão do caracter para inteiro, ou **2** que representa o valor em si? E se quiséssemos um número com dois caracteres? O **getchar** pega apenas um caracter por vez.

### Lendo dados de maneira mais fácil com scanf

Existe em C uma função chamada **scanf**, análoga ao **printf**, que facilita a conversão dos caracteres no formato especificado para leitura. Passamos para ela o formato que queremos e a variável que guardará o valor digitado:

\[code language="c"\] #include<stdio.h> int main() { int opcao; scanf("%d", &opcao);

printf("Opção escolhida: %d\\n", opcao);

return 0; } \[/code\]

Ao compilarmos e executarmos novamente o código, conseguimos a saída que queríamos:

\[code\] Opção escolhida: 2 \[/code\]

Com a variável **opcao** guardando o valor digitado, podemos agora efetuar comparações de forma mais fácil.

\[code language="c"\] #include<stdio.h> int main() { printf("=========== Menu ===========\\n\\n"); int opcao; do { printf("Escolha uma opção "); scanf("%d", &opcao); switch(opcao) { case 1: printf("Alura\\n"); break; case 2: printf("Caelum\\n"); break; case 3: printf("Casa do Código\\n"); break; default: printf("Opção inválida.\\n"); break; } } while (opcao < 1 || opcao > 3); return 0; } \[/code\]

Se executarmos o código passando "2" como argumento, teremos a saída com a mensagem "Caelum". Se digitarmos um número inválido, aparecerá a mensagem de "Opcão inválida" até que seja digitado um valor esperado. A função **scanf** facilitou tanto a leitura quanto a comparação com o valor do **switch** evitando que tivéssemos que efetuar alguma conversão na mão. Mas nem tudo são flores se tratando do **scanf**, se digitarmos algum caracter não numérico, teremos um loop infinito!

Isto acontece pois a função **scanf** possui alguns problemas. O loop infinito é causado pelo fato da função **scanf** não limpar o _buffer_ de leitura da memória. Como dissemos que queremos ler apenas números, os caracteres serão ignorados e portanto o último valor digitado continuará na memória, resultando no loop infinito.

### Problemas com scanf? Faça uma leitura mais segura com fgets e a stdlib

O **scanf** facilita a conversão e a leitura mas pode nos trazer muitos problemas. Para conseguirmos ler os dados de uma forma mais segura, precisamos primeiramente efetuar a leitura da informação como uma string e depois convertê-la para número. Vamos utilizar o método **fgets** da **stdio** para fazermos o primeiro passo. Este método lê todos os caracteres digitados até que seja encontrada uma quebra de linha.

Para o **fgets** devemos passar três argumentos: um array de caracteres que representa a linha digitada, a quantidade de caracteres que desejamos ler (normalmente quantos sabemos que a linha possui) e por último de onde queremos buscar a informação. Além disso o método **fgets** retorna **NULL** se der algum erro na leitura, então é interessante só efetuarmos a conversão se der tudo certo. Teremos algo como:

\[code language="c"\] if(fgets(linha, sizeof(linha), stdin) != NULL) { // aqui faremos a conversão } else { break; } \[/code\]

Só falta declararmos este array chamado **linha**, vamos iniciá-lo com o valor da macro **BUFSIZ** que representa um valor otimizado para o buffer de I/O de acordo com o SO. Podemos declarar este array da seguinte forma:

\[code language="c"\] char linha\[BUFSIZ\]; \[/code\]

Agora só falta efetuarmos a conversão do caracter para um número. Felizmente, não precisamos nos preocupar em converter na mão. A própria biblioteca padrão do C, chamada de **stdlib** possui um método que efetua esta conversão. Este método é o **strtol**. Vamos então utilizá-lo passando como parâmetro a linha e qual base devemos efetuar a conversão (decimal - 10, hexadecimal - 16, etc):

\[code language="c"\] opcao = strtol(linha, NULL, 10); \[/code\]

O segundo parâmetro da função **strtol** representa a partir de qual caracter ocorreu um erro de conversão do código. Poderíamos pegar a referência para este array se fosse necessário mostrar o que faltou converter.

Nosso código fica então como:

\[code language="c"\] #include<stdio.h> #include<stdlib.h> int main() { printf("=========== Menu ===========\\n\\n"); printf("Escolha uma opção "); int opcao; char linha\[BUFSIZ\];

do { if(fgets(linha, sizeof(linha), stdin) != NULL) { opcao = strtol(linha, NULL, 10); } else { break; }

switch(opcao) { case 1: printf("Alura\\n"); break; case 2: printf("Caelum\\n"); break; case 3: printf("Casa do Código\\n"); break; default: printf("Opção inválida.\\n"); break; } } while (opcao < 1 || opcao > 3); return 0; } \[/code\]

Ao executarmos podemos agora tanto passar caracteres inválidos para termos a saída "Opção inválida" quanto as opções 1 a 3 que imprimirá a opção correspondente. Conseguimos ler os caracteres de uma maneira mais segura e eficiente. E você? Já teve problemas com **scanf**? Deixe seu comentário!

Se quiser começar e saber mais a respeito da linguagem C é só dar uma olhada nos cursos da [Alura](https://www.alura.com.br/cursos-online-programacao).
