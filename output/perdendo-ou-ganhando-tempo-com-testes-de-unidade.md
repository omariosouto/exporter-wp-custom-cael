---
title: "Perdendo ou ganhando tempo com testes de unidade"
date: "2010-12-10"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[Durante as aulas e palestras sobre TDD e testes de software](http://www.caelum.com.br/curso/pm-87-agile-praticas-ageis/) é bem comum ouvir perguntas relativas a "o que deve ser testado e o que não precisa ser testado". Geralmente os exemplos inicials que encontramos na literatura sobre TDD são muito simplistas, nos levando a crer que devemos testar todo e qualquer método de uma classe de maneira isolada, mesmo que não faça muito sentido.

Um exemplo desse tipo de abordagem é a tentativa de se testar uma constante, entre outros clássicos como a presença de uma anotação, métodos de delegação, getters e setters, etc. Supondo uma classe que armazena constantes de cartão de crédito e um possível teste de unidade para ela:

\[java\] public class CartoesDeCreditoTest { @Test public void deveRelacionarVisaComOValor123() { assertEquals(123, CartoesDeCredito.VISA); } }

public class CartoesDeCredito { public final static int VISA = 123; public final static int MASTERCARD = 456; } \[/java\]

Repare que existe uma duplicação de dados entre o código de teste e o código de produção: o número _123_. Quando o valor dessa constante mudar, você possui dois pontos de código para alterar. Ao fazer uma refatoração para remover essa duplicação, teremos um resultado como a seguir:

\[java\] public class CartoesDeCreditoTest { @Test public void deveRelacionarVisaComOValor123() { assertEquals(CartoesDeCredito.VISA, CartoesDeCredito.VISA); } } \[/java\]

Muitas vezes, o programador, acostumado a escrever testes de unidade para todo o código, acaba desenvolvendo testes sem utilidade: código que na realidade não testa nada. Naturalmente, o desenvolvedor não faria a refatoração acima, tal exemplo demonstra como um teste do genêro não traz valor, somente complexidade para nosso sistema.

Ou seja, em casos como o mostrado acima não há utilidade em escrever um teste de unidade para constantes. Ao fazer isso, o programador gera [duplicação de dados entre a classe de teste e a de produção](http://www.amazon.com/Test-Driven-Development-Kent-Beck/dp/0321146530/ref=sr_1_1?ie=UTF8&qid=1290727199&sr=8-1).

É importante testar constantes, mas no caso acima por exemplo, **não através de testes de unidade**: se ela representa um valor de um outro sistema, por exemplo, escreva um teste de integração.

No caso do cartão de crédito, suponha que a constante 123 seja o código do pagamento via cartão VISA no sistema do PayPal, portanto testamos a integração entre a aplicação e o sistema externo. Verifique se, ao passar o valor 123, o pagamento gerado foi um pagamento VISA.

\[java\] @Test public void deveEnviarCartaoVisaParaOPayPal() { Cobranca resultado = payPal.efetuaCobranca(valor, numeroDeUmCartaoVisa, CartoesDeCredito.VISA);

assert(that(resultado.getStatus()).is(PayPal.APROVADO)); } \[/java\]

Em um exemplo mais elaborado, imagine agora o seguinte trecho de código abaixo que usa o Active Record do Rails para fazer um filtro:

\[ruby\] def filtro(name) where("nome like %#{name}%") end

def test\_verifica\_se\_where\_foi\_invocado componente.should\_receive(:where).with("nome like 'guilherme'") componente.filtro("guilherme") end

\# outra variação do mesmo teste:

def test\_verifica\_se\_where\_foi\_invocado query = componente.filtro("guilherme") query\[0\].should == "nome like 'guilherme'" end \[/ruby\]

Neste caso, uma vez que o método possui um corpo simples, uma invocação de uma função do Active Record, o teste abusa de _mocks_ e é apenas uma mímica daquilo que o método executou. **Ele garante somente que o programador digitou aquilo que ele esperava digitar.** Testes como esse só indicam um problema no design do teste ou um problema no design do código.

No mundo Java é comum ver similares quando encontramos a utilização de Mocks ao extremo:

\[java\] public void adiciona(Produto p) { this.session.save(p); }

@Test public void testSalvarEhInvocado() { Produto produto = new Produt(); one(session).save(produto); dao.adiciona(produto); } \[/java\]

Veja que o próprio nome do método diz que ele não serve pra nada: _verifica se o salvar é invocado_. Você quer testar o comportamento que essa classe terá sobre seu sistema e não se um determinado método foi invocado. A obviedade que o método está sendo invocado é trivial de ser notada e por isso mesmo não precisaria ser nosso foco no teste.

Mas alguns podem dizer: _mas não preciso testar o Active Record, eu sei que ele funciona_. Ou ainda, _não preciso testar o Hibernate, eu sei que ele funciona_. Sim, você não precisa testar o Active Record unitariamente pois ele já foi testado, **mas existe a necessidade de testar a integração do seu código com o Active Record.** No exemplo de uma query, sabemos que escrevemos uma query como desejávamos, mas ela está retornando aquilo que desejamos? E deixando de lado o resto? São essas perguntas que nosso teste precisa responder.

\[ruby\] def test\_deve\_retornar\_pessoas\_que\_tenham\_parte\_do\_nome lista = executa\_metodo\_where("joão");

lista.should\_contain o\_cidadao("João Gilberto") end \[/ruby\]

O mesmo vale para queries em Java que utiliza JPA/Hibernate.

**Para saber mais sobre teste e design:** Kent Beck já discutia acoplamento tanto de dados quanto de código entre classes de teste e classes de produção. Isso é muitas vezes ignorado pelo programador. Imagine que você precise alterar muitos testes toda vez que fizer uma pequena alteração: é um indício de um [design rígido](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod), onde uma simples alteração propaga uma série de outras alterações.

> Como regra geral, que possui suas excecões, não existe valor em um teste que é uma mímica daquilo que foi **digitado** e não de seus efeitos e retornos. Nesse caso, pense duas vezes antes de escrever esse teste e verifique se testar aquela classe de outra maneira (através de um teste de integração, por exemplo) não seria mais útil.
