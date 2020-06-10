---
title: "Java EE 6: Começando com Bean Validation"
date: "2010-02-04"
author: "lucasas"
authorEmail: "lucasas@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Não existe tarefa mais comum hoje em dia do que validar dados em uma aplicação. Por exemplo, validamos se na camada de apresentação o usuário preencheu algum campo obrigatório, depois fazemos a mesma lógica de validação em nossa regra de negócio e por último validamos se os dados que serão salvos no banco também estão corretos. O que na maioria das vezes os desenvolvedores fazem é validar estas regras em todos os lugares, e muitas vezes resulta em validações complicadas e possíveis erros na aplicação. Muitos desenvolvedores consideram essa tarefa enfadonha.

Na nova versão do Java EE lançada dezembro de 2009 existem grandes novidades: [Servlets 3.0](https://blog.caelum.com.br/java-ee6-comecando-com-as-servlets-3-0/), JAX-RS, CDI, JSF 2.0 e a **Bean Validation**, definida pela [JSR 303](http://jcp.org/en/jsr/detail?id=303). Com a Bean Validation é possível realizarmos validações atráves de metadados (anotações) e as utilizarmos em todas as camadas de nossa aplicação.

Podemos até mesmo criar nossas próprias regras de validações e aplicarmos em outros pontos do nosso sistema. Essas regras podem ser facilmente integradas com JPA e JSF, por exemplo. Vamos considerar uma classe que represente um contato:

\[java\] public class Contato {

private String nome;

private String email;

// getters e setters

public void enviaEmail() { // codigo envio de email } } \[/java\]

E vamos adicionar nossas regras de validação utilizando a Bean Validation:

\[java\] public class Contato {

@NotEmpty @Pattern(regexp = ".+@.+\\\\.\[a-z\]+") private String email; @NotEmpty private String nome;

// getters e setters

public void enviaEmail() { // codigo envio de email } } \[/java\]

A anotação `@NotEmpty` garante que o email não poderá ser vazio assim como o atributo nome. No atributo email também colocamos a anotação `@Pattern`, onde podemos passar qualquer expressão regular, que o Bean Validation validará o valor passado ao atributo `nome` com a expressão regular.

Lembrando que o Bean Validation é uma especificação e como toda especificação, para utilizarmos precisamos de uma implementação para a utilizarmos. A implementação usada nos exemplos é a [Hibernate Validator](http://hibernate.org/subprojects/validator), e é a implementação de referência.

**Integrando com o JSF**

O código abaixo mostra um formulário simples utilizando JSF, que chama um método `enviaEmail` do ManagedBean quando clicamos no botão "_EnviaEmail_":

\[xml\] <h:form id="formulario"> <h:panelGrid columns="3"> <h:inputText id="email" value="#{contato.email}"/> <h:message for="email" styleClass="error"/> <h:commandButton action="#{contato.enviaEmail}" value="Enviar Email"/> </h:panelGrid> </h:form> \[/xml\]

Precisamos ainda anotar nossa classe `Contato` com `@ManagedBean(name="contato")` e `@RequestScoped` para que a mesma seja um ManageBean e consiga integrar-se com o JSF. Quando clicarmos no botão "_Enviar Email_" automaticamente será validado se o atributo `email` foi preenchido a atende a expressão regular da anotação `@Pattern`.

Podemos muitas vezes nos deparar com algum caso onde é necessário fazermos uma validação um pouco mais específica, algo que não esteja implementado por padrão no Bean Validation. Validar um CEP seria um bom exemplo. O primeiro passo para fazermos nossa própria validação é criar uma anotação que sugira o nome desta validação.

\[java\] @Constraint(validatedBy = CepValidator.class) @Documented @Target(ElementType.FIELD) @Retention(RetentionPolicy.RUNTIME) public @interface Cep { String message() default "Cep inválido"; Class<?>\[\] groups() default { }; Class<? extends Payload>\[\] payload() default { };

} \[/java\]

Reparem que em nenhum momento fizemos de fato a validação se o CEP é válido ou não. Por isso anotamos a anotação CEP que acabamos de criar com `@Constraint` e setamos o atributo `validatedBy` com a classe que está nossa validação realmente. O próximo passo é criarmos a classe `CEPValidator` que fará a validação. Esta classe tem que obrigatóriamente implementar a interface `javax.validation.ConstraintValidator`:

\[java\] public class CepValidator implements ConstraintValidator<Cep, String> {

private Pattern pattern = Pattern.compile("\[0-9\]{5}-\[0-9\]{3}");

@Override public void initialize(Cep constraintAnnotation) {

}

@Override public boolean isValid(String value, ConstraintValidatorContext context) { Matcher m = pattern.matcher(value); return m.matches(); }

} \[/java\]

No método `isValid` verificamos e retornamos um booleano dizendo se a `String` passada como argumento está de acordo com a expressão regular que criamos no topo da classe. Podemos agora validar qualquer atributo de algum Java Bean, devendo apenas anotar o atributo com a nossa recém criada `@CEP`. Por exemplo:

\[java\] public class Contato {

@Cep private String cep;

// getters e setters } \[/java\]

Para rodar esse exemplo precisamos de um servidor de aplicação compatível com o Java EE 6, como o Glassfish 3.0. A premissa do Bean Validation é que muitos outros frameworks acabem por adotar um único mecanismo de validação, sendo possível reaproveitar o código criado aqui. O [Caelum Stella](http://stella.caelum.com.br/), que fornece diversos validadores para sistemas que envolvem o domínio brasileiro, em breve será compatível com essa especificação, possibilitando que você utilize-o em todas as [diversas especificações que compões o Java EE](http://www.caelum.com.br/curso/formacao-consultor-java-ee-avancado/).

Além disso precisamos de um Servidor de Aplicação compatível com a versão 6 do Java EE. Por enquanto o único servidor que implementa essa versão é o Glassfish v3.0 que por sinal também é uma implementação de referência.
