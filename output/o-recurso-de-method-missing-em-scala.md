---
title: "O recurso de method missing em Scala"
date: "2011-09-20"
author: "alberto.souza"
authorEmail: "alberto.souza@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um dos argumentos de quem gosta de linguagens estaticamente tipadas é a ajuda fornecida pelo compilador no momento da escrita de código. Erros como invocação de métodos que não existem e assinatura errada são facilmente evitados. O mesmo não é encontrado nas linguagens mais dinâmicas, como Ruby, onde os testes de unidade podem trazer essa garantia. Entretanto, a dinamicidade dessas linguagens permite que escrevamos códigos que não são possiveis nas estaticamente tipadas, como Java.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/09/method_missing.jpg "method_missing")](https://blog.caelum.com.br/wp-content/uploads/2011/09/method_missing.jpg)

Por exemplo, uma feature muito utilizada pelos códigos escritos em Ruby é o _method missing_. Talvez apareça até em excesso em alguns projetos,  sendo o caso mais conhecido o próprio [Rails](http://www.caelum.com.br/curso/rr-71-ruby-on-rails/), principalmente para escrever DSLs.  Um desses usos é poder buscar por todas as propriedades das classes de modelo. Normalmente teríamos que criar métodos como `buscaPorNome`, `buscaPorEmail` ou combinações destes para atingir esse objetivo. Só que no framework ganhamos métodos prontos como `Pessoa.findByNome` ou `Pessoa.findByEmail`. O ponto é que esses métodos não existem na classe, mas o Rails permite que a invocação seja realizada e, caso o método não exista, será invocado o `method_missing(nomeDoMetodoInvocado,argumentosPassados)` . Dessa forma conseguimos ganhar esses comportamentos já prontos.

Scala, a partir da versão 2.9, possui um recurso com o mesmo objetivo do `method_missing`. Para exemplificar vamos utilizar uma agenda de contatos. Queremos que nossa agenda seja capaz de buscar contatos através de algumas propriedades da nossa classe. Uma implementação inicial seria como a que segue:

\[scala\] class AgendaPadrao { def findByNome(nome:String) = { // implementacao padrao }

def findByEmail(email:String) = { // implementacao padrao }

} \[/scala\]

Aqui caímos no problema mencionado acima, caso queiramos adicionar novas buscas precisamos adicionar novos métodos. Uma outra possibilidade seria recorrer ao paradigma funcional, para receber o critério de busca como parâmetro, já que o Scala também suporta essa abordagem. Porém, a ideia é tentar resolver da mesma forma que o Rails, queremos dar a possibilidade de alguém conseguir invocar na nossa agenda métodos como: `findByNome`, `findByEmail` ou qualquer outra. O primeiro passo a fazer é herdar de uma [trait](http://www.scala-lang.org/node/126) chamada [Dynamic](http://www.scala-lang.org/archives/downloads/distrib/files/nightly/docs/library/scala/Dynamic.html). É uma trait apenas de marcação, assim como `Serializable` no Java. Em seguida implementamos um método `applyDynamic`. O código é o que segue:

\[scala\] class Agenda extends Dynamic { def applyDynamic(fakeMethodName: String)(args: Any\*) = { "metodo %s com parametros %s".format(fakeMethodName,args) } } \[/scala\]

Agora já podemos chamar qualquer método na nossa classe, existindo ou não. Vamos realizar a implementação usando o padrão `findByPropriedade`. Vamos usar um pouco de expressões regulares para conseguir identificar a propriedade que está sendo usada para realizar a busca. Também precisaremos de reflection para invocar o método que retorna o valor da propriedade na nossa classe:

\[scala\] class Agenda extends Dynamic { def applyDynamic(fakeMethodName: String)(args: Any\*) = { val FindBy = """(findBy)(\\w+)""".r val found = fakeMethodName match { case Like(\_, property) => { val realMethodName = lowerFirstLetter(property) contatos.filter(c => invokeMethod\[String\](c,realMethodNAme).(c,realMethodName).startsWith(args(0).toString)) } } found } }

\[/scala\]

Ainda podemos evoluir nossa implementação de method missing para suportar mais buscas. O exemplo completo do código pode ser encontrado [aqui](https://gist.github.com/1176751). Usamos uma implementação baseada em listas, mas a mesma poderia ser levada para dentro de um _DAO_ permitindo buscas diretamente no banco de dados. Importante notar que precisamos tratar o caso que nenhum padrão for encontrado, para dar feedback para quem fez a invocação informando que aquele método não existe. Nesse caso lançamos uma exception. Um outro ponto a ser observado é a perda da checagem em tempo de compilação, agora só descobrimos que um método não existe quando rodamos o programa, explicitando mais ainda a importância dos testes de unidade.

E vocês, o que acham dessa feature? O que acham dessa aproximação do estaticamente tipado ao dinâmico?
