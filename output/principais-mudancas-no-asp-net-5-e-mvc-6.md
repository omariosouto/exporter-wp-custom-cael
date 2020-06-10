---
title: "Principais mudanças no ASP.NET 5 e MVC 6"
date: "2015-03-10"
author: "gabriel.ferreira"
authorEmail: "gabriel.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Como [já postamos aqui](https://blog.caelum.com.br/net-agora-e-open-source/), a maior parte do .NET agora é open source e junto com essa novidade estão vindo outras. Pra começar, o ASP.NET foi praticamente **reescrito do zero**. Sendo assim, essa é a versão mais significativa em toda a história do ASP.NET.

Entre várias mudanças, tentaremos citar as [mais importantes até agora](http://stephenwalther.com/archive/2015/02/24/top-10-changes-in-asp-net-5-and-mvc-6).

**1- Não teremos mais Visual Basic**

**ATUALIZAÇÃO:** _quando escrevemos esse post a Microsoft realmente tinha planos de não mais suportar o VB. Porém, após isso ser noticiado a comunidade de desenvolvedores VB não gostou, fez barulho e conseguiu o que queria: o suporte ao VB foi incluído na nova versão. Você pode ler mais a respeito [aqui](http://www.infoq.com/news/2015/04/VB-Core)._

Ainda não é oficial, mas ao que tudo indica([aqui](https://github.com/aspnet/Home/issues/236) e [aqui](http://stackoverflow.com/questions/28040944/vb-net-support-for-asp-net-5-mvc6)) é hora de dizer tchau ao VB. O ASP.NET 5 vai suportar somente C#.

Apesar de ainda termos aplicações rodando com VB, a grande maioria que vai desenvolver utilizando ASP.NET hoje em dia prefere o C# mesmo.

O VB serviu ao seu propósito, mas acredito que está na hora de seguirmos em frente com relação à ele.

**2- Web Forms perdendo força**

Se quiser você poderá desenvolver usando o Web Forms ainda, pois a [Microsoft diz](http://www.asp.net/vnext/overview/aspnet-vnext/aspnet-5-overview#webforms) que ele não será descontinuado e que continuarão lançando atualizações para o mesmo. Porém, ele não fará parte do ASP.NET 5 e você perderá vários recursos bacanas que virão por aí.

Ou seja, se possível é melhor focar no desenvolvimento com o MVC mesmo.

**3- ASP.NET no OSX e Linux**

Isso mesmo, agora você que não usa Windows poderá desenvolver aplicações para o ASP.NET no seu Mac ou usando uma distro Linux.

Você não precisará do Visual Studio pra isso e poderá programar usando seu editor favorito. Já existe um projeto open source que dá suporte para o intelisense do C#, o [Omnisharp](http://www.omnisharp.net/). Ele funciona com Sublime Text, Atom, Emacs, Vim e Brackets.

**4- Suporte a Bower, NPM e GruntJS**

Pra galera do front-end, essa do [Grunt](http://gruntjs.com/) é bacana. O Grunt é uma ferramenta que tem como objetivo automatizar tarefas, principalmente com Javascript.

Pra poder dar suporte ao Grunt, a Microsoft precisou dar suporte à mais dois gerenciadores de pacotes(além do [Nuget](https://www.nuget.org/)): [NPM](https://www.npmjs.com/) e [Bower](http://bower.io/).

**5- AngularJS**

O Visual Studio 2015 vai ter templates para a criação de controllers, directives, modules e factories do AngularJS. Com o suporte ao Grunt, o ASP.NET se tornará um ótimo framework server-side pra criar aplicações client-side com o Angular.

**6- ASP.NET Dependency Injection Framework**

Não precisaremos mais depender de frameworks de injeção de dependência de terceiros como [Ninject](http://www.ninject.org/) e [AutoFac](http://autofac.org/): o ASP.NET 5 já virá com suporte nativo pra injeção de dependência.

**7- xUnit.net**

Nas versões anteriores do ASP.NET MVC o framework padrão para testes era o [Visual Studio Unit Testing Framework](https://msdn.microsoft.com/en-us/library/ms243147%28VS.80%29.aspx).

O ASP.NET 5 utilizará o [xUnit.net](https://github.com/aspnet/xunit). Olhando o [repositório do ASP.NET MVC](https://github.com/aspnet/mvc), vemos que ele já está usando o xUnit para testes.

**8- Tag Helpers**

Esse é bem legal e vai ter um grande impacto na maneira que criamos nossas views no MVC.

Vamos considerar o seguinte código que cria uma view simples para um produto:

\[code language="java"\]@model Caelum.Models.Produto

@using (Html.BeginForm("Salva", "Produto", FormMethod.Post)) { <div> @Html.LabelFor(p => p.Nome, "Nome:") @Html.TextBoxFor(p => p.Nome) </div> <input type="submit" value="Salva" / > }\[/code\]

Nesse exemplo os helpers Html.BeginForm, Html.LabelFor, Html.TextBoxFor nos ajudam a criar o formulário. Eles são legais e funcionaram muito bem para nós até agora. O problema com eles é que eles não são familiares para um desenvolvedor front-end: boa parte desses desenvolvedores têm conhecimento somente de HTML, CSS e Javascript e não é confortável com o conceito de métodos. Isso pode mais dificultar o trabalho do que facilitar.

Poderemos fazer isso da mesma forma usando tag helpers:

\[code language="java"\]@model Caelum.Models.Produto @addtaghelper "Microsoft.AspNet.Mvc.TagHelpers"

<form asp-controller="Produto" asp-action="Salva" method="post"> <div> <label asp-for="Nome">Nome:</label> <input asp-for="Nome" /> </div>

<input type="submit" value="Salvar" /> </form>\[/code\]

Essa página contém (aparentemente) somente tags HTML. Um desenvolvedor front-end ficaria bem mais confortável com esse código do que com o anterior.

[Aqui](https://github.com/DamianEdwards/TagHelperStarterWeb) tem um exemplo do uso de Tag Helpers.

_Fontes:_ [http://www.asp.net/vnext/overview/aspnet-vnext/aspnet-5-overview#webforms](http://www.asp.net/vnext/overview/aspnet-vnext/aspnet-5-overview#webforms) [http://www.theregister.co.uk/2015/02/24/now\_with\_grunt\_and\_gulp\_aspnet\_goes\_crossplatform\_in\_new\_visual\_studio\_2015\_preview/](http://www.theregister.co.uk/2015/02/24/now_with_grunt_and_gulp_aspnet_goes_crossplatform_in_new_visual_studio_2015_preview/) [http://stephenwalther.com/archive/2015/01/12/asp-net-5-and-angularjs-part-1-configuring-grunt-uglify-and-angularjs](http://stephenwalther.com/archive/2015/01/12/asp-net-5-and-angularjs-part-1-configuring-grunt-uglify-and-angularjs) [http://stephenwalther.com/archive/2015/02/24/top-10-changes-in-asp-net-5-and-mvc-6](http://stephenwalther.com/archive/2015/02/24/top-10-changes-in-asp-net-5-and-mvc-6) [https://github.com/aspnet/home](https://github.com/aspnet/home) [http://xunit.github.io/docs/getting-started-aspnet.html](http://xunit.github.io/docs/getting-started-aspnet.html) [http://www.hanselman.com/blog/ASPNET5VNextWorkInProgressExploringTagHelpers.aspx](http://www.hanselman.com/blog/ASPNET5VNextWorkInProgressExploringTagHelpers.aspx)
