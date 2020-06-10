---
title: "Injeção de dependências no ASP.NET MVC"
date: "2012-05-03"
author: "victor.harada"
authorEmail: "victorkendy@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Para melhorar a manutenibilidade de um sistema, [devemos](http://stackoverflow.com/questions/1257670/c-sharp-when-to-program-to-an-interface) [programar voltados para interface](http://www.arquiteturajava.com.br/livro/programe-voltado-a-interface-nao-a-implementacao.pdf). Assim, nos preocupamos apenas com o contrato definido pela interface, não com sua implementação, diminuindo o acoplamento do código. Uma das formas de facilitar essa prática é utilizar a injeção de dependências. Dessa forma, nossas classes não precisam conhecer a lógica de instanciação, ciclo devida e nem implementação dos componente do sistema, podendo mudar facilmente a implementação de suas dependências.

[![](https://blog.caelum.com.br/wp-content/uploads/2012/05/aspnet-300x232.gif "aspnet")](https://blog.caelum.com.br/wp-content/uploads/2012/05/aspnet.gif)

Quando escrevemos um controller no [ASP.NET MVC](http://www.caelum.com.br/curso/online/aspnetmvc/), é interessante que a classe não precise se preocupar com a instanciação de suas dependências. Queremos, por exemplo, receber todas essas dependências no construtor. Vamos usar como exemplo um `UserController` que precisa de um `IUserDao`:

\[java\] class UserController { private IUserDao userDao; public UserController(IUserDao userDao) { this.userDao = userDao; } } \[/java\]

Porém, o ASP.NET MVC não consegue instanciar um controller escrito dessa forma. Ele não sabe como instanciar um `IUserDao`! Por esse motivo, apenas controllers que tenham um construtor sem argumentos podem ser instanciados. Para mudar esse comportamento, precisamos ensinar o ASP.NET MVC a instanciar controladores que recebem parâmetros no construtor.

## Unity Container

Para que possamos criar instâncias de classes que recebam as dependências pelo construtor, precisamos primeiro guardar as instâncias dessas dependências em algum lugar. Precisamos, por exemplo, ter um `IUserDao` em mãos para passar para o construtor do `UserController`.

Existem diversos frameworks que nos ajudam nessa tarefa. Eles são conhecidos como frameworks de injeção de dependência. Nesse post utilizaremos o framework de injeção de dependências da Microsoft, o **[Unity](http://unity.codeplex.com/)**.

O Unity nos oferece uma classe chamada `UnityContainer`, classe na qual podemos registrar tipos que serão injetados. Para registrar uma dependência, utilizamos o método `RegisterType` do container, passando a interface, a implementação que deve ser injetada e um nome para ela.

No exemplo, queremos registrar o `UserController` como uma implementação de `IController` e o `UserDao` como uma implementação de `IUserDao`. Além disso, para seguir a convenção de nomes do ASP.NET MVC, registraremos o `UserController` com o nome _User_.

\[java\] Container.RegisterType(typeof(IUserDao), typeof(UserDao)); Container.RegisterType(typeof(IController), typeof(UserController), “User”); \[/java\]

Agora que temos registrado no Unity tanto o Dao quanto o Controller, podemos pedir instâncias dessas classes para ele! Ao pedirmos uma instância de `UserController`, o Unity se encarregará de enviar o `UserDao` para o construtor dele:

\[java\] IController controller = Container.Resolve<IController>("User"); \[/java\]

## ControllerFactory com Injeção de Dependências

Agora precisamos ensinar o ASP.NET MVC a utilizar o Unity para instanciar os controllers. Para fazer isso, precisamos criar uma classe que implementa `IControllerFactory`. Implementações dessa interface devem fornecer três métodos: `CreateController`, `ReleaseController` e `GetControllerSessionBehavior`.

`CreateController` recebe uma `String` com o nome do controller que precisa ser criado e deve devolver uma instância de `IController`. `ReleaseController` recebe um `IController` e deve liberar os recursos utilizados. A `String` com o nome do controller segue [a convenção do ASP.NET MVC](http://msdn.microsoft.com/en-us/gg618491), ou seja, quando recebemos a `String` "_User_", devemos criar uma instância de `UserController`.

\[java\] class UnityFactory : IControllerFactory { public UnityFactory() { this.Container = new UnityContainer(); this.Container.RegisterType(typeof(IUserDao), typeof(UserDao)); this.Container.RegisterType(typeof(IController), typeof(UserController), “User”); }

public IController CreateController (RequestContext context, String controllerName) { IController controller = this.Container.Resolve<IController>(controllerName); return controller; } // ... } \[/java\]

Precisamos também ensinar o ASP.NET MVC a destruir nosso controller e liberar quaisquer recursos que alocamos. Para isso, vamos implementar o método `ReleaseController`:

\[java\] public void ReleaseController(IController controller) { this.Container.Teardown(controller); } \[/java\]

O `GetControllerSessionBehavior` deve retornar o valor de um enum que define o comportamento da session do ASP.NET MVC. Como queremos que a Session tenha [o mesmo comportamento da Session padrão](http://msdn.microsoft.com/en-us/library/system.web.sessionstate.sessionstatebehavior.aspx), esse método vai retornar `System.Web.SessionState.SessionStateBehavior.Default`.

\[java\] public System.Web.SessionState.SessionStateBehavior GetControllerSessionBehavior(RequestContext ctx, string controllerName) { return System.Web.SessionState.SessionStateBehavior.Default; } \[/java\]

Estamos quase lá. Resta apenas substituir o mecanismo padrão do ASP.NET MVC pelo que acabamos de criar. Essa substituição é feita no arquivo `Global.asax` do projeto, através do método `SetControllerFactory`. Para manter a organização do arquivo `Global.asax`, o registro do `ControllerFactory` ficará junto com as configurações globais dentro do método `Application_Start()`:

\[java\] protected void Application\_Start() { AreaRegistration.RegisterAllAreas(); RegisterGlobalFilters(GlobalFilters.Filters); RegisterRoutes(RouteTable.Routes); UnityFactory factory = new UnityFactory(); ControllerBuilder.Current.SetControllerFactory(factory); } \[/java\]

Agora que a aplicação está utilizando a fábrica customizada, o ASP.NET MVC é capaz de instanciar o `UserController`, passando o `UserDao` para ele! Essa factory ainda pode ser melhorada. Poderíamos, por exemplo, registrar automaticamente no Unity todos os controllers e componentes que serão injetados. Você pode ver essa implementação, um pouco mais complicada, [aqui](https://gist.github.com/2490266 "Implementação da Unity Factory"). Além disso, você pode ver a implementação da factory utilizando o container [Windsor](http://docs.castleproject.org/Windsor.MainPage.ashx) no projeto [agile-tickets-csharp](https://github.com/caelum/agile-tickets-csharp), projeto utilizado no curso [PM-87](http://msdn.microsoft.com/en-us/gg618491) da Caelum.

Pronto! Nossos controllers agora podem declarar suas dependências no construtor, deixando o código mais claro e mais fácil de ser testado. Aprenda a desenvolver com ASP.NET MVC em nosso [curso online](http://www.caelum.com.br/curso/online/aspnetmvc/ "Curso Online de ASP.NET MVC").
