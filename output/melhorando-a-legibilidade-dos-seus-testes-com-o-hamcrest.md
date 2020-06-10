---
title: "Melhorando a legibilidade dos seus testes com o Hamcrest"
date: "2012-11-21"
author: "luiz.real"
authorEmail: "luiz.real@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Testes automatizados são uma parte importante de uma aplicação. Além de verificarem o bom funcionamento do sistema, servem como uma forma de documentação do código testado. Mas, para servirem como documentação, os testes precisam ser de fácil leitura, permitindo a compreensão da lógica testada rapidamente.

Existem diversas técnicas para tornar o código dos testes mais legível. Uma delas, que vale para código em geral, é extrair trechos complicados de códigos para métodos e classes a parte. Nos testes, podemos por exemplo extrair a criação de objetos complexos ou ainda verificações complicadas para métodos isolados. É neste último ponto que o [Hamcrest](http://hamcrest.org/) pode nos ajudar.

O Hamcrest é uma biblioteca que se integra muito bem com o JUnit e tem versões para várias linguagens. Ela nos provê formas de verificar os resultados dos nossos testes muito mais concisamente. Além disso, quando uma verificação do Hamcrest falha, a mensagem de erro detalha o problema encontrado. Imagine, por exemplo, que queremos verificar que um método que implementamos devolve uma lista ordenada de números. Com JUnit puro, poderíamos fazer assim:

\[sourcecode language="java"\] @Test public void trazListaOrdenadaComTodosOsElementos() { List<Integer> lista = new ArrayList<>(); lista.add(5); lista.add(3); lista.add(2); lista.add(7);

List<Integer> listaOrdenada = new Ordenador().ordena(lista);

Assert.assertEquals(4, listaOrdenada.size()); Assert.assertEquals(new Integer(2), listaOrdenada.get(0)); Assert.assertEquals(new Integer(3), listaOrdenada.get(1)); Assert.assertEquals(new Integer(5), listaOrdenada.get(2)); Assert.assertEquals(new Integer(7), listaOrdenada.get(3)); } \[/sourcecode\]

Veja só quantos "asserts" precisamos escrever! E, se o nosso ordenador sumir com algum elemento, olha só a mensagem de erro:

[![Resultado do JUnit para o teste sem Hamcrest](https://blog.caelum.com.br/wp-content/uploads/2012/11/ResultadoJUnitSemHamcrest-300x287.png "Resultado do JUnit para o teste sem Hamcrest")](https://blog.caelum.com.br/wp-content/uploads/2012/11/ResultadoJUnitSemHamcrest.png)

Não dá para saber qual elemento sumiu! Como vamos corrigir esse bug se não sabemos direito o que aconteceu?

Vamos reescrever esse código usando o Hamcrest. Para isso, em vez de usar o `assertEquals` ou `assertTrue`, vamos usar o método `assertThat` do JUnit. Esse método recebe dois argumentos: o resultado do teste que queremos inspecionar para ver se está correto e um _Matcher_, um objeto do Hamcrest. No nosso caso, queremos passar `listaOrdenada` como primeiro argumento. E o segundo?

O Hamcrest já provê diversos Matchers que fazem verificações das mais simples às mais complexas. Temos, por exemplo, verificações para listas, que vão nos ajudar bastante. No nosso caso, queremos verificar que a lista devolvida têm os elementos 2, 3, 5 e 7 **nessa ordem**. O Hamcrest já nos dá uma forma de verificar isso! Veja só:

\[sourcecode language="java"\] @Test public void trazListaOrdenadaComTodosOsElementos() { List<Integer> lista = new ArrayList<>(); lista.add(5); lista.add(3); lista.add(2); lista.add(7);

List<Integer> listaOrdenada = new Ordenador().ordena(lista);

Assert.assertThat(listaOrdenada, Matchers.contains(2, 3, 5, 7)); } \[/sourcecode\]

A classe `org.hamcrest.Matchers` possui uma série de métodos estáticos que criam Matchers do Hamcrest, dentre eles o método `contains`, que verifica que uma dada lista possui exatamente os elementos que passarmos como argumentos ao método. Se `listaOrdenada` tiver elementos a mais ou a menos ou se os números não estiverem na ordem certa, o teste falha. E veja só como fica a mensagem de erro quando o teste falha:

[![Resultado do JUnit para o teste com Hamcrest](https://blog.caelum.com.br/wp-content/uploads/2012/11/ResultadoJUnitComHamcrest-261x300.png "Resultado do JUnit para o teste com Hamcrest")](https://blog.caelum.com.br/wp-content/uploads/2012/11/ResultadoJUnitComHamcrest.png)

Um Matcher do Hamcrest nada mais é do que uma classe cuja função é verificar se um dado objeto tem as propriedades desejadas. Por exemplo, o método `contains` devolve um objeto que verifica se uma lista tem os elementos desejados.

Apesar de o Hamcrest já incluir diversos Matchers, nem sempre encontramos um que se adeque às nossas necessidades. Felizmente, podemos criar nosso próprio Matcher sem grandes dificuldades! Imagine, por exemplo, que queremos verificar que o primeiro elemento da lista devolvida pelo nosso `Ordenador`. Podemos fazer simplesmente:

\[sourcecode language="java"\] @Test public void trazListaOrdenadaComTodosOsElementos() { List<Integer> lista = new ArrayList<>(); lista.add(5); lista.add(3); lista.add(2); lista.add(7);

List<Integer> listaOrdenada = new Ordenador().ordena(lista); Assert.assertEquals(new Integer(2), listaOrdenada.get(0)); } \[/sourcecode\]

Porém, esse código pode dar um erro pois não verificamos que há elementos na lista antes de pegar o primeiro! Além disso, se o primeiro elemento não for 2, não saberemos onde ele foi parar na lista. Vamos, então, criar nosso próprio Matcher, que já vai verificar se a lista está preenchida e vai nos mostrar a lista inteira caso o teste falhe. Para isso, vamos estender a classe `org.hamcrest.TypeSafeMatcher` e sobrescrever alguns métodos dela.

A classe `TypeSafeMatcher` é genérica, ou seja, precisamos estendê-la passando o tipo para o qual queremos que nosso Matcher funcione. No nosso caso, o Matcher deve funcionar para uma lista de inteiros. Portanto, vamos estender `TypeSafeMatcher<List<Integer>>`:

\[sourcecode language="java"\] public class PrimeiroElementoMatcher extends TypeSafeMatcher<List<Integer>>{ public void describeTo(Description desc) { }

protected boolean matchesSafely(List<Integer> listaRecebida) { return false; } } \[/sourcecode\]

Vamos entender o que cada um desses métodos faz. O método `describeTo` é onde você vai explicar o que seu Matcher vai testar. No nosso caso, o Matcher vai verificar que recebemos "uma lista que começa com 2", então vamos escrever isso.

\[sourcecode language="java"\] @Override public void describeTo(Description desc) { desc.appendText("Uma lista começando com 2"); } \[/sourcecode\]

O método `matchesSafely` é o coração do nosso Matcher. Nele ficará o código responsável pelo teste. Veja que ele recebe como argumento a lista que queremos inspecionar. Então vamos à implementação:

\[sourcecode language="java"\] @Override protected boolean matchesSafely(List<Integer> listaRecebida) { if (listaRecebida.size() > 0) { return listaRecebida.get(0) == 2; } return false; } \[/sourcecode\]

Agora, para usar nosso Matcher recém-criado, basta passar uma instância dele no `assertThat`:

\[sourcecode language="java"\] @Test public void trazListaOrdenadaComTodosOsElementos() { List<Integer> lista = new ArrayList<>(); lista.add(5); lista.add(3); lista.add(2); lista.add(7);

List<Integer> listaOrdenada = new Ordenador().ordena(lista);

Assert.assertThat(listaOrdenada, new PrimeiroElementoMatcher()); } \[/sourcecode\]

Agora, se o teste falhar, veremos a seguinte mensagem:

[![Teste com o nosso Matcher falhando](https://blog.caelum.com.br/wp-content/uploads/2012/11/ResultadoCustomMatcher-265x300.png "Teste com o nosso Matcher falhando")](https://blog.caelum.com.br/wp-content/uploads/2012/11/ResultadoCustomMatcher.png)

Veja só! Sabemos não só que o teste falhou como sabemos o porquê!

Podemos deixar o teste ainda mais legível. Para isso, vamos criar um método para isolar a criação do nosso Matcher com um nome que faça a linha do `assertThat` parecer uma frase. Algo do tipo "verifique que a lista ordenada comeca com dois". Em código:

\[sourcecode language="java"\] @Test public void trazListaOrdenadaComTodosOsElementos() { List<Integer> lista = new ArrayList<>(); lista.add(5); lista.add(3); lista.add(2); lista.add(7);

List<Integer> listaOrdenada = new Ordenador().ordena(lista);

Assert.assertThat(listaOrdenada, comecaComDois()); }

private PrimeiroElementoMatcher comecaComDois() { return new PrimeiroElementoMatcher(); } \[/sourcecode\]

Veja como o Hamcrest ajudou na legibilidade dos nossos testes! Começamos verificando que nossa lista estava ordenada elemento por elemento. Nosso teste tinha várias linhas de código, tornando sua leitura mais difícil. Com o Hamcrest, conseguimos isolar essa verificação numa outra classe, deixando o código do teste mais conciso e focado.

No [curso de TDD](http://www.alura.com.br/cursos-online-agile/tdd), parte da formação online de [testes](http://www.alura.com.br/cursos-online-agile/mock) [automatizados](http://www.alura.com.br/cursos-online-agile/selenium), falamos mais sobre a importância dos testes e sobre essa e diversas outras técnicas para deixá-los fáceis de ler e manter.
