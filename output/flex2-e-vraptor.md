---
title: "Flex2 e VRaptor"
date: "2007-05-07"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Flex2 (SDK) é opensource, razão suficiente para mostrar um pequeno exemplo como usar Flex2 com VRaptor 2.x (em breve teremos uma versão com VRaptor 3 deste artigo). Vamos criar um página de login e uma lógica que verifique os dados no Servidor.

**Update**: Hoje em dia, no vraptor 3, voce pode fazer a integração facilmente via [json diretamente](http://vraptor.caelum.com.br/documentacao/view-e-ajax/)

**O que é Flex2?**

[Adobe Flex2](http://www.adobe.com/products/flex/) é um framework para criar aplicações ricas que rodam no browser. Flex2 é baseado no Flash que deve ser conhecido pelas interfaces bonitas e altamente interativas. Ele estende o Flash com um modelo de programação mais versátil e completo.

**Flash Player**

Para rodar uma aplicação Flex2 é preciso um plugin ([flash player](http://www.adobe.com/shockwave/download/index.cgi?Lang=BrazilianPortuguese&P1_Prod_Version=ShockwaveFlash)) para o browser. Ele funciona semelhante a JVM e abstraí todos as diferenças e incompatibilidades entre tipos e versões do browser.

**Flex2 SDK**

Para desenvolver uma aplicação Flex2, temos que baixar o [Flex Software Development Kit](http://www.adobe.com/products/flex/downloads/) ou SDK que, como eu mencionei é OpenSource. Com ele vem as ferramentas (como o compilador) e as bibliotecas básicas para criar uma aplicação.

Baixe o [Flex2 SDK](http://www.adobe.com/products/flex/downloads/) e descompacte-o. Na pasta bin você encontra o executável mxmlc, o compilador, que nós usaremos.

**A primeira classe com Flex2/Actionscript**

A linguagem do flex se chama [ActionScript](http://livedocs.adobe.com/flex/201/html/wwhelp/wwhimpl/js/html/wwhelp.htm?href=Part5_ProgAS_155_1.html) sua sintaxe é bem parecida com a do Java. Também tem package, import, construtores e métodos (que se chamam funções).

Vamos criar uma classe que representa nosso modelo na página de login ou seja uma classe Usuario com dois atributos, login e senha. Uma diferença é a palavra-chave "var" para definir variáveis e "function" para construtores/métodos. Além disso, o tipo da variável e o return type do método vem depois da declaração:

\[as3\] package modelo{ public class Usuario {

// class fields private var login:String; private var senha:String;

/\*\* \* Constructor with parameters \*/ public function Usuario(login:String, senha:String) { this.login = login; this.senha = senha; }

public function getLoginParameters():Object { return {"usuario.login": this.login, "usuario.senha" : this.senha}; }

public function toString():String { return "Login: " + this.login + ", Senha: " + this.senha; }

} } \[/as3\]

Parece uma mistura do java e javascript. Salve a classe num arquivo Usuario.as numa pasta chamada modelo.

[Adobe Flex Builder](http://www.adobe.com/products/flex/flexbuilder/) é uma IDE paga (baseada no eclipse) que ajuda na criação das classes e componentes visuais.

  
  
**A interface gráfica**

Os [componentes gráficos](http://livedocs.adobe.com/flex/201/html/wwhelp/wwhimpl/js/html/wwhelp.htm?href=Part2_DevApps_048_1.html) são definidos num arquivo xml de [forma declarativa](http://livedocs.adobe.com/flex/201/html/wwhelp/wwhimpl/common/html/wwhelp.htm?context=LiveDocs_Book_Parts&file=mxmlSyntax_052_1.html#111107). Para criar um [item](http://livedocs.adobe.com/flex/201/html/wwhelp/wwhimpl/js/html/wwhelp.htm?href=Part2_DevApps_048_1.html) num formulário para inserir o login, podemos usar:

\[xml\] <mx:FormItem label="Login"> <mx:TextInput id="loginInput"/> </mx:FormItem> \[/xml\]

Login, Senha e um botão seria:

\[xml\] <mx:HBox> <mx:FormItem label="Login"> <mx:TextInput id="loginInput"/> </mx:FormItem> <mx:FormItem label="Senha"> <mx:TextInput displayAsPassword="true" id="senhaInput"/> </mx:FormItem> </mx:HBox> <mx:Button label="login" click="sendLogin(loginInput.text,senhaInput.text);"/> \[/xml\]

**Scripts**

Observe que nós usamos um [evento](http://livedocs.adobe.com/flex/201/html/wwhelp/wwhimpl/common/html/wwhelp.htm?context=LiveDocs_Book_Parts&file=events_054_01.html#168690) no botão, semelhante ao Javascript. Aqui chamamos um função sendLogin( .. , .. ) com os parâmetros do login e senha. Dentro do xml podemos definir uma área para nossos funções. Basta abrir :

\[as3\] <mx:Script>

<!\[CDATA\[

public function sendLogin(login:String, senha:String):void { //cria modelo var usuario:Usuario = new Usuario(login,senha); //chamada http aqui }

\]\]>

</mx:Script> \[/as3\]

Definimos a interface gráfica e um método/função para ser chamado quando o botão "login" for apertado. Falta programar a chamada http, mas vamos primeiro criar a lógica no servidor com VRaptor.

  
  
**Lógica com VRaptor**

Usei o [blank-project](http://downloads.sourceforge.net/vraptor2/blank-project-src-2.3.2.2.zip?modtime=1175725060&big_mirror=0) do VRaptor para instalar rapidamente um projeto web [pré-configurado](http://www.vraptor.org/pt/installing.html) no [Eclipse.](http://www.eclipse.org)

A classe `UsuarioLogic` é bem parecida com a do primeiro [exemplo](http://www.vraptor.org/pt/first-example.html) na página principal do vraptor. Ela simula um login de uma usuario num sistema. Se for um login válido, ela devolve "ok" caso contrário "invalid".

\[java\] @Component public class LoginLogic {

private String autenticado = "invalid";

@Remotable public void check(Usuario usuario) {

if(usuario == null){ return; }

//simula login if("johann".equals(usuario.getLogin()) &amp;&amp; "12345".equals(usuario.getSenha())) { System.out.printf("%s é valido!", usuario.getLogin()); this.autenticado = "ok"; } } //ejetando "autenticado" no formato JSON public String getAutenticado() { return this.autenticado; } } \[/java\]

e o modelo:

\[java\] public class Usuario {

private String login, senha;

//getters e setters } \[/java\]

**JSON**

[@Remotable](http://www.vraptor.org/pt/ajax.html) muda o comportamento padrão do VRaptor e devolve todos os [dados ejetados](http://www.vraptor.org/pt/presentation/index.html) (pelo getters) no formato [JSON](http://www.json.org/).

Por exemplo, acessando o servidor pela lógica no contexto "flex":

http://localhost:8080/flex/login.check.ajax.logic?usuario.login=johann&usuario.senha=12345

devolveria a resposta no formato JSON:

{"autenticado":"ok"}

**Chamando a lógica com flex**

Vamos voltar ao flex, e programar a chamada http. Para isso o flex oferece um objeto [HTTPService](http://livedocs.adobe.com/flex/201/html/wwhelp/wwhimpl/js/html/wwhelp.htm?href=Part2_DevApps_048_1.html) que pode ser declarado no xml do flex:

\[xml\] <mx:HTTPService id = "service" method = "POST" rootURL = "http://localhost:8080/testes/" result = "onSuccess(event)" fault = "onError(event)" /> \[/xml\]

O importante é o atributo id, com ele podemos acessar o objeto HTTPService dentro do script. O método de callback "onSuccess" será automaticamente chamado depois do request, "onError" quando um erro acontecer. O princípio dos callbacks é também usado no javascript/ajax. Então voltando à função sendLogin, podemos fazer a chamada completa.

  
  
**Corelib**

No método onSuccess(event) converteremos a resposta Http para um objeto JSON. Por padrão o flex (ainda) não sabe lidar com JSON, por isso temos que disponibilzar uma [biblioteca](http://labs.adobe.com/wiki/index.php/ActionScript_3:resources:apis:libraries) que dará essa possibilidade . A [corelib](https://github.com/mikechambers/as3corelib) do flex2 vem com suporte para JSON, e tem que ser baixado separadamente. Depois de descompactar, adicione o arquivo corelib.swc da pasta bin na pasta lib do Flex SDK.

  
  
**Script completo**

Finalmente o script intereiro que usa a classe Usuario, o HTTPService com os métodos de callback e a classe JSON da corelib:

\[as3\] <mx:Script>

<!\[CDATA\[

import mx.controls.Alert; import mx.rpc.events.ResultEvent; import mx.rpc.events.FaultEvent; import mx.utils.ObjectUtil; import mx.rpc.http.HTTPService import com.adobe.serialization.json.JSON; import modelo.Usuario;

public function sendLogin(login:String, senha:String):void { //cria modelo var usuario:Usuario = new Usuario(login,senha);

//seta o url service.url = "login.check.ajax.logic";

//faz a chamada passando os parâmetros service.send(usuario.getLoginParameters()); }

public function onSuccess(event:ResultEvent):void {

//cria String baseado no resultado var rawData:String = String(event.result); //cria um objeto json var respostaNoFormatoJSON:Object = JSON.decode(rawData);

//login ok? if(respostaNoFormatoJSON\["autenticado"\] == "ok") { Alert.show("Bem vindo!"); } else { Alert.show("Usuário não existe"); } }

public function onError(event:FaultEvent):void { Alert.show(ObjectUtil.toString(event.fault)); }

\]\]> </mx:Script> \[/as3\]

  
  
**Compilação dos arquivos flex**

O xml todo (com script e interface gráfica) deve estar num arquivo login.mxml. Temos a seguinte estrutura:

login.mxml
modelo/Usuario.as

Execute na linha de comando usando o compilador do flex (que está na pasta `bin` do sdk):

...pasta-para-flex-sdk/bin/mxmlc login.mxml

Isso vai criar a aplicação ou seja um arquivo `login.swf`.

  
  
**Rodando no servidor**

O `login.swf` deve estar no servidor, você pode abrir o `login.swf` localmente mas a chamada Http acaba num security exception (é bom para testar o método `onError(..)`).

No nosso exemplo:

http://localhost:8080/flex/login.swf

Toda configuração do servidor é padrão tomcat/
