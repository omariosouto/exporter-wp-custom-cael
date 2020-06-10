---
title: "O novo desafio dos servidores web"
date: "2006-08-30"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

A evolução dos servidores web é um assunto bem interessante. Toda essa evolução até hoje aconteceu para atingir um aumento na vazão (throughput). Ou seja, conseguir atender mais requisições por segundo.

Algumas das primeiras idéias para geração de conteúdo dinâmico vieram com o CGI (Common Gateway Interface), que define a interface de comunicação entre o servidor web e o processo que gera o conteúdo dinâmico (escrito em PHP, ASP, perl ou C/C++, por exemplo). O grande problema do CGI é justamente a necessidade de criação de um novo processo a cada nova conexão; o que não é nada leve.

Foi aí então que vieram as Servlets. A grande diferença é que agora não é criado um novo processo a cada conexão, e sim uma **nova thread**. Este conceito é conhecido como _thread per connection pattern_ e resolveu bem os problemas, trazendo um aumento significativo na vazão dos servidores web que podem agora atender um número bem maior de conexões/requisições.

Existe hoje um novo desafio, que veio com o uso intensivo de requisições assíncronas nos sistemas web (ou se preferir, AJAX). Isto fez o volume de requisições aumentar **muito**, fazendo com que muitas conexões sejam abertas e existam muitas threads executando no servidor. O novo problema agora é o grande número de threads, que limitam a vazão do servidor. Iniciar uma nova thread é muito mais leve do que iniciar um novo processo, mas mesmo assim, o servidor gasta recursos para manter uma thread executando.

Uma solução seria manter as conexões http (cada uma associada a uma thread no servidor) abertas por mais tempo. Assim o cliente pode aproveitar sempre a mesma conexão para enviar diversas requisições assíncronas e o servidor usa sempre a thread associada a esta conexão para tratar as requisições. Isto pouparia o servidor de abrir e fechar muitas threads e possivelmente diminuiria o número de threads em execução, mas traz um outro problema: existem diversas threads no servidor que ficam a maior parte do tempo ociosas, enquanto não há requisições na conexão associada, desperdiçando recursos.

E então os servidores web começam a evoluir denovo. O uso do java.nio (New IO) aqui é a chave para o sucesso. Através da IO não bloqueante que esta API fornece, os novos servidores conseguem reaproveitar threads ociosas. Não é mais necessário associar cada thread a uma conexão: a diferença é que a thread só é associada a uma conexão, quando esta tem uma requisição pendente (a ser tratada). É assim que surgiu o novo conceito de _thread per request pattern_. Cada thread serve para atender uma requisição e quando termina, volta a estar disponível para atender outras requisições. Não importa se a conexão foi fechada ou não.

Um exemplo interessante de implementação (acompanhado de um comparativo de performance) pode ser visto [neste artigo no developerWorks da IBM](http://www.ibm.com/developerworks/library/j-nioserver/).

Já existem alguns servidores que evoluíram, como por exemplo o [Jetty](http://jetty.mortbay.org), os servidores de aplicação [Glassfish](http://glassfish.java.net/) e o BEA WebLogic. O pessoal do Tomcat também está preparando [um connector baseado em java.nio](http://svn.apache.org/repos/asf/tomcat/tc6.0.x/trunk/java/org/apache/tomcat/util/net/) para o Tomcat 6.0.

Já tem até bastante gente [querendo que a especificação de Servlets evolua para se beneficar de java.nio](http://blogs.webtide.com/gregw/2006/05/18/1147978560000.html)! É um assunto polêmico. Não são todas as aplicações que precisam de uma vazão tão grande que justifique a técnica apresentada.

_ps.: Sim, o antigo problema do CGI hoje é super atenuado com o uso de FastCGI._
