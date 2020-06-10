---
title: "Metaprogramação: Eigenclass em Ruby"
date: "2010-03-10"
author: "anderson.leite"
authorEmail: "anderson.leite@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O [modelo de objetos do Ruby](http://andersonleiteblog.wordpress.com/2010/02/09/metaprogramacao-ruby-object-model/) não é trivial. Em códigos mais complexos não é simples saber de onde realmente vem um método ou mesmo enxergar que toda classe em Ruby é um objeto, e isso pode causar algumas confusões. Uma das partes mais interessantes do modelo de objetos Ruby é conhecida como **Singleton Class**. Para chegar nela, vamos analisar o código abaixo:

\[ruby\]class Caelum def teste puts "teste" end end

obj = Caelum.new obj.teste \[/ruby\]

Nada demais até aqui. Apenas criamos uma classe e invocamos um método. Porém em Ruby nossas classes são abertas, e podemos adicionar novos métodos não só em classes já definidas, como também em objetos. Uma forma de fazer isso é:

\[ruby\] def obj.ensina puts "ruby" end

obj.ensina # => 'ruby' \[/ruby\]

Basicamente um objeto procura os métodos na sua classe (**obj** procura em **Caelum**) e, caso não encontre, sobe na hierarquia das superclasses, até [Object](http://ruby-doc.org/core/classes/Object.html) (ou [BasicObject](http://ruby-doc.org/core-1.9/classes/BasicObject.html) no Ruby 1.9).

Mas **onde está o método `ensina` na hierarquia?** Ele não pode estar definido em `obj`, afinal `obj` não é uma classe, e também não pode estar na classe `Caelum`, ou todas as instancias da classe `Caelum` também teriam esse método. [![](https://blog.caelum.com.br/wp-content/uploads/2010/03/first.png "first")](https://blog.caelum.com.br/wp-content/uploads/2010/03/first.png)

> Eigenclasses

O fluxo comentado acima não está totalmente completo. Cada objeto em Ruby pode ter sua própria classe, uma classe especial, escondida. Existe uma sintaxe especial para acessar essa classe, que é:

\[ruby\] class << obj end\[/ruby\]

Qualquer método definido dentro do código acima pertenceria apenas aquela instância. A classe acima é chamada de **Eigenclass** (ou Singleton Class). O modelo de objetos Ruby agora fica da seguinte forma:

[![](https://blog.caelum.com.br/wp-content/uploads/2010/03/second.png "second")](https://blog.caelum.com.br/wp-content/uploads/2010/03/second.png)

> Eigenclasses e Method Lookup

Seguindo o modelo de objetos Ruby, um objeto procura um método de instância na sua Classe. Caso não ache, sobe para a superclasse da classe.

\[ruby\] class Caelum def ensina

end end

class Sala < Caelum end

obj = Sala.new obj.ensina \[/ruby\]

No exemplo acima, `obj` procura o método `ensina` na classe `Sala`. Como não encontra, sobe até a superclasse que é `Caelum`. Onde entra a **Eigenclass** nisso ?

Primeiro vamos criar um método em todos os objetos que retorne a **Eigenclass** de um objeto:

\[ruby\] class Object def eigenclass class << self; self; end end end

p "caelum".class #=> String p "caelum".eigenclass #=> #Class:#String:0x18ba78 \[/ruby\]

Repare que o Ruby coloca um sinal _#_ na frente da **Eigenclass**. Usaremos esse sinal apartir de agora para identificar esse tipo de classe, sendo `#obj` a **Eigenclass** do objeto `obj`.

Agora vamos criar um método em `obj` colocando-o na sua **Eigenclass** e verificar a hierarquia:

\[ruby\] class << obj def eigen\_method 'obj#eigen\_method()' end end

p obj.class #=> Sala p obj.eigenclass #=> #obj p obj.eigenclass.superclass #=> Sala \[/ruby\]

A superclasse de uma **Eigenclass** é a própria classe do objeto. Ou seja, se um objeto possui uma **Eigenclass**, Ruby inicia a procura do método nela. Caso não encontre, sobe para a primeira superclasse (que é a própria classe do objeto).

> Eigenclasses e herança

Para completar o modelo, quem é a superclasse de uma **Eigenclass**? Vamos adicionar uma eigenclass na classe Caelum e analisar o modelo:

\[ruby\] class Caelum class << self def meu\_eigen\_method 'Caelum.meu\_eigen\_method()' end end end

p Caelum.eigenclass # => #Caelum p Sala.eigenclass # => #Sala p Sala.eigenclass.superclass # => #Caelum p Caelum.eigenclass.superclass # => #Object \[/ruby\]

[![](https://blog.caelum.com.br/wp-content/uploads/2010/03/third.png "third")](https://blog.caelum.com.br/wp-content/uploads/2010/03/third.png)

Repare que a Superclass da Eigenclass de Sala (`Sala.eigenclass.superclass`) é a eigenclass da classe Caelum! Ou seja, **A superclasse de uma eigenclasse é a eigenclass da superclasse do objeto!** Não muito trivial.

[Matz](http://pt.wikipedia.org/wiki/Yukihiro_Matsumoto), o criador do Ruby não chegou a definir um nome para esse tipo de classe, sendo que os programadores costumaram chamá-la de singleton class por conta própria. Como muita gente acabou confundindo com o design pattern de mesmo nome, outras opções começaram a surgir. **Eingenclass** surgiu na Alemanha onde **eigen** tem o significado de "próprio de algo", sendo uma tradução comum algo como "a própria classe do objeto".
