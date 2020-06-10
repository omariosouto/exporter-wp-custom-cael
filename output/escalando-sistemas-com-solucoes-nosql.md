---
title: "Escalando sistemas com soluções NoSQL"
date: "2010-06-07"
author: "douglas.campos"
authorEmail: "douglas.campos@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Um dos grandes desafios enfrentados no dia a dia do desenvolvedor eficaz é o de cumprir requisitos não-funcionais de uma aplicação, principalmente os relacionados a **performance** e **escalabilidade**. Uma das alternativas mais conhecidas para escalar horizontalmente é a de dividir as tarefas que não necessitam de retorno imediato ao cliente em **processos batch**. Para tanto, podemos usar diversas ferramentas, desde soluções caseiras até diversos frameworks, tanto em ruby como em java.

Quando se trata de processos batch, é comum buscar um controle mais fino sobre o resultado da execução (sucesso, falha, pendente, etc). Normalmente, usaríamos bancos relacionais para isso, tanto pela sua popularidade como pela performance. Supondo que eu precise gerenciar 1000 pequenas tarefas simultâneas, pagaríamos um preço bem alto pela concorrência, visto que o banco começaria a demorar para obter o lock, fazer o insert, refazer o lock e atualizar o status da tarefa. Muitos consideram o uso de banco de dados relacionais um erro para casos como esse. Buscando eficiência, poderíamos usar uma [solução NoSQL](https://blog.caelum.com.br/2009/10/30/bancos-de-dados-nao-relacionais-e-o-movimento-nosql/) para persistência e controle das tarefas, onde evitaríamos os locks, graças ao modelo de concorrência simplificado.

[![](https://blog.caelum.com.br/wp-content/uploads/2010/06/resque-web-e1275918412140-300x224.jpg "interface de administraçao resque-web")](https://blog.caelum.com.br/wp-content/uploads/2010/06/resque-web.jpg)

Uma opção em ruby é o **[resque](http://github.com/defunkt/resque)**, um framework de processamento batch criado pelo [github](http://github.com) que utiliza o [redis](http://redis.io/), um banco de dados não-relacional, tanto para persistir as tarefas, como para coordenar os diversos processos executores de tarefas, mais conhecidos como _workers_.

Agendar um processo é bem simples - basta que eu tenha uma classe ruby com um método `perform`, com o processamento a ser executado e um atributo de instância `@queue` define em qual fila a tarefa será colocada:

\[ruby\] # uma classe qualquer, poderia ser um model class Relatorio @queue = :relatorio\_anual

def self.perform(ano) gera\_relatorio\_anual(ano) end end \[/ruby\]

e finalmente, faço o agendamento:

\[ruby\] #método recebe a classe da tarefa, e os argumentos Resque.enqueue(Relatorio, 2009) \[/ruby\]

Essa chamada faz uso bem eficiente de metaprogramação, de maneira que um worker posteriormente executará um código semelhante a esse:

\[ruby\] classe, argumentos = Resque.reserve(:relatorio\_anual) classe.perform(\*argumentos) if class.respond\_to? :perform \[/ruby\]

posteriormente iniciamos um worker usando as tasks do rake que vem no próprio resque, indicando qual fila o worker que estamos criando vai atender

\[bash\] $ QUEUE=relatorio\_anual rake resque:work \[/bash\]

assim que o worker subir ele já irá executar `Resque.reserve`, que o colocará em espera por novos trabalhos. opcionalmente, poderíamos executar um worker para atender todas as filas:

\[bash\] $ QUEUE=\* rake resque:work \[/bash\]

O principal fator que contribui para que _resque_ e _redis_ funcionem como um relógio é o fato de ambos estarem focados em alta performance e, ao mesmo tempo, extrema simplicidade sem ser muito invasivo, já que permitem que você mantenha sua estrutura de persistência relacional inalterada.

Cada vez mais sistemas vem utilizando solucões NoSQL para algumas funcionalidades específicas, como foi o caso recente do [Large Hadron Collider com o Mongo DB](http://blog.mongodb.org/post/660037122/holy-large-hadron-collider-batman). Parece que a combinação de bancos relacionais e não-relacionais no mesmo sistema, cada um com seu propósito, tem sido a direção tomada pelo mercado.
