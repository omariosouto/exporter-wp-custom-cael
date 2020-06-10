---
title: "Nova versão do xstream"
date: "2006-07-27"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A nova versão do xstream trará grandes novidades, entre elas a idéia de coleções persistentes.

Isto é, imagine que você possui uma java.util.List na sua mão:

\[java\] public void facaAlgo(List lista) { } \[/java\]

E dentro do seu método você adicione um objeto a essa lista: \[java\] lista.add(new Pessoa("guilherme silveira","caelum")); \[/java\]

Nesse momento, a sua lista escreveu um arquivo xml em disco, representando a sua entidade!

O XStream escreve exatamente o seguinte xml em disco:

\[xml\] <pessoa> <nome>guilherme silveira</nome> <empresa>caelum</empresa> </pessoa> \[/xml\]

Como fazer isso? Simples, basta você definir o seu diretório onde os arquivos serão salvos e utilizar sua lista para a serialização automática:

\[java\] File dir = new File("/tmp"); List xmlList = new XmlArrayList(new FileStreamStrategy(dir)); xmlList.add("guilherme"); xmlList.add("silveira"); \[/java\]

A classe `FileStreamStrategy` é quem define que cada objeto vira um arquivo xml diferente... implementando estratégias diferentes você consegue enviar esses arquivos para algum outro lugar, compactá-los em um único arquivo zip etc.

Essa é mais uma funcionalidade do XStream tentando facilitar a vida de quem trabalha com xml. Neste caso será muito útil para escrever _tickets_ no sistema de arquivos para serem consumidos por algum outro programa.

Acesse [xstream.codehaus.org](http://xstream.codehaus.org) e começe a trabalhar com xml de uma maneira mais simples.
