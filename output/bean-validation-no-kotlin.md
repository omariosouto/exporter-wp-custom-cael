---
title: "Bean Validation no Kotlin"
date: "2017-12-11"
author: "Thiago Andrade"
authorEmail: "thiago.andrade@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Em uma API que estávamos construindo para captar algumas avaliações de cursos, optamos por utilizar o Spring como framework juntamente com o Kotlin como linguagem de programação.

Em um dos pontos deste sistema precisávamos receber um JSON que continha algumas informações referentes a uma avaliação feita por um aluno, para depois preparar as informações que queríamos persistir.

No dia-a-dia de um desenvolvedor, é muito comum lidar com [validações](https://blog.caelum.com.br/java-ee-6-comecando-com-bean-validation/), com o principal objetivo de manter a consistência dos dados de uma aplicação.

Nesta aplicação não foi diferente, precisamos de algumas validações básicas para garantir consistência. Em um dos casos tínhamos um modelo que representava uma resposta:

```kotlin

data class Resposta(

        var observacao: String = "",

        var respostas: SortedSet<String> = sortedSetOf(),

        var idPergunta: Long = 0,

        var hashTurmaId: String = ""

)
```

Como algumas informações precisam ser validadas, afinal não queríamos dados inconsistentes circulando na nossa aplicação, utilizar o Bean Validation seria suficiente. Logo:

```kotlin

data class Resposta(

        var observacao: String = "",

        @Size(min = 1) var respostas: SortedSet<String> = sortedSetOf(),

        @NotNull var idPergunta: Long = 0,

        @NotBlank var hashTurmaId: String = ""

)
```

Então com a classe feita ficou faltando nosso Controller para receber a requisição. A princípio, simplesmente queremos validar se existem erros e, se existir, ele devolve um HTTP Status Code 400, senão ele devolve um Status 202:

```kotlin

@RequestMapping("resposta")
@RestController
class RespostaController {

    @Autowired
    private lateinit var respostaAlunoService: RespostaAlunoService

    @PostMapping(consumes = arrayOf(MediaType.APPLICATION_JSON_VALUE))
    fun salva(@Valid @RequestBody resposta: Resposta, bindingResult: BindingResult): ResponseEntity<Any> {

        if (bindingResult.hasErrors()) {

            return ResponseEntity.badRequest().build()

        }

        respostaAlunoService.save(resposta)

        return  ResponseEntity.accepted().build()

    }

}
```

## Validando os dados

Agora que temos um mínimo necessário, chegou o momento de verificar se nossa validação usando Bean Validation trará os resultados esperados. Fazemos então uma requisição com dados consistentes:

```json

{

    "observacao":"uma observação",

    "respostas":["uma resposta", "outra resposta"],

    "idPergunta":777,

    "hashTurmaId":"202cb962ac59075b964b07152d234b70"

}
```

Como esperado deu tudo certo, primeiro passo concluído.

Agora tentamos fazer uma requisição com dados inválidos, para indicar que tenho problemas de validação:

```json

{

    "observacao":"uma observação",

    "respostas":[],

    "hashTurmaId":""

}
```

No final das contas ele passa de boa e não identifica que existe erros! Onde será que erramos?

## Decompile para Java

Afinal, se fizermos um comparativo de como ficaria isso em Java, seria algo como:

```java

public class Resposta {

    private String observacao;

    @Size(min = 1)
    private  SortedSet<String> respostas = new TreeSet();

    @NotNull
    private Long idPergunta;

    @NotBlank
    private String hashTurmaId;

}
```

No Kotlin, o bytecode gerado na compilação pode ser [compatível desde](https://kotlinlang.org/docs/reference/faq.html) [Java](https://kotlinlang.org/docs/reference/faq.html) [6 ao Java 8](https://kotlinlang.org/docs/reference/faq.html) , sendo assim, se pudéssemos ver como fica nosso código feito em Kotlin para Java poderíamos ver o que está acontecendo por baixo dos panos.

Na IDE [IntelliJ](https://www.jetbrains.com/idea/), pertencente a [empresa que desenvolveu o Kotlin](https://www.jetbrains.com/), existe uma forma de decompilar o bytecode gerado a partir do Kotlin para Java. Dentro da IDE seguindo o menu **Tools > Kotlin > Show Kotlin Bytecode** podemos ver o bytecode gerado:

[![Kotlin Bytecode](https://blog.caelum.com.br/wp-content/uploads/2017/11/kotlin_bytecode.png)](https://blog.caelum.com.br/wp-content/uploads/2017/11/kotlin_bytecode.png)

No topo no lado esquerdo temos o botão **Decompile** e podemos assim decompilar para Java e visualizar o código equivalente:

```java

public final class Resposta {
   @NotNull
   private String observacao;
   @NotNull
   private SortedSet<String> respostas;
   private long idPergunta;
   @NotNull
   private String hashTurmaId;

   @NotNull
   public final String getObservacao() {
      return this.observacao;
   }

   public final void setObservacao(@NotNull String var1) {
      Intrinsics.checkParameterIsNotNull(var1, "<set-?>");
      this.observacao = var1;
   }

   @NotNull
   public final SortedSet getRespostas() {
      return this.respostas;
   }

   public final void setRespostas(@NotNull SortedSet var1) {
      Intrinsics.checkParameterIsNotNull(var1, "<set-?>");
      this.respostas = var1;
   }

   public final long getIdPergunta() {
      return this.idPergunta;
   }

   public final void setIdPergunta(long var1) {
      this.idPergunta = var1;
   }

   @NotNull
   public final String getHashTurmaId() {
      return this.hashTurmaId;
   }

   public final void setHashTurmaId(@NotNull String var1) {
      Intrinsics.checkParameterIsNotNull(var1, "<set-?>");
      this.hashTurmaId = var1;
   }

   public Resposta(@NotNull String observacao, @Size(min = 1) @NotNull SortedSet respostas, @javax.validation.constraints.NotNull long idPergunta, @NotBlank @NotNull String hashTurmaId) {
      Intrinsics.checkParameterIsNotNull(observacao, "observacao");
      Intrinsics.checkParameterIsNotNull(respostas, "respostas");
      Intrinsics.checkParameterIsNotNull(hashTurmaId, "hashTurmaId");
      super();
      this.observacao = observacao;
      this.respostas = respostas;
      this.idPergunta = idPergunta;
      this.hashTurmaId = hashTurmaId;
   }
 }
```

Quando observamos as annotations que nos interessam, referente as validações, notamos que elas foram não para os atributos mas sim para o construtor. Mas, afinal, porque este comportamento?

Para responder essa pergunta temos que ver onde podemos aplicar as annotations utilizadas na validação. Veja, por exemplo, o **@NotNull**:

```java

@Target({ElementType.METHOD, ElementType.FIELD, ElementType.ANNOTATION_TYPE, ElementType.CONSTRUCTOR, ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Constraint(
    validatedBy = {}
)
public @interface NotNull {

    //trecho omitido

}
```

Note que no @Target fica definido a aplicabilidade da annotation, informando os locais possíveis: METHOD, FIELD, ANNOTATION\_TYPE, CONSTRUCTOR e PARAMETER

## Como fazemos em Kotlin

O que vale lembrar é que no Kotlin conseguimos [acessar uma property, e não diretamente os atributos da classe](https://blog.caelum.com.br/como-preparar-o-ambiente-e-escrever-seu-primeiro-codigo-com-kotlin/) então embora o Target da annotaion suporte a utilização em um field, precisamos indicar para o Kotlin [onde queremos utilizar](https://kotlinlang.org/docs/reference/annotations.html#annotation-use-site-targets):

```kotlin

data class Resposta(

        var observacao: String = "",

        @field:Size(min = 1) var respostas: SortedSet<String> = sortedSetOf(),

        @field:NotNull var idPergunta: Long = 0,

        @field:NotBlank var hashTurmaId: String = ""

)
```

Note agora como fica o equivalente em Java após decompilar:

```java

public final class Resposta {
   @NotNull
   private String observacao;
   @Size(
      min = 1
   )
   @NotNull
   private SortedSet<String> respostas;
   @javax.validation.constraints.NotNull
   private long idPergunta;
   @NotBlank
   @NotNull
   private String hashTurmaId;

   @NotNull
   public final String getObservacao() {
      return this.observacao;
   }

   public final void setObservacao(@NotNull String var1) {
      Intrinsics.checkParameterIsNotNull(var1, "<set-?>");
      this.observacao = var1;
   }

   @NotNull
   public final SortedSet getRespostas() {
      return this.respostas;
   }

   public final void setRespostas(@NotNull SortedSet var1) {
      Intrinsics.checkParameterIsNotNull(var1, "<set-?>");
      this.respostas = var1;
   }

   public final long getIdPergunta() {
      return this.idPergunta;
   }

   public final void setIdPergunta(long var1) {
      this.idPergunta = var1;
   }

   @NotNull
   public final String getHashTurmaId() {
      return this.hashTurmaId;
   }

   public final void setHashTurmaId(@NotNull String var1) {
      Intrinsics.checkParameterIsNotNull(var1, "<set-?>");
      this.hashTurmaId = var1;
   }

   public Resposta(@NotNull String observacao, @NotNull SortedSet respostas, long idPergunta, @NotNull String hashTurmaId) {
      Intrinsics.checkParameterIsNotNull(observacao, "observacao");
      Intrinsics.checkParameterIsNotNull(respostas, "respostas");
      Intrinsics.checkParameterIsNotNull(hashTurmaId, "hashTurmaId");
      super();
      this.observacao = observacao;
      this.respostas = respostas;
      this.idPergunta = idPergunta;
      this.hashTurmaId = hashTurmaId;
   }

}
```

Nesse caso indicamos onde exatamente queremos utilizar a annotation, e fazendo com que agora nossa validação funcione!

E você, já teve que lidar com essa situação? Usou outra forma para resolver? Comente com a gente! ;)
