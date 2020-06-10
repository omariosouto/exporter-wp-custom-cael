---
title: "Validações customizadas no Asp.Net MVC"
date: "2014-07-22"
author: "victor.harada"
authorEmail: "victorkendy@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Uma das formas de fazer validação em uma aplicação Asp.Net MVC é utilizar as anotações (Attributes) definidos no namespace _System.ComponentModel.DataAnnotations_. Se quisermos, por exemplo, que o campo nome de um modelo de usuário seja obrigatório utilizamos o _RequiredAttribute_:

\[java\] public class Usuario { \[Required\] public String Nome { get; set; }

public String Password { get; set; } } \[/java\]

E dentro do controller da aplicação, podemos verificar se as informações enviadas pelo usuário são válidas utilizando o _ModelState_:

\[java\] public class UsuarioController : Controller { public ActionResult Cadastra(Usuario usuario) { if(ModelState.IsValid) { // Modelo é válido } else { // Modelo é inválido } } } \[/java\]

Mas e se quiséssemos validar que o usuário preencheu um password forte (contém números, letras maiúsculas e minúsculas). Nesse caso, poderíamos colocar a regra de validação diretamente dentro do controller da aplicação:

\[java\] public class UsuarioController : Controller { public ActionResult Cadastra(Usuario usuario) { Regex regex = new Regex(@"((?=.\*\\d)(?=.\*\[a-z\])(?=.\*\[A-Z\]).{4,100})"); if(regex.IsMatch(usuario.Password)) { ModelState.AddModelError("password.Fraco", "Password muito fraco") } if(ModelState.IsValid) { // Modelo é válido } else { // Modelo é inválido } } } \[/java\]

Mas e se essa validação for utilizada em outros pontos da aplicação? Nesse caso, seria mais interessante se pudéssemos isolar o código da validação dentro de uma nova anotação.

## Nova anotação de validação

Vamos então criar uma nova anotação de validação chamada _PasswordForteAttribute_. Para que possamos utilizar essa classe como uma validação no Asp.Net MVC, ela precisa herdar de _ValidationAttribute_:

\[java\] public class PasswordForteAttribute : ValidationAttribute {

} \[/java\]

Dentro dessa classe, a regra de validação customizada é implementada dentro de um método chamado _IsValid_:

\[java\] public class PasswordForteAttribute : ValidationAttribute { public override bool IsValid(object value) {

} } \[/java\]

Caso o value seja um valor válido, o método _IsValid_ deve devolver o valor _true_, senão _false:_

\[java\] public class PasswordForteAttribute : ValidationAttribute { private static readonly String PasswordPattern = @"((?=.\*\\d)(?=.\*\[a-z\])(?=.\*\[A-Z\]).{4,100})";

private static readonly Regex PasswordRegex = new Regex(PasswordPattern);

public override bool IsValid(object value) { String password = value.ToString(); return PasswordRegex.IsMatch(password); } } \[/java\]

Quando o campo não obedecer a regra de validação, precisamos informar qual é a mensagem de validação que deve ser exibida para o usuário. Para isso, precisamos passar a mensagem como argumento do construtor da classe _ValidationAttribute:_

\[java\] public class PasswordForteAttribute : ValidationAttribute { public PasswordForteAttribute() : base("Senha muito fraca") { }

// implementação da validação } \[/java\]

Agora precisamos simplesmente anotar o modelo com essa nova anotação e depois remover a lógica da validação customizada da action do controller, lembrando que como a classe será utilizada como anotação, não precisamos do sufixo _Attribute_:

\[java\] public class Usuario { \[Required\] public String Nome { get; set; }

\[PasswordForte\] public String Password { get; set; } }

public class UsuarioController : Controller { public ActionResult Cadastra(Usuario usuario) { if(ModelState.IsValid) { // Modelo é válido } else { // Modelo é inválido } } } \[/java\]

Agora temos um código de validação isolado e reutilizável.

## Validação no navegador

Essa validação que criamos será executada quando alguma action de um controller receber um objeto do tipo usuário, ou seja, para executarmos a validação precisamos de uma requisição web! Mas quando utilizamos as anotações do namespace _System.ComponentModel.DataAnnotations_, a maioria delas define validações que são executadas no navegador do usuário da aplicação, tudo o que precisamos fazer para utilizar essa validação no cliente é utilizar o _HtmlHelper_ para gerar o formulário dentro de uma view fortemente tipada, como fazemos no curso de [razor do Alura](http://www.alura.com.br/cursos-online-net/razor):

\[java\] @model Aplicacao.Models.Usuario

@using(Html.BeginForm("Cadastra", "Usuario", FormMethod.Post)) { @Html.LabelFor(u => u.Nome) @Html.TextBoxFor(u => u.Nome) @Html.ValidationMessageFor(u => u.Nome)

@Html.LabelFor(u => u.Password) @Html.PasswordFor(u => u.Password) @Html.ValidationMessageFor(u => u.Password)

<input type="submit" value="Enviar" /> } \[/java\]

Queremos que a nossa validação de password forte também seja executada no navegador do usuário, para isso, precisamos fazer com que a classe _PasswordForteAttribute_ implemente a interface _IClientValidatable_:

\[java\] public class PasswordForteAttribute : ValidationAttribute, IClientValidatable \[/java\]

Todas as classes que implementam _IClientValidatable_ precisam informar ao Asp.Net MVC qual é o código javascript que será executado no navegador do cliente. Isso é feito através do método _GetValidationRules_:

\[java\] public class PasswordForteAttribute : ValidationAttribute, IClientValidatable { private static readonly String PasswordPattern = @"((?=.\*\\d)(?=.\*\[a-z\])(?=.\*\[A-Z\]).{4,100})";

// Implementação do IsValid

public IEnumerable GetClientValidationRules(ModelMetadata metadata, ControllerContext context) {

} } \[/java\]

O _IEnumerable<ModelClientValidationRule>_ é o objeto que configura qual é o código javascript que será executado para fazer a validação. Dentro dele precisamos informar qual é a mensagem de validação que deve ser exibida para o usuário, o nome da regra de validação (que será utilizado para identificar qual é a função javascript que será executada) e também os parâmetros que devem ser passadas para a função de validação.

\[java\] public class PasswordForteAttribute : ValidationAttribute, IClientValidatable { private static readonly String PasswordPattern = @"((?=.\*\\d)(?=.\*\[a-z\])(?=.\*\[A-Z\]).{4,100})";

// Implementação do IsValid

public IEnumerable GetClientValidationRules(ModelMetadata metadata, ControllerContext context) { var regra = new ModelClientValidationRule(); regra.ErrorMessage = "Password muito fraco"; regra.ValidationType = "passwordforte"; regra.ValidationParameters.Add("regex", PasswordPattern); return new List(){ regra }; } } \[/java\]

A implementação das regras de validação no navegador, no Asp.Net MVC, é feita utilizando-se uma biblioteca chamada [jquery.validation.unobtrusive](https://www.nuget.org/packages/jQuery.Validation.Unobtrusive/ "jQuery Validation Unobtrusive no Nuget"), então vamos incluí-la na view do formulário:

\[html\] @\* código do formulário \*@

@\* Assumindo que a versão do jquery é a 1.10.2 e está na pasta Scripts do projeto \*@ <script src="~/Scripts/jquery-1.10.2.js"></script> <script src="~/Scripts/jquery.validate.js"></script> <script src="~/Scripts/jquery.validate.unobtrusive.js"></script> \[/html\]

E agora precisamos configurar o jquery validate para que ele reconheça a nova regra de validação. Para isso precisamos registrar o nome dessa nova regra de validação e os parâmetros que ela precisa (no caso, o parâmetro é a expressão regular para o password):

\[html\] @\* Importa os scripts do jquery \*@ <script> // o primeiro argumento é o nome da validação // e o segundo é o nome do argumento da validação $.validator.unobtrusive.adapters.addSingleVal("passwordforte", "regex"); </script> \[/html\]

Para finalizar precisamos informar qual é a função de validação que será executada:

\[html\] <script> $.validator.unobtrusive.adapters.addSingleVal("passwordforte", "regex");

// Aqui precisamos falar qual é o nome da regra de validação: passwordforte // e a função de validação $.validator.addMethod("passwordforte", function (value, element, regex) { // implementação da validação }); </script> \[/html\]

Na função de validação, recebemos 3 argumentos, o valor que está sendo validado (no caso o campo de texto para o password), o elemento html e, por fim, os parâmetros que foram passados no _ValidationParameters_ do _ModelClientValidationRule_. Na implementação do método, precisamos devolver algo que avalie para _true_ caso o value seja válido e para _false_, caso contrário.

\[html\] <script> $.validator.unobtrusive.adapters.addSingleVal("passwordforte", "regex"); $.validator.addMethod("passwordforte", function (value, element, regex) { var passwordRegex = new RegExp(regex); return value.match(passwordRegex); }); </script> \[/html\]

Isso é tudo que precisamos fazer para criar uma nova validação que será executada no navegador do cliente! Podemos ainda melhorar esse código isolando os códigos de validação dentro de um arquivo javascript e importar esse arquivo dentro do layout da aplicação para que o código não fique repetido em todos os formulários.

## Simplificando a validação

A validação da senha é parecida com uma outra validação que já existe no Asp.Net MVC, a _RegularExpressionAttribute_, e por isso podemos reutilizar uma parte do código que já está pronta no .Net para implementar a validação na view. Ao invés de utilizarmos diretamente o _ModelClientValidationRule_, podemos utilizar uma de suas classes filhas para utilizarmos a validação na view implementada pelo Asp.Net MVC. Para a validação com expressões regulares, utilizamos a _ModelClientValidationRegexRule_:

\[java\] public IEnumerable GetClientValidationRules(ModelMetadata metadata, ControllerContext context) { var rule = new ModelClientValidationRegexRule("Password muito fraco", PasswordPattern); return new List() { rule }; } \[/java\]

Utilizando essa implementação para o _GetClientValidationRule_, só precisamos importar os códigos javascript para o _jquery.validate.unobtrusive_, sem implementarmos nossa própria função de validação. A implementação dessa validação pode ser encontrada nesse [gist](https://gist.github.com/victorkendy/16257f151740305c96c0 "Implementação da validação").

Para mais informações sobre a criação de validações customizadas, você pode ler o [post do Brad Wilson](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html "Unobtrusive validation") sobre o tema e para mais um exemplo prático, temos esse [artigo](http://www.codeproject.com/Articles/613330/Building-Client-JavaScript-Custom-Validation-in-AS "validação customizada - Code Project") do code project.

Aprenda mais sobre as tecnologias do .Net em nossos [cursos presenciais](https://www.caelum.com.br/cursos-dotnet/) ou nas [formações online do Alura](http://www.alura.com.br/trilha/csharp).
