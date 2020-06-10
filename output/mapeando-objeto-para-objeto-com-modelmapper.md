---
title: "Mapeando Objeto para Objeto com ModelMapper"
date: "2014-06-09"
author: "steppat"
authorEmail: "steppat@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

É muito comum que aplicações mapeiem o modelo de negócio (domínio) para outras estrutura de dados. Por exemplo, se quisermos expor uma parte do domínio como serviço web é preciso mapear os dados para JSON ou XML. Há bibliotecas para esta tarefa como, por exemplo, [XStream](http://xstream.codehaus.org) ou [JAX-B](https://jaxb.java.net/). Com elas podemos usar anotações para configurar o mapeamento de JSON/XML. O problema é que essas configurações começam a poluir as classes de domínio e nem sempre são flexíveis o suficiente. [![mapeamento1](https://blog.caelum.com.br/wp-content/uploads/2014/04/mapeamento1.png)](https://blog.caelum.com.br/wp-content/uploads/2014/04/mapeamento1.png)

Uma forma de resolver isso é criar **classes dedicadas** que só existem para definir o modelo do serviço web. São classes que não possuem nenhuma regra de negócio. Podemos enxerga-las seguindo o [antigo padrão DTO](http://martinfowler.com/eaaCatalog/dataTransferObject.html) (_Data Transfer Object_, muitas vezes chamado de _Value Object_). São objetos que só existem para serem transferidos entre camadas físicas. As anotações de mapeamento ficariam então nessas classes e o domínio estaria livre dessas configurações. [![mapeamento2](https://blog.caelum.com.br/wp-content/uploads/2014/04/mapeamento2.png)](https://blog.caelum.com.br/wp-content/uploads/2014/04/mapeamento12png)

O problema agora é que precisamos **popular esses objetos**, já que criamos uma hierarquia paralela ao nosso domínio, algo que pode ser bastante trabalhoso. O ideal é delegar o trabalho para um framework. Com tempo sugiram [algumas opções](http://www.javacodegeeks.com/2013/10/java-object-to-object-mapper.html) que se preocupam com o **mapeamento de objeto para objeto** (_Object-to-Object Mapper_). Exemplos disso, são [Apache Dozer](http://dozer.sourceforge.net/), [Orika](http://orika-mapper.github.io/), [Automapper](http://automapper.org/) e entre vários outros.

Um framework que se destaca no mercado é o [ModelMapper](http://modelmapper.org/). Com ele é possível mapear modelos complexos, com nenhuma ou poucas configurações - sempre seguindo convenções.

[![modelmapper](https://blog.caelum.com.br/wp-content/uploads/2014/04/modelmapper2.jpg)](https://blog.caelum.com.br/wp-content/uploads/2014/04/modelmapper2.jpg)

Vamos dar um exemplo mais concreto. Parte do nosso _domain model_ poderia representar um `Pedido` que se relaciona com outras classes como `Endereco`, `Produto` e `Cliente`:

\[code language="java"\] public class Pedido { private Endereco destino; private List<Produto> produtos = new ArrayList<>();

private Cliente cliente;

//construtores e métodos omitidos } \[/code\]

A tarefa do _ModelMapper_ é mapear isso para o DTO que normalmente está mais achatado (_flat_). Veja abaixo que o `PedidoDto` possui, na sua maioria, apenas atributos simples:

\[code language="java"\] public class PedidoDto { private String ruaDestino; private String numeroDestino; private String cidadeDestino; private String cepDestino; private String cliente;

private List<ProdutoDto> produtos;

//getters e setters omitidos } \[/code\]

O uso do _ModelMapper_ é simples bastando instanciá-lo para em seguida chamar seu método `map(..)` que popula o DTO e recebe o objeto fonte e o tipo do DTO: \[code language="java"\] Pedido pedido = pegaPedido(); ModelMapper mapper = new ModelMapper(); PedidoDto dto = mapper.map(pedido, PedidoDto.class); //já foi populado \[/code\]

Repare que o _ModelMapper_ precisa chamar vários _getters_ no domínio para pegar, por exemplo, o nome da rua. Nesse exemplo ele executará algo assim:

\[code language="java"\] String rua = pedido.getDestino().getRua(); pedidoDto.setRuaDestino(rua); \[/code\]

O _ModelMapper_ é inteligente o suficiente para encontrar o caminho no domínio e chama os _getter_ desde que existam essas informações no DTO. Quando não há como deduzir o caminho correto, é preciso configurá-lo programaticamente. Por exemplo, para pegar o nome do cliente é necessário chamar:

\[code language="java"\] String nome = pedido.getCliente().getNome().getSobreNome(); peditoDto.setCliente(nome); \[/code\]

Nesse caso o _ModelMapper_ precisa de uma dica pois não tem como deduzir esta chamada. Para tal, existe o `PropertyMap` que configura programaticamente o mapeamento entre a fonte (`source()`) e o destino (`map()`) :

\[code language="java"\] mapper.addMappings(new PropertyMap<Pedido, PedidoDto>() {

@Override protected void configure() { String nome = source().getCliente().getNome().getSobreNome(); map().setCliente(nome); }} ); \[/code\]

É importante mencionar que um [Object-to-Object](http://www.javacodegeeks.com/2013/10/java-object-to-object-mapper.html) Mapper não só serve para mapear o domínio para um DTO. [Há várias outras motivações](http://dozer.sourceforge.net/documentation/whymap.html) para uma hiearquia paralela. De qualquer forma um _object-to-object mapper_ assume esse parte trabalhosa de copiar os valores entre objetos.

O código completo deste post encontra-se no [github](https://github.com/steppat/modelmapper).
