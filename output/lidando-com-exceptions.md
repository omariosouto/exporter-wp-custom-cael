---
title: "Lidando com Exceptions"
date: "2006-10-07"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

**Pequena Revisão** Sabemos que as exceções em Java são classificadas em dois tipos:

- **Checked Exceptions**: seguem a regra do handle-or-declare. O desenvolvedor é obrigado a tratar (try-catch) ou relançar (throws), caso não saiba como tratar.
- **Unchecked Exceptions**: não é obrigatório tratar nem relançar, apesar de ser possível. Caso não haja try-catch adequado à exceção gerada, ela é automaticamente relançada. Geralmente são filhas de `java.lang.RuntimeException` direta ou indiretamente.

Uma das grandes dificuldades dos desenvolvedores Java é saber como lidar adequadamente com exceções. Perguntas como _"Minha exceção deve ser checked ou unchecked?"_ e _"O que escrever no meu bloco catch?"_ são extremamente comuns. Espero esclarecer um pouco estas dúvidas expondo aqui a minha maneira de agir nestes casos.

**Gerando exceções** Aconteceu algo de errado no seu sistema (atingiu um estado inconsistente) e você está louco para fazer `throw new Exception();` ?

- **Primeira regra:** coloque **no mínimo** uma mensagem explicativa na sua exceção. Quantas vezes você não ficou nervoso com o seu colega desenvolvedor, que jogou uma exceção sem mensagem _nenhuma_ e não há como ter a mínima idéia do que aconteceu? Melhorando um pouco fica: `throw new Exception("Um dos argumentos para o cálculo do desconto é Inválido");`
- **Segunda regra**: use sempre exceções específicas ao seu caso, isto é, se o problema foi no cálculo de um desconto faça algo do tipo `throw new CalculoDeDescontoException("Um dos argumentos é inválido");` e não `throw new Exception("Não há como calcular o desconto pois um dos argumentos é inválido");`. Aqui você poderia terminar com **muitas** exceções específicas demais. Como alternativa, poderia então fazer algo como `throw new LogicaDeNegocioException("Um dos argumentos para o calculo do desconto é inválido")`. Usando sempre o bom senso.

**Checked ou Unchecked?** Pare um pouco para pensar no significado de um `try-catch`:

> Uma chance de se recuperar do erro. Uma chance de tratar a exceção.

Tendo isso em mente fica mais fácil decidir entre lançar uma checked exception ou uma unchecked exception. Tente responder a seguinte pergunta:

> Quero dar a chance a quem chama meu código de tratar o possível erro?

Se a resposta for sim, _use uma checked exception_. Neste caso o chamador vai ser obrigado a fazer o `try-catch` e terá a chance de se recuperar do erro. Não é o que você tanto quis?

Caso ele não saiba como se recuperar do erro, será **obrigado** a relançar a exceção. Assim, outro lugar terá a chance de se recuperar do erro. No fim das contas, o que uma checked exception trouxe foi a **capacidade de recuperação.**

**Terceira regra:** use checked exceptions para erros recuperáveis.

E quanto a erros irrecuperáveis? Nestes casos **não** queremos dar a chance de recuperação do erro. São casos como "queimou o banco de dados", ou então `NullPointerException`, que é sempre culpa do programador (apesar do nosso orgulho dizer o contrário). Deu erro e pronto, não há o que fazer. A única saída é mostrar para o usuário do sistema que aconteceu um problema, uma mensagem do tipo:

> Desculpe, ocorreu um erro interno. Contate o administrador.

Como para unchecked exceptions não é obrigatório o _handle-or-declare_ o mais comum é que a exceção vá subindo na pilha de chamadas e seja exibida ao usuário. Aqui uma grande vantagem é que pode existir um componente especialista em lidar com errors irrecuperáveis. Ele poderia interceptar todas as ações do sistema e esperar por erros que ninguém tratou: erros irrecuperáveis. Quando um erro destes chegar, o componente poderia fazer log da ocorrência, fazer rollback da transação e formatar a mensagem adequada ao usuário (não parece muito legal jogar o `Stacktrace` na cara de um usuário, parece?)

Implementar este componente interceptador não é tão difícil atualmente:

- Ambiente WEB: recursos como _Servlet Filters_ que interceptam todas as requisições em um ambiente web ou tratamento de erro declarativo pertencente à especificação de servlets/jsp (seção _error-page_ do web.xml).
- Ambientes desktop: programação orientada a aspectos, proxies ou coisas menos avançadas como o padrão de projeto _Decorator._

**Quarta regra:** use unchecked exceptions para erros irrecuperáveis.

**Ei, a API que eu uso não segue estas regras!** Você, como bom samaritano, agora segue as boas práticas. Mas o JDBC por exemplo, não segue. Supondo que você seja obrigado a usá-lo:

`**try** {  
 PreparedStatement stmt = con.prepareStatement(query);  
 // ...  
} **catch** (SQLException e) {  
 // Epa! SQLException é erro grave, irrecuperável! Pode ter queimado o banco de dados...  
 // Não deveria ser checked exception e, além disso, é genérica demais.    // Serve para muitos casos!  
 **throw new** AcessoADadosException("Problema na criação do Statement",  e);  
 // Agora sim, encapsulei a SQLException na minha exceção específica.    // Além disso ela é RuntimeException: unchecked!  
}`

Neste caso, `AcessoADadosException` é uma unchecked exception, já que o seu erro é irrecuperável e você não quer dar a chance de ninguém se recuperar dele. Além de que, agora a sua exceção é mais específica e descreve melhor o seu problema, sua API ficou mais limpa: não vão ser mais necessários `try-catch(SQLException)` nem `throws SQLException` espalhados pelo seu sistema todo.

De fato, o caso mais comum é o de erros irrecuperáveis. Por isso essa prática de _transformar_ checked exceptions (mal empregadas) em unchecked exceptions têm se tornado comum.

**Quinta regra**: **nunca** faça:

`} **catch**(Exception e) {  
 System.out.println(e);  
}`

Este código pega **muito mais erros do que ele está realmente esperando** e o pior, **não está preparado para tratá-los**. Sempre relance erros que você não está preparado para tratar. Nunca pegue um erro e ignore-o. O rollback de uma transação pode depender da ocorrência de um erro irrecuperável que você acaba de esconder do resto do sistema!
