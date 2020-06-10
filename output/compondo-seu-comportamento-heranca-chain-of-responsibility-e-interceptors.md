---
title: "Compondo seu comportamento: herança, Chain of Responsibility e Interceptors"
date: "2010-06-28"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

São diversos os momentos em que temos a tentação de usar herança para implementar funcionalidades de maneira rápida. Um exemplo simples é o polêmico caso de `Properties` e `Hashtable` em Java.

Alguns padrões também costumam ser implementados através de herança são cadeias de responsabilidade, [decorators, template method](https://blog.caelum.com.br/design-patterns-no-java-se-o-template-method/), [filtros/interceptadores](http://java.sun.com/blueprints/corej2eepatterns/Patterns/InterceptingFilter.html), entre outros. O exemplo a seguir mostra a adição de comportamento através de herança próxima ao que acontece com uma `Servlet`, ou ainda um `InputStream` que delega para outro:

\[java\] class Server { Response service(Request req) { return new Response("<html>get para " + req.getPath() + "</html>"); } }

class CacheableServer extends Server { Response service(Request req) { Response resp = super(req); resp.addHeader("Cache-control", "public, max-age=7200"); return resp; } }

class LoggingServer extends CacheableServer { Response service(Request req) { Logger.debug("Requesting " + req.getPath()); return super(req); } } \[/java\]

Encadear comportamentos através de herança de classes apresenta diversos problemas de acoplamento e dificulta a customização de um processo.

Se eu desejasse um servidor com log mas sem as características de Cache, deveria criar uma quarta classe, `LoggingWithoutCacheServer` que herda de `Server` **e reescrever o código de log**. Costuma-se então sugerir a utilização de herança para evitar copiar-e-colar de código, mas podemos ver que somente com ela isso não é verdade.

Herança de classes é positivo por causa do polimorfismo, [mas cria uma acoplamento perigoso](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/), já discutido por diversos autores, em especial nos livros [Effective Java](https://blog.caelum.com.br/effective-java-segunda-edicao/) e Design Patterns.

Em Ruby temos uma abordagem semelhante de reutilização de comportamento através da herança de classe ou através da inclusão de módulos:

\[ruby\] module ServiceProvider def service(req) Response.new "<html>get para #{req.path}</html>" end end

module CacheableProvider def service(req) res = super(req) res.headers\["Cache-control"\] = "public, max-age=7200" res end end

module LoggingProvider def service(req) Logger.debug "Requesting #{req.path}" super(req) end end \[/ruby\]

E agora podemos descrever nosso serviço através da inclusão dos módulos, na ordem que for necessária:

\[ruby\] class Server include ServiceProvider include CacheableProvider include LoggingProvider end \[/ruby\]

Se desejamos uma outra ordem de execução qualquer, basta mudar a ordem ou adicionar novos módulos que delegam para `super` quando desejado. Aqui ainda temos problemas de herança apresentados no post de 2006: o acoplamento da classe `Server` com os módulos é muito forte e, pior ainda, **criamos dependências implicitas entre os módulos que antes não se conheciam**: se um dos providers de log possui um método chamado `info` e o outro provider possui outro método `info`, sua aplicação não funcionará como o esperado.

A inclusão de módulos para compor a herança, seja em tempo de codificação ou execução, mantem o acoplamento alto. Note que ambas as abordagens permitem a adição de novo comportamento ou encadeamento dos mesmos visando minimizar a atualização na classe filha, mas acabando por necessitar que o autor de cada módulo conheça os detalhes dos outros, aumentando o acoplamento.

[Favorecer composição de comportamentos através da agregação de objetos diminui o acoplamento entre os mesmos](https://blog.caelum.com.br/effective-java-segunda-edicao/). Em Java teríamos:

\[java\] interface Server { Response service(Request req); } class DefaultServer implements Server { public Response service(Request req) { return new Response("<html>get para " + req.getPath() + "</html>"); } } class CacheableServer implements Server { private Server delegate; CacheableServer(Server delegate) { this.delegate = delegate; } public Response service(Request req) { Response resp = delegate.service(req); resp.addHeader("Cache-control", "public, max-age=7200"); return resp; } } class LoggingServer implements Server { private Server delegate; LoggingServer(Server delegate) { this.delegate = delegate; } public Response service(Request req) { Logger.debug("Requesting " + req.getPath()); return delegate.service(req); } } \[/java\]

E a criação de nosso serviço ou processo pode ser totalmente customizada:

\[java\] Server log = new LoggingServer(new Server()); Server logAndCache = new LoggingServer(new CachingServer(new DefaultServer())); \[/java\]

Para quem gostaria de utilizar essa prática de compor o processamento de uma requisição em diversas fases, como as empresas de host, a [funcionalidade de Valves do Tomcat em 2004 permitia que criassem filtros](http://tomcat.apache.org/tomcat-4.1-doc/config/valve.html), anteriormente possuindo [apis não publicadas em sua versão 3](http://tomcat.apache.org/tomcat-3.3-doc/internal.html), exatamente como aqueles que [o middleware Rack faz hoje em dia](http://vision-media.ca/resources/ruby/ruby-rack-middleware-tutorial):

\[ruby\] module Rack class CustomLog def initialize app @app = app end def call env status, headers, body = @app.call env if env\['HTTP\_METHOD'\]=='HEAD' body = nil end \[status, headers, body\] end end end \[/ruby\]

Em linguages funcionais como Javascript ou com suporte a ponteiros de função, como C, podemos criar o mesmo tipo de composição de comportamento:

\[ruby\] server = function (req) { // return nova resposta }

function support\_log(base) { return function(req) { var res = base(req); console.log("logando o resultado"); return req; } }

function support\_head(base) { return function(req) { var res = base(req); if(req.method == "HEAD"){ res.body = ""; } return res; } }

function parse(req) { return support\_head(support\_log(server))(req); } \[/ruby\]

Note que em todos os casos, assim como [na sequência de converters e wrappers do XStream](http://hirenscafe.blogspot.com/2010/02/object-serialization-of-non-serialized.html), [favorecemos composição ao invés de herança](https://blog.caelum.com.br/livros-escolhendo-a-trindade-do-desenvolvedor-java/).

Ao mesmo tempo, encontramos uma dificuldade na hora de implementar a composição: como compartilhar objetos para serem acessados entre diferentes comportamentos que foram adicionados ao nosso objeto original?

A solução mais encontrada em todas as linguagens mencionadas é a criação de um escopo novo contendo todos os objetos a serem compartilhados (contexto), um objeto que funciona como um mapa. Em linguagens com tipagem estática isso pode implicar em um perigo a mais, um cast forçado:

\[java\] public Response service(Request req, Context ctx) { User user = (User) ctx.get("logged\_in\_user"); Logger.debug("Requesting " + req.getPath()); return delegate.service(req); } \[/java\]

Essa é a solução adotada pelo [servlet API](http://java.sun.com/blueprints/guidelines/designing_enterprise_applications_2e/web-tier/web-tier5.html), além de ser adotado [pela implementação do Rack middleware com o env](http://rack.rubyforge.org/doc/SPEC.html).

Uma outra solução pouco utilizada e que poderia aumentar a legibilidade em troca de menos visibilidade é fazer o lazy load de acesso a variável através do method\_missing, algo como:

\[ruby\] def method\_missing(sym, args) return env\[sym\] if env.include?(sym.to\_s) super(sym, args) end \[/ruby\]

Compor um comportamento através de outros é fundamental para diminuir a complexidade de métodos e classes. Podemos fazer isso através de herança, pagando-se um preço alto, ou utilizar chain of responsability, interceptors e outros padrões, que podem ser implementados puramente através de interfaces e composição.
