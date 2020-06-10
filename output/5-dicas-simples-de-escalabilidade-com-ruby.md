---
title: "5 dicas simples de escalabilidade com Ruby"
date: "2010-12-08"
author: "douglas.campos"
authorEmail: "douglas.campos@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![](https://blog.caelum.com.br/wp-content/uploads/2010/12/bannerrails-e1291794780334.jpg "rails")  
Escrever aplicações Ruby e Rails, justamente pela alta produtividade dessa plataforma, permite a criação e implantação de um novo projeto em um curto espaço de tempo. Colocar o projeto rapidamente em produção para receber feedback dos clientes faz parte da cultura ágil.

Mas e se a aplicação atingir um público maior que o esperado inicialmente? Excelente para os negócios? Mas o aumento considerável de carga será absorvido pelo seu sistema? Quais as chances de o sistema escalar a ponto de atender a nova demanda sem problemas?

Código bonito e boas práticas de desenvolvimento geralmente não são suficiente. Um ambiente mal configurado tem grandes chances de estragar tudo. E, nessas horas, um contato com administração de sistemas faz uma grande diferença! Conhecer bem a plataforma e tecnologia usada é essencial. Algumas dicas para sua aplicação rodar mais suave ao usar Ruby:

**1) Configure o Apache ou o [Nginx](https://blog.caelum.com.br/melhorando-o-guj-jetty-nio-e-load-balancing/) para servir conteúdo estático**

Por mais óbvio que pareça, grande parte dos desenvolvedores esquece desse detalhe. Normalmente a aplicação responde muito bem, e você só vai sentir esse problema quando estiver tentando manter uma aplicação no ar. Normalmente as requisições de javascripts, css, imagens, acabam por serem servidas pelo seu servidor de aplicação (thin, mongrel, passenger, etc), quando poderiam ser servidas diretamente pelo servidor web que está à frente da sua estrutura.

**2) Garanta um número mínimo de instâncias pré-carregadas**

Lembrando a época que usávamos FastCGI, é saudável manter um número [mínimo de instâncias da sua aplicação](http://modrails.com/documentation/Users%20guide%20Nginx.html#PassengerMinInstances) equivalente a quantidade de CPUs que uma máquina possui, visando diminuir o risco de sofrer o [dog-pile-effect](http://blog.plataformatec.com.br/2009/09/como-evitar-dog-pile-effect-no-rails/), que é gerado por uma sequência de solicitações a um recurso que ainda não estava pronto para ser servido. O tempo de subir mais processos da aplicação é suficiente para que o seu sistema simplesmente saia do ar.

**3) Dimensione o uso de memória da máquina**

Uma das piores situações que podem acontecer com um servidor é precisar utilizar excessivamente a memória virtual (paginação ou swap). Os sistemas operacionais modernos, como Linux e BSD, fazem uso da região de swap para conseguir melhorar a performance do acesso aos dados em disco, retirando programas inativos da memória RAM e usando-a para fazer [cache do sistema de arquivos](http://tldp.org/LDP/tlk/fs/filesystem.html). Em contrapartida, quando falta memória RAM, o que era pra otimizar o sistema acaba por ser uma grande tragédia; sistemas que estão paginando por necessidade acabam por ser muito mais lentos (afinal, acesso a disco é muito mais lento do que acesso à memória RAM), e a grande e esmagadora parte de processos de aplicações web são focada em IO (IO-bound), o que faz com que uma aplicação que precisa de disco fique esperando o constante trabalho do sistema em reorganizar a memória para acesso.

Em linhas gerais, não existe um número mágico para quantidades e consumo de memória; você deve efetuar testes para poder ter uma estimativa do comportamento do seu sistema sob carga constante, em situações de stress, ou em condições de baixo volume de acessos. Uma ferramenta excelente para este tipo de teste é o [JMeter](http://jakarta.apache.org/jmeter/).

**4) Utilize um balanceador de carga, mesmo em uma máquina só**

Essa é uma dica que literalmente salva vidas durante um aumento inesperado de acessos. Ao invés de colocar o sistema atendendo diretamente as requisições, faça uso de um bom balanceador de carga, como o [HAProxy](http://haproxy.1wt.eu/), que possui qualidades fantásticas no quesito escalabilidade e estabilidade. No caso do HAProxy, ele permite adicionar mais servidores sem sequer derrubar as conexões em atividade.

**5) Não confie no caminho otimista**

A melhor forma de ver como um sistema se comportará em produção, é ir desligando os pedaços e ver se o sistema se recupera de uma falha. Desligue o banco de dados: quando ele retorna, o seu sistema se recupera? Tente outras situações: mate um processo, reinicie o servidor web, e veja se o seu sistema consegue se recuperar sozinho de situações inesperadas. Um bom ajuste de timeouts pode fazer toda a diferença durante uma falha inesperada de serviços.

* * *

No [curso RR-75](http://www.caelum.com.br/curso/rr-75-ruby-rails-avancado/), tratamos ainda de outros assuntos de escalabilidade, bem como performance e recursos avançados de Ruby e Rails. No fim, escalar aplicações não é uma tarefa impossível. Pequenos ajustes e algumas configurações são capazes de multiplicar a escalabilidade de suas aplicações. Mas são atitudes importantes para não sofrer indisponibilidade no pior momento, bem quando seu sistema começa a receber mais atenção e usuários.
