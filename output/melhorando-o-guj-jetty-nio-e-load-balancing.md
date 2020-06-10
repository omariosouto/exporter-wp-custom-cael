---
title: "Melhorando o GUJ: Jetty, NIO e load balancing"
date: "2008-06-27"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![GUJ2](https://blog.caelum.com.br/wp-content/uploads/2008/06/guj2.png)](https://blog.caelum.com.br/wp-content/uploads/2008/06/guj2.png "GUJ2")

Durante boa parte da vida do [GUJ.com.br](http://www.guj.com.br), na sua segunda versão (screenshot acima), o site sofreu diversas quedas e passou por muitos períodos de lentidão, mesmo depois de ter migrado para um servidor dedicado. A grande verdade é que por um bom tempo ficamos devendo a devida atenção ao deployment do GUJ. Sempre que um problema acontecia alguém simplesmente reiniciava o servidor, sem investigar as causas reais do problema com profundidade.

Dada a relação próxima que a Caelum sempre teve com o site, já que dois dos fundadores do GUJ são também os fundadores da Caelum, resolvemos assumir de vez a posição de **criadores** do GUJ. A Caelum agora é a **patrocinadora e mantenedora oficial** do GUJ. Recentemente andamos gastando algum tempo, tentando acabar de vez com estes problemas que o GUJ a tanto tempo sofre. Felizmente, a melhora já é bem perceptível!

Há algum tempo atrás, o GUJ ficava esporadicamente muito lento. Para resolver estes problemas de lentidão, o primeiro passo foi conseguir um servidor dedicado, pago pelos anúncios espalhados pelo site. Já faz tempo que o GUJ roda neste servidor dedicado e desde então a performance se tornou quase sempre aceitável.

Porém, o servidor dedicado não resolveu todos os problemas, já que `java.lang.OutOfMemoryError` sempre foi o principal problema enfrentado pelo GUJ. Sempre desconfiamos que o culpado poderia ser o código do próprio GUJ, ou até do JFórum, que deveriam conter algum [vazamento de memória](http://en.wikipedia.org/wiki/Memory_leak).

Alguns desenvolvedores da Caelum já tiveram ótimas experiências passadas com o [Jetty](http://jetty.mortbay.org), que é um excelente servidor web e servlet contêiner. Foi, inclusive, um dos servidores Java pioneiros a usar conectores [NIO (java.nio)](http://java.sun.com/javase/6/docs/api/java/nio/package-summary.html). O Jetty foi desenhado para ser embutido em outras aplicações Java e portanto é extremamente leve. Consome bem menos memória que o Tomcat, seu concorrente mais conhecido, e não deixa nada a desejar nas outras características.

O servidor dedicado do GUJ tem 2GB de memória RAM e o Tomcat estava configurado inicialmente para ter um heap máximo de 768MB (`-Xmx768M`). A primeira tentativa foi aumentar o heap máximo (`-Xmx1024M`), mas mesmo assim o temível `OutOfMemoryError` insistia em aparecer.

Resolvemos então dar uma chance ao Jetty. Já que ele consome menos memória, acreditamos que os `OutOfMemoryError` demorariam mais a aparecer. Logo ao subir, o jetty ocupa 4% da memória do servidor. O impressionante é que em duas semanas no ar, o uso total de memória não passou de 12%. Na verdade, o uso de memória estabilizou em 9% do total, porém recentemente fizemos alguns testes de carga no servidor do guj, com mais do que o dobro do número de conexões que o guj recebe hoje nos períodos de pico. Isto fez com que o uso de memória do Jetty pulasse para 12%. Uma diferença **enorme** da quantidade usada pelo Tomcat, que chegava facilmente a 80%.

[![uso de memória do Jetty](https://blog.caelum.com.br/wp-content/uploads/2008/06/top-guj.png)](https://blog.caelum.com.br/wp-content/uploads/2008/06/top-guj.png "uso de memória do Jetty")

Já temos algum tempo rodando, sem problemas de memória e com o jetty estável usando 9-12% do total da memória do servidor. Já estamos até pensando em descartar a possibilidade de existir um vazamento de memória no código do GUJ ou do JForum. Isto tornaria o Tomcat culpado pelos problemas de memória!

A possibilidade de vazamentos de memória no Tomcat (estávamos com a versão 6.0.14, sem o APR e com Linux Kernel 2.6.22) deste tamanho é um pouco assustadora. Com a base de usuários que o Tomcat tem, muito possivelmente alguém já teria pego este problema muito antes de nós. Provavelmente o problema é de alguma configuração mal feita no Tomcat do GUJ.

Fato é que o Jetty foi uma tentativa que deu certo e o problema está aparentemente resolvido. O Jetty mostrou um uso mais alto de CPU que o Tomcat, chegando a picos de 60% da capacidade total de processamento do servidor, que possui 2 processadores. Através dos testes de carga que fizemos, temos percebido que o Jetty usa bastante CPU para responder diversas requisições simultâneas. Isso não chega a ser um problema, já que os dois processadores que o servidor possui são mais do que suficientes para atender a quantidade de requisições por segundo que o GUJ recebe hoje.

Apesar de termos [algumas suspeitas](http://jira.codehaus.org/browse/JETTY-256), ainda não investigamos a razão do alto uso de CPU. Este é um ponto a ser abordado, caso o GUJ tenha problemas com disponibilidade de processamento algum dia.

Mesmo não representando um problema hoje, esta situação preocupa já que o MySQL rodando na mesma máquina também costuma usar bastante CPU. Isto pode se tornar um problema em algum dia que tenha um pouco mais de requisições por segundo do que o comum, já que o uso de CPU chegava as vezes perto do limite. Felizmente, grande parte das requisições ao servidor do GUJ são para conteúdo estático: imagens, JavaScripts, arquivos CSS, download de pdfs (dos artigos), entre outros. Todo esse conteúdo estático era servido pelo Jetty, contribuindo para o alto uso de CPU. Resolvemos então tentar um [proxy reverso](http://en.wikipedia.org/wiki/Reverse_proxy) na frente do Jetty, especificamente para servir este conteúdo estático.

![proxy reverso servindo conteúdo estático](https://blog.caelum.com.br/wp-content/uploads/2008/06/reverse_proxy.png)

Existem diversas alternativas de [proxy reverso](http://en.wikipedia.org/wiki/Reverse_proxy) e a primeira a ser considerada quase sempre é o conhecido servidor web [Apache Httpd](http://httpd.apache.org/), com a adição do [mod\_proxy](http://httpd.apache.org/docs/2.0/mod/mod_proxy.html). É uma excelente solução e existe bastante documentação para fazer tudo funcionar. No entanto, faz um tempo que eu já estava querendo testar o servidor [russo](http://nginx.net/) [Nginx](http://wiki.codemongers.com/Main), tão falado pelo pessoal da [Engine Yard](http://engineyard.com/).

A desvantagem do Nginx para o Apache Httpd é a documentação não tão extensa. Esse é um grande problema para a comunidade do Nginx, que tem se esforçado em traduzir grande parte do que está escrito em russo. Apesar disso, o Nginx é impressionante e superou todas as nossas expectativas. Além de ser extremamente rápido, consome **pouquíssimos** recursos. Cada um dos processos (1 master + 5 workers) consome na maior parte do tempo apenas **1%** de CPU e **0.2%** da memória disponível do nosso servidor. Incrível!

O conteúdo estático do GUJ agora é todo servido pelo Nginx; as requisições nem chegam ao Jetty. Além disso, o Nginx, como um bom proxy reverso, oferece diversas otimizações. Uma essencial para o GUJ é o preenchimento automático dos cabeçalhos HTTP de cache, sugerindo aos browsers que façam cache do conteúdo estático. Agora o consumo de CPU diminuiu consideravalmente.

[![uso de recursos do nginx no GUJ](https://blog.caelum.com.br/wp-content/uploads/2008/06/nginx.png)](https://blog.caelum.com.br/wp-content/uploads/2008/06/nginx.png "uso de recursos do nginx no GUJ")

Não fizemos nenhum comparativo científico de performance, mas a melhora dos tempos de resposta do GUJ está visível. Frequentemente tenho a sensação de que o site está _"voando"_.

Configuramos também no Nginx a [exibição de algumas estatísticas de acesso](http://www.nginx.eu/nginx-rrd.html); tão valiosas ao GUJ. É **assustador** como o padrão de acesso se repete a cada dia, e a cada semana. Repare como o gráfico de requisições por segundo é praticamente **idêntico** para cada dia (com exceção do sábado e domingo, que são parecidos entre si).

[![requisições por segundo no GUJ, em uma semana](https://blog.caelum.com.br/wp-content/uploads/2008/06/guj-requests-week.png)](https://blog.caelum.com.br/wp-content/uploads/2008/06/guj-requests-week.png "requisições por segundo no GUJ, em uma semana")

Temos também o interesante gráfico de conexões por segundo, que mostra relação média aproximada de **3 requisições por conexão**.

[![conexões por segundo no GUJ, em uma semana](https://blog.caelum.com.br/wp-content/uploads/2008/06/guj-connections-week.png)](https://blog.caelum.com.br/wp-content/uploads/2008/06/guj-connections-week.png "conexões por segundo no GUJ, em uma semana")

Note que a legenda do gráfico está errada, já que deveria mostrar _"connections/sec"_. O pequeno pico que dá para ver no gráfico aparece por causa de alguns testes de carga que fizemos neste dia. Pode ser desconsiderado.

Fizemos ainda algumas experiências com múltiplos servidores Jetty por trás do Nginx, funcionando também como [balanceador de carga](http://en.wikipedia.org/wiki/Load_balancing_(computing)). Espero em breve postar sobre nossa experiência em geral com balanceamento de carga e alguns outros truques que pudemos testar nesta experiência do GUJ e em alguns clientes.

Aproveitando o post, o [GUJ recentemente comemorou](http://www.guj.com.br/posts/list/94487.java) a marca de **meio milhão de mensagens**. É um orgulho poder fazer parte desta comunidade, e mais ainda por poder torná-la cada vez melhor.
