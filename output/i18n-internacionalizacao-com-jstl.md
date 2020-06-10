---
title: "i18n: Internacionalização com JSTL"
date: "2007-08-13"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A origem do termo **i18n** é bastante curiosa. [Pessoas preguiçosas (como eu)](http://www.techtips.com.br/programacao/bom-programador-preguioso/) odeiam ter que ficar escrevendo palavras grandes como _**i**nternationalizatio**n**_. Adivinha quantas letras existem entre o **i** e o **n**?

A mesma idéia foi usada para _localization_: **l10n**.

Internacionalizar aplicações web é cada vez mais uma tarefa corriqueira de todo desenvolvedor web. Comumente as pessoas me perguntam como fazer para ter aquelas bandeiras no topo do site, que mudam a lingua do sistema inteiro ([exemplo na aplicação demo do VRaptor](http://www.vraptor.org/mydvds)).

A maioria dos frameworks web tem a sua maneira particular de prover esse mecanismo, mas o que muita gente desconhece é que existe uma forma padrão de fazer isso, definida na especificação do Java EE, através da **J**SP **S**tandard **T**ag**L**ibs.

### 1\. Escolha uma implementação

JSTL é apenas uma especificação, portanto é necessário o uso de uma de suas implementações. A mais usada é a [Jakarta Standard](http://jakarta.apache.org/taglibs/doc/standard-doc/intro.html), que você pode baixar [diretamente do site da Apache](http://jakarta.apache.org/site/downloads/downloads_taglibs-standard.cgi).

Certifique-se de que esteja baixando alguma implementação da versão 1.1 da JSTL (ou superior). Não garanto que irá funcionar com versões mais antigas. Essa versão 1.1 da JSTL requer o uso de, no mínimo, a versão 2.4 da especificação de Servlets. Portanto certifique-se de configurar corretamente a versão no seu _web.xml_. Perceba o `version="2.4"` no exemplo abaixo:

\[xml\] <?xml version="1.0" encoding="UTF-8"?>

<web-app id="MinhaWebApp" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee

http://java.sun.com/xml/ns/j2ee/web-app\_2\_4.xsd">

<display-name>Minha aplicação</display-name>

<!-- Resto das coisas ... -->

</web-app> \[/xml\]

Basta descompactar o zip que foi baixado e colocar o jstl.jar (jar da especificação) e o standard.jar (implementação) no diretório WEB-INF/lib da sua aplicação web.

### 2\. Configure um arquivo de mensagens

Para dizer à JSTL qual será o arquivo que contém as mensagens do seu sistema, basta adicionar a seguinte configuração ao seu web.xml:

\[code lang="xml" highlight="10,11,12,13,14,15"\] <web-app id="MinhaWebApp" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee

http://java.sun.com/xml/ns/j2ee/web-app\_2\_4.xsd">

<display-name>Minha aplicação</display-name> <context-param> <param-name> javax.servlet.jsp.jstl.fmt.localizationContext </param-name> <param-value>messages</param-value> </context-param> <!– Resto das coisas … –> </web-app> \[/code\]

Este arquivo de mensagens deve estar no _classpath_ da sua aplicação web e deve possuir a extensão `.properties`. Existem várias formas de se fazer isso. A mais simples, é criar o arquivo _messages.properties_ no diretório onde estão os fontes (\*.java) da sua aplicação (comumente esse diretório se chama _src_).

Certifique-se também de não colocar este arquivo dentro de nenhum pacote. Se fosse mesmo necessário deixá-lo dentro de algum pacote, a configuração no web.xml mudaria um pouco:

`<param-value>br.com.empresa.pacote.messages</param-value>`.

### 3\. Deixe todas as mensagens (e labels) do seu sistema no arquivo de mensagens

Comece colocando as mensagens no arquivo _messages.properties_:

```
site.titulo = Sistema com i18n
saudacao = Bem vindo ao sistema

campo.nome = Nome:
campo.email = Email:
campo.rua = Rua:
campo.cidade = Cidade: 

botao.enviar = Enviar
botao.cancelar = Cancelar

erro.campo.obrigatorio = Por favor, preencha o campo
# etc ...
```

Agora, para usar essas mensagens nas suas páginas JSP, basta usar a taglib **fmt** da _JSTL_:

\[code lang="xml" highlight="5,8,9,12,15,18,21,24"\] <%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>

<html> <head> <title><fmt:message key="site.titulo"/></title> </head> <body> <h1><fmt:message key="site.titulo"/></h1> <p><fmt:message key="saudacao"/></p>

<form> <fmt:message key="campo.nome"/> <input type="text" name="nome" /><br/>

<fmt:message key="campo.email"/> <input type="text" name="email" /><br/>

<fmt:message key="campo.rua"/> <input type="text" name="rua" /><br/>

<fmt:message key="campo.cidade"/> <input type="text" name="cidade" /> <p><input type="submit" value='<fmt:message key="botao.enviar"/>'></p> </form> </body> </html> \[/code\]

### 4\. Traduza o sistema para mais línguas

Para fazer com que o sistema possa funcionar com diferentes línguas, basta criar novos arquivos de mensagens, um para cada lingua diferente. Por exemplo, para traduzir o sistema para o alemão:

```
# arquivo messages_de.properties
site.titulo = System mit i18n
saudacao = Herzlichen Willkommen

campo.nome = Name:
campo.email = Email:
campo.rua = Strasse:
campo.cidade = Stadt: 

botao.enviar = Senden
botao.cancelar = Abbrechen

erro.campo.obrigatorio = Das ist ein benötige Feld
# etc ...
```

E para ter uma versão em inglês do sistema:  

```
# arquivo messages_en.properties
site.titulo = i18n system
saudacao = Welcome

campo.nome = Name:
campo.email = Email:
campo.rua = Street:
campo.cidade = City: 

botao.enviar = Send
botao.cancelar = Cancel

erro.campo.obrigatorio = Required field
# etc ...
```

Estes arquivos devem estar no mesmo diretório do arquivo de mensagens padrão: _messages.properties_.

A tag `fmt:message` sempre procura o arquivo de mensagens mais adequado para o **Locale** associado ao usuário. No início, o **Locale** do usuário é aquele que está configurado no browser. Os navegadores enviam no cabeçalho das requisições quais são as línguas preferidas pelo usuário. Experimente mudar essas configurações no seu navegador e ver que o sistema passa a ser exibido em línguas diferentes.

Se o Locale associado usuário for en\_US (inglês dos EUA), a tag fmt:message irá tentar buscar as mensagens nos seguintes arquivos, **em ordem**:

- _messages\_en\_US.properties_
- _messages\_en.properties_
- _messages.properties_

O primeiro a ser encontrado será usado. Portanto, a boa prática é ter o arquivo _messages.properties_ com a língua padrão do sistema e um arquivo específico para cada língua adicional.

### 5\. Mude a língua do sistema a força

Para fazer com que o sistema passe a ser exibido em uma língua diferente da que o browser pediu, basta trocar o **Locale** associado ao usuário.

Para tal existe uma tag da JSTL, a `fmt:setLocale`, que pode ser usada junto com um `c:if` em uma página mudaLingua.jsp, por exemplo:

\[code lang="xml" highlight="2"\] <c:if test="${not empty param.lingua}"> <fmt:setLocale value="${param.lingua}" scope="session"/> </c:if> \[/code\]

Basta agora chamar essa página passando o locale adequado. Experimente chamar _mudaLingua.jsp?lingua=de_ e _mudaLingua.jsp?lingua=en_ e ver o sistema ser exibido em outra lingua.

Parece um pouco estranho ter um jsp responsável pela tarefa de mudar a língua do sistema, já que os jsps deveriam ser responsáveis apenas pela apresentação (geração de html, no nosso caso). A funcionalidade parece se encaixar melhor dentro de uma classe responsável por mudar a lingua do sistema.

A classe `javax.servlet.jsp.jstl.core.Config` serve justamente para controlar as configurações da JSTL programaticamente. Com isso, você pode ter uma servlet, uma action do struts, uma lógica do vraptor, ou algo similar para mudar a lingua do seu sistema.

Exemplo com uma servlet:

\[java\] public class MudaLinguaServlet extends HttpServlet { @Override protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException { String language = request.getParameter("lingua"); Locale locale = new Locale(language);

Config.set(request.getSession(), Config.FMT\_LOCALE, locale); Config.set(request.getSession(), Config.FMT\_FALLBACK\_LOCALE, locale);

response.sendRedirect("index.jsp"); } } \[/java\]

Basta agora chamar a servlet passando o parâmetro `lingua`. Experimente chamar _mudaLingua?lingua=en_ e _mudaLingua?lingua=de_. Ou você pode ainda deixar bandeiras para diferentes países com links para essa Servlet que muda a língua.
