---
title: "Internacionalização no código Java"
date: "2007-10-02"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Já falamos neste blog sobre i18n, o Fabio postou um [artigo](https://blog.caelum.com.br/i18n-internacionalizacao-com-jstl/) como internacionalizar as suas aplicações web usando [JSTL](http://java.sun.com/products/jsp/jstl/). **fmt** dá todo suporte, mas fora do ambiente web precisamos procurar outra solução. Por exemplo, numa aplicação swing, ou mesmo web tem casos que precisamos mostrar uma mensagem já internacionalizada. Temos que saber com i18n / L10n também programaticamente fora do contêiner web. Isto é útil no dia-a-dia e também faz parte da certificação [SCJP](http://www.sun.com/training/catalog/courses/CX-310-055.xml) ;)

### [java.util.Locale](http://java.sun.com/j2se/1.5.0/docs/api/java/util/Locale.html)

Esta classe influencia fortemente o trabalho de outras classes referente à formatação e internacionalização. Com ela será definida uma região, por exemplo:

\[java\] Locale ptBr = new Locale("pt", "BR"); //Locale para o Brasil \[/java\]

Definimos um Locale com o idioma português (_iso-code pt_) e o país Brasil (_iso-code BR_). Vocês poderiam reclamar que está óbvio que o Brasil fala português, mas isso não aplica para todos os países, Veja o exemplo:

\[java\] Locale enCA = new Locale("en","CA"); Locale frCA = new Locale("fr","CA"); \[/java\] Definimos o mesmo pais _Canada_ mas com dois idiomas diferentes (uma região para francês outra para inglês).

Também podemos pegar o _Locale_ da maquina virtual:

`Locale vmLocale = Locale.getDefault(); //pergunta de certificação`

### [java.text.DateFormat](http://java.sun.com/j2se/1.5.0/docs/api/java/text/DateFormat.html) e [java.text.NumberFormat](http://java.sun.com/j2se/1.5.0/docs/api/java/text/NumberFormat.html)

A formatação das duas classes é baseada em um _Locale_. Elas servem para formatar Data ou Números/Moeda respectivamente. Para receber um instância das classes é preciso usar um dos métodos _getInstance()_ delas:

\[java\] DateFormat dateFormat = DateFormat.getInstance(); NumberFormat numberFormat = NumberFormat.getInstance(); \[/java\] Mas aonde está o _Locale_? Nesse caso _DateFormat/NumberFormat_ são baseado no _Locale_ padrão, ou seja o da máquina virtual. Você pode passar o _Locale_ no método, por exemplo:

\[java\] DateFormat dateFormat = DateFormat.getDateInstance(DateFormat.FULL, ptBR); NumberFormat numberFormat = NumberFormat.getNumberInstance(ptBR); \[/java\]

Agora você recebe uma instância para formatar datas ou numeros referente do Brasil. Existem outros métodos para receber uma instância, aqui algumas variações:

\[java\] Locale ptBR = new Locale("pt", "BR"); DateFormat dateFormat = DateFormat.getDateInstance(DateFormat.FULL, ptBR); System.out.println(dateFormat.format(new Date()));

DateFormat timeFormat = DateFormat.getTimeInstance(DateFormat.MEDIUM, ptBR); System.out.println(timeFormat.format(new Date())); NumberFormat numberFormat = NumberFormat.getNumberInstance(ptBR); //para números System.out.println(numberFormat.format(13.23)); NumberFormat moedaFormat = NumberFormat.getCurrencyInstance(ptBR); //para moedas System.out.println(moedaFormat.format(13.23)); \[/java\]

Dependo do dia, o resultado seria: `Sexta-feira, 21 de Setembro de 2007 00:51:05 13,23 R$ 13,23`

As duas classes, junto com o _Locale_ dão muito poder e não servem somente para formatação, mas também para fazer parsing de uma _String_ para um _Date/Number_, veja só:

\[java\] System.out.println(dateFormat.parse( "Sexta-feira, 21 de Setembro de 2007")); System.out.println(timeFormat.parse("00:58:16")); System.out.println(numberFormat.parse("13,23")); System.out.println(moedaFormat.parse("R$ 13,23")); \[/java\] Imprime (Dependo da data): `Fri Sep 21 00:00:00 BRT 2007 Thu Jan 01 00:58:16 BRT 1970 13.23 13.23`

### [ResourceBundle](http://java.sun.com/j2se/1.5.0/docs/api/java/util/ResourceBundle.html)

A classe _ResourceBundle_ é aquela que acessa seu arquivo de internacionalização. Supondo que temos dois arquivos de propriedades no classpath, um para as mensagens em português, outro para as em alemão e um padrão, com o conteúdo seguinte:

_messages\_pt\_BR.properties_ `welcome=Bem vindo no Brasil`

_messages\_de\_DE.properties_ `welcome=Hallo in Deutschland`

_messages.properties_ `welcome=Welcome in default logout=Leaving default`

A nomenclatura segue o padrão, messages\_'iso-code-language'\_'iso-code-country'. Parece familiar? Para carregar o arquivo "messages" baseada na _Locale_ pt-BR basta usar:

\[java\] Locale ptBR = new Locale("pt","BR"); ResourceBundle bundle = ResourceBundle.getBundle("messages", ptBR); System.out.println(bundle.getString("welcome")); \[/java\] O resultado será: `Bem vindo no Brasil`

Para carregar a mensagem em alemão, só precisamos mudar o _Locale_. Mas se eu chamo uma mensagem com a chave "logout"? Por exemplo:

\[java\] System.out.println(bundle.getString("logout")); \[/java\] O bundle vai procurar de maneira seguinte:

_messages\_pt\_BR.properties //não acha messages\_pt.properties //não acha messages.properties_

e finalmente acha o valor da chave 'logout' no arquivo _messages.properties_ e imprime na tela: `Leaving default`

### [MessageFormat](http://java.sun.com/j2se/1.5.0/docs/api/java/text/MessageFormat.html)

Falta mencionar a classe que ajuda substituir parâmetros na mensagem.

\[java\] String mensagemParametrizada = "Isto foi um post sobre {0} e {1}."; String mensagem = MessageFormat.format(mensagemParametrizada, "i18n", "formatação"); System.out.println(mensagem); \[/java\]

Imprime: `Isto foi um post sobre i18n e formatação.`
