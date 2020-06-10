---
title: "Protótipo de aplicação iOS usando Swift"
date: "2015-01-13"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A linguagem Swift e a nova versão do XCode fornecem diversas funcionalidades, algumas novas e outras atualizadas, que facilitam a criação de um protótipo. Veremos aqui como criar uma aplicação simples com poucos arrastas e solta, classes, variáveis membro e chamadas de métodos, propriedades e funções.

Primeiro abrimos o Xcode e criamos um novo projeto do tipo "Single View App".

[![Screenshot 2014-11-30 17.16.19](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.16.19-300x174.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.16.19.png)

Ao escolhermos nosso "Main.storyboard" vemos todos os controllers que compõem a navegação entre telas de nosso projeto: nesse instante temos um único view controller:

[![Screenshot 2014-11-30 17.17.44](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.17.44-300x205.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.17.44.png)

Arrastamos então dois labels, dois textos e um botão. Mudamos o nome dos labels e do botão com um duplo-clique para ficar como a seguir:

[![Screenshot 2014-11-30 17.18.41](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.18.41-300x270.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.18.41.png)

Ao clicarmos no botão Play, vemos a aplicação rodando em nosso iPhone simulado. Perfeito!

[![Screenshot 2014-11-30 17.19.11](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.19.11-168x300.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.19.11.png)

O próximo passo é escolher o assistente de código, que permite visualizar o código atrelado ao componente visual selecionado nesse instante (nosso View Controller), e vemos nossa classe que está conectada a ele:

[![Screenshot 2014-11-30 17.23.06](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.23.06-300x214.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.23.06.png)

Adicionamos no nosso código duas conexões do tipo UITextField para ler os dados dos dois campos que criamos. Anotamos elas com IBOutlet, sem esquecer de marcar nossos campos como opcionais, uma vez que não setaremos o valor deles programaticamente na inicialização de nosso objeto! A conexão entre os dois será feita em tempo de execução, mas para isso precisamos dizer ao nosso Xcode que desejamos tal conexão.

Note que um círculo vazio surge, que puxamos e soltamos em cima de nosso campo, para conectar a variável ao campo.

[![Screenshot 2014-11-30 17.23.57](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.23.57-300x128.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.23.57.png)

Criamos também uma função chamada adicionar, anotamos com IBAction e conectamos com nosso botão. Agora é só ler os dados de nossos campos, como opcionais, e imprimir:

[![Screenshot 2014-11-30 17.25.40](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.25.40-300x93.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.25.40.png)

Claro, podemos validar os campos antes de acessar valores opcionais, que podem ser nulos. Assim evitamos uma crash da aplicação caso o mesmo seja nulo:

`@IBAction func add() { if nome==nil || email==nil { return } println("os dados lidos foram: ") println(nome!.text) println(email!.text) }`

Podemos melhorar o código e usar interpolação de strings:

`@IBAction func add() { if nome==nil || email==nil { return } println("os dados lidos foram: \(nome!.text) \(email!.text)") }`

Pronto, rodamos novamente nossa aplicação e podemos digitar os valores e enviar os dados, vemos o resultado em nosso log:

[![Screenshot 2014-11-30 17.27.39](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.27.39-216x300.png)](https://blog.caelum.com.br/wp-content/uploads/2015/01/Screenshot-2014-11-30-17.27.39.png)

Por mais que seja muito simples criar uma aplicação inicial usando o Xcode com Swift, é importantíssimo que foquemos nosso aprendizado também em boas práticas de linguagem e de programação, entender somente superficialmente o que acontece nos tora meros usuários da linguagem. Entender o que acontece por trás, como e por que a linguagem e a API funcionam de determinada maneira, é o próximo passo, o que define um programador profissional.

Conheça nosso [livro sobre Swift](http://www.casadocodigo.com.br/products/livro-swift-ios) assim como os [cursos online no Alura](http://www.alura.com.br/cursos-online-ios)!
