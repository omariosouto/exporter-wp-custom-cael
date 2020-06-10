---
title: "Possibilidades de design no uso do seu Generic DAO"
date: "2010-07-27"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muitas vezes, quando estamos criando nosso sistema temos a tentação de criar o [GenericDAO](https://blog.caelum.com.br/brincando-com-generics-o-bizarregenericdao/) para não ter que ficar repetindo as operações _CRUD_ e listagens.

O maior problema com o `GenericDAO` é que não necessariamente todas as operações fazem sentido para uma determinada classe. Daí o que fazer se, por exemplo, não faz sentido excluir um pagamento?

\[java\] public class PagamentoDAO extends GenericDAO<Pagamento> { @Override public void excluir(Pagamento pagamento) { throw new UnsupportedOperationException(); } } \[/java\]

Não parece uma solução muito elegante, mas é um dos únicos jeitos de proibir uma operação declarada na classe mãe, e ainda assim, só funciona em tempo de execução. Esse é um dos principais motivos para muitos não gostarem de usar o `GenericDAO` e [preferirem usar composição ao invés de herança](https://blog.caelum.com.br/como-nao-aprender-orientacao-a-objetos-heranca/). Mas como fazer para não repetir o código **trivial** das operações do _CRUD_?

Um dos jeitos é usar uma outra abstração de persistência de objetos: o [Repository](http://martinfowler.com/eaaCatalog/repository.html). Com o _Repository_, temos um lugar onde podemos guardar e buscar por objetos, não importando como fazemos isso. O _DAO_ já está muito ligado com armazenamento em banco de dados, e foi criado quando as operações do BD eram muito trabalhosas (em especial no _JDBC_).

E como juntar o _Repository_ com o `GenericDAO`? O _Repository_ pode ser definido, por exemplo, como uma interface, e aí podemos fazer o seguinte: se, para um pagamento, faz sentido apenas salvar e listar, mas não excluir, então criamos a interface:

\[java\] public interface PagamentoRepository { void salva(Pagamento pagamento); Pagamento busca(Long id); List<Pagamento> lista(); } \[/java\]

E usamos o `GenericDAO` como **implementação** dessa interface:

\[java\] class PagamentoDAO extends GenericDAO<Pagamento> implements PagamentoRepository { // implementacao extra } \[/java\]

E no nosso código de domínio "**nunca**" referenciaremos o `PagamentoDAO`, apenas o `PagamentoRepository`, assim mesmo que a [implementação saiba fazer mais coisas](http://www.arquiteturajava.com.br/livro/programe-voltado-a-interface-nao-a-implementacao.pdf), a interface só expõe as operações suportadas.

Se você usa [Injeção de Dependências](http://www.arquiteturajava.com.br/livro/inversao-de-controle-cade-minha-chave-de-fenda.pdf) e algum framework que a suporta (como o [VRaptor](http://vraptor.caelum.com.br), Spring ou Java EE6), você pode deixar os _DAO_s apenas como infraestrutura, e usar os _Repositories_ como interfaces públicas da sua aplicação:

\[java\] public class PagamentoController { public PagamentoController(PagamentoRepository repository) { this.repository = repository; }

public void salva(Pagamento pagamento) { // validações e outras regras repository.salva(pagamento); } } \[/java\]

Ainda poderíamos melhorar o nome do nosso repositório para `BaseDePagamentos`, `ContasAPagar` ou ainda `Pagamentos`, evitando usar sufixos nas classes. Usando abstrações e padrões simples conseguimos evitar repetição de código sem perder a semântica e restrições das nossas classes de modelo, além de esconder detalhes de implementação.
