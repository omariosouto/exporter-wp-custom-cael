---
title: "Facilitando a manutenção dos testes ao diminuir o acoplamento com o código"
date: "2011-03-01"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[![](https://blog.caelum.com.br/wp-content/uploads/2011/03/acoplamento_coesao.jpg "acoplamento coesao")](http://www.flickr.com/photos/dieterlaskowski/126773205/) É fácil entender por que devemos diminuir o acoplamento entre classes: a alteração em um ponto do sistema pode propagar a necessidade de mudanças em outros. [Dependendo do acoplamento](http://www.martinfowler.com/ieeeSoftware/coupling.pdf), uma simples alteração exige um esforço enorme. Em alguns casos as mudanças não são feitas, e a funcionalidade é simplesmente descartada, devido a esse alto custo de manutenção deixado pelo acoplamento . Uma das tentativas para diminui-lo é tentar sempre _[programar voltado para interfaces e não para uma implementação](http://www.objectmentor.com/resources/articles/dip.pdf)_ e [TDD pode ser utilizado para atingir esse objetivo](https://blog.caelum.com.br/tdd-e-sua-influencia-no-acoplamento-e-coesao/).

Mas é mais difícil perceber que o acoplamento entre classes possa existir até mesmo entre o código de teste e o código de produção. Isso pode também fazer com que alterações também sejam caras.

Testes de integração, que visam verificar o comportamento de várias unidades juntas dentro de um sistema, dão um excelente feedback sobre a corretude do programa, mas geram um acoplamento muito forte entre o teste e o código. Observe o teste de integração abaixo:

\[java\] public void deveCompararOValorDaAcaoDeDuasEmpresasDiferentes() { // usando um HttpClient simples String uri = "http://servico\_falso\_da\_bovespa\_para\_testes"; HttpClient client = new HttpClient(uri);

BuscadorDePrecos buscador = new BuscadorDePrecosNaBovespa(client);

Comparador compara = new Comparador(buscador); double diferenca = compara.acoesDa("petrobras", "vale");

assertEquals(1.0, diferenca, 0.001); } \[/java\]

O teste acima verifica se a classe `Comparador` calcula a diferença entre duas ações. Para isso, ela usa a classe `BuscadorDePrecosNaBovespa` que, por sua vez, acessa um cliente qualquer de Http (aqui ilustrado como `HttpClient`) para fazer as requisições.

Esse teste entrega feedback sobre a corretude do programa; batendo contra um servidor parecido com o da Bovespa, recebendo os valores e executando as comparações devidas. Se o programador deseja alterar a implementação do `BuscadorDePrecosNaBovespa`, removendo o `HttpClient` para fazer uso de uma outra ferramenta, como o [Restfulie](http://restfulie.caelum.com.br/), além de quebrar os testes da classe `BuscadorDePrecosNaBovespa` (que já era esperado), ainda quebrariam os do `Comparador`. Uma mudança simples, que deveria acarretar em consequências apenas nos testes específicos dessa classe, afeta diferentes classes e testes desse sistema; aumentando o custo de manutenção da bateria de testes, um tradeoff que pode ou não fazer sentido em seu projeto.

Perceba que a classe `Comparador` não está interessada em como a classe `BuscadorDePrecosNaBovespa` faz seu trabalho; ela apenas espera que alguém busque esse valor. Se a classe `Comparador` fosse testada isoladamente, tudo que ela precisaria é receber "alguém" que retorne o valor de uma determinada ação. Ou seja:

\[java\] public interface BuscadorDePrecos { Acao pega(String empresa); }

public class ComparadorTest { @Test public void deveCompararOValorDaAcaoDeDuasEmpresasDiferentes() { BuscadorDePrecos buscador = mock(BuscadorDePrecos.class); when(buscador.pega("petrobras")).thenReturn(new Acao("petrobras", 101.0); when(buscador.pega("vale")).thenReturn(new Acao("vale", 100.0);

Comparador compara = new Comparador(buscador); double diferenca = compara.acoesDa("petrobras", "vale");

assertEquals(1.0, diferenca, 0.001); } } \[/java\]

A classe `Comparador`, testada isoladamente, ainda depende de um `BuscadorDePrecos`, um objeto que saiba como buscar o preço de ações. Mas agora o teste passa para ela um dublê, uma classe que finge ser a outra. O teste desconhece a existência de uma implementação concreta dessa interface.

A classe `BuscadorDePrecosNaBovespa` (que implementa essa interface) pode ser modificada, sem propagar o custo de manutenção para além da sua própria classe de testes.

[Programar para interfaces não só diminui o acoplamento](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/) entre as classes de produção, mas também entre seu código de teste e de produção. Devemos buscar sempre o baixo acoplamento, para diminuir os custos de manutenção, não importando onde ele esteja presente.

Testes de integração são fundamentais para validar a corretude do sistema, mas podem apresentar uma barreira para sua evolução. Os desenvolvedores devem pesar a abordagem de testes para garantir o máximo de qualidade interna e externa, mantendo um custo aceitável para o desenvolvimento e evolução a longo prazo.
