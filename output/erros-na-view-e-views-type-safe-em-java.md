---
title: "Erros na view e views type safe em java"
date: "2014-10-21"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Diversas vezes escolhemos uma linguagem como Java para nosso projeto para que tenhamos benefícios de uma linguagem type safe mainstream. Ganhamos o poder de refatoração sem medo através do uso da IDE, a capacidade de detectar toda um categoria de erros em tempo de compilação ao invés de descobrir em tempo de execucão que algo estava muito errado etc.

Mas ao escolhermos Java para o desenvolvimento de uma aplicação web tradicional junto com um framework como o VRaptor, qual a tecnologia de view que escolhemos? JSP? Velocity? Freemarker? Twirl? Erb?

Se escolhermos uma view com linguagem dinâmica e não compilada, como JSP, Freemarker ou Erb, não sabemos em tempo de desenvolvimento se o método que estamos invocando na view existe de verdade em nossos modelos. Não cobrimos 100% com testes de aceitação (100% de testes de unidade aqui não valem), então só pegamos alguns erros de invocações inválidas em tempo de execução, um horror. Só descobrimos que digitamos um "items" ao invés de "itens" dentro de uma cláusula de if em produção. Claro, o desenvolvedor deveria testar todos os caminhos, mas todos os caminhos são (literalmente) infinitos.

> Linguagens de view não compiladas facilitam o indesejado: pegar erros somente em produção.

Mas o JSP é compilado. Somente em produção. E mesmo que instalássemos um plugin na IDE para compilar o JSP, ao utilizar expression language a compilação não pega erros como os citados acima.

Essas views também não permitem definir quais são as variáveis que elas precisam receber para serem renderizadas. O resultado é um controller que esquece de enviar uma variável ou faz uma query desnecessária e envia dados além do desejado pela view: por um lado o comportamento é indesejado e no outro desnecessário, não ótimo.

Desejamos então o que? Uma view capaz de definir o que ela precisa, e que nos diga em tempo de desenvolvimento o que estamos fazendo de errado: desde variáveis descenessárias, variáveis erradas ou invocações inválidas.

O Twirl oferta isso, mas em outra linguagem, Scala. E com ela, vem todas as dependências do Scala, mais uma linguagem para o desenvolvedor de backend e o de frontend aprenderem, erros de compilação obscuros (ligados a outra linguagem) e a impossibilidade de um debug de nossa view para seres humanos (a implementação atual do Twirl é feita em cima de sequências de yields).

Queremos então algo onde eu escreva primariamente HTML e secundariamente (meus laços e condicionais) em Java, que não obrigue o desenvolvedor a aprender mais uma linguagem, que seja debugável, apresente mensagens de erro adequadas ao meu ambiente e permita que o desenvolvedor pegue diversos tipos de erro durante o desenvolvimento, como no arquivo lista.tone a seguir:

\[code\] @(List<User> users)

<html> <% for(User user : users) { %>

<h1>Hi @user</h1>

<% } %> </html> \[/code\]

A view acima foi escrita em [Panettone](https://github.com/caelum/vraptor-panettone), uma template engine que utiliza código Java e permite declarar as variáveis que deseja utilizar. O código acima será transformado durante desenvolvimento em uma classe chamada template.lista, com o método render:

\[code\] public void render(List<User> users) { // line 3 write("<html>\\n"); // line 4 for(User user : users) { // line 6 write("<h1>Hi @user</h1>\\n"); // line 7 } // line 8 write("</html>\\n"); } \[/code\]

Isto é, em tempo de desenvolvimento temos todas as vantagens que estávamos procurando. Para isso basta deixar rodando um jar que compila nossas views o tempo todo, seja através de um java -jar, do ant ou do maven. Ou podemos ainda instalar um plugin do Eclipse (solução mais simples de todas) que ao aplicar a natureza Panettone no projeto, já passa a compilar todas as suas views.

O processo de migração de uma view JSP para Panettone não é doloroso, uma vez que a expression language existente no Panettone é compile safe (diferentemente do JSP) e suporta diversas características dele. Para nossos projetos onde não usamos taglibs de terceiros, o Panettone parece ser um bom caminho para minimizar erros em nossos projetos.

Para começar a utilizar o Panettone (principalmente com VRaptor), você pode seguir [as informações do README](https://github.com/caelum/vraptor-panettone) ou conversar na lista do VRaptor e no GUJ.
