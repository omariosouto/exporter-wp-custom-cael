---
title: "Logar é preciso, debugar não é preciso?"
date: "2010-05-27"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Muitas vezes você percebe que está debugando o mesmo trecho de código incessantemente: horas são gastas tentando procurar o erro, checando variáveis, adicionando e removendo break points e até adicionando complexos alarmes condicionais. Ferramentas como o Netbeans e o Eclipse facilitam muito esse trabalho, mesmo quando ele é feito remotamente, o que é ainda mais complexo.

O tempo gasto com debug é relevante, e devemos tentar minimizá-lo. Não é a toa que [Brian Kernighan, considerado um dos co-autores do C, é frequentemente citado em relação a este assunto](http://logging.apache.org/log4j/1.2/manual.html), dizendo que:

"_Como escolha pessoal, nós tendemos a não usar debuggers a não ser para tirar uma stracktrace ou o valor de uma ou outra variável. A razão é que é fácil de se perder em tantos detalhes de estruturas de dados e controle de fluxo complicados. Nós achamos que ir passo a passo no programa é menos produtivo que adicionar logs e asserções em lugares estratégicos. Ficar clicando em linhas de código toma mais tempo que olhar o log. Toma menos tempo decidir onde colocar as invocações ao log que fazer o passo a passo até a seção crítica do código, mesmo assumindo que saibamos onde ela está. Mais importante, debug por logging fica no programa, já o processo de debug é transiente._".

Como já estamos bastante acostumados a usar logging, vamos aproveitar pra ver que nem sempre é tão trivial: às vezes, temos de tomar cuidado ao colocar inúmeras invocações à API de log sem muito critério. Para ilustrar o caso, vamos imaginar uma situação bastante comum: estamos querendo descobrir por que os emails que deveriam chegar pro cliente não estão sendo enviados. Considere o seguinte código que usa o [Apache Commons Mail](http://commons.apache.org/email/):

\[java\] class EnviadorEmail { public void enviaEmailDeCadastro(String to) { try { SimpleEmail email = new SimpleEmail(); email.setHostName("mail.myserver.com"); email.addTo(to, "nome do cliente"); email.send(); } catch(EmailException e) { } } } \[/java\]

O erro aqui é [a péssima prática de _engolir_ exceptions](https://blog.caelum.com.br/lidando-com-exceptions/), que costuma aparecer em diversos códigos por aí. Ao debugar rapidamente, iremos perceber que devemos corrigir esse codigo. Como? Uma sugestão seria colocar um logging aí.

Então vamos nos aventurar a usar um sistema de logging para o Java. Existem vários, sendo o mais clássico o Log4J, mas ainda há a própria [API de logging do Java](http://java.sun.com/j2se/1.4.2/docs/guide/util/logging/overview.html) (desde 1.4) e temos tambem wrappers, como o [commons logging](http://commons.apache.org/logging/) ou o [SL4J - simple logging](http://www.slf4j.org/), este último adotado pelo Hibernate. Vamos utilizar o Log4J dada a sua grande adoção. [Baixe o Log4J](http://logging.apache.org/log4j/1.2/download.html) e coloque o JAR no build path do seu projeto.

Agora, podemos criar um logger especifico para a nossa classe e, dentro do catch, mostrar uma mensagem:

\[java\] class EnviadorEmail { private static final Logger LOG = Logger.getLogger(EnviadorEmail.class); public void enviaEmailDeCadastro(String to) { try { SimpleEmail email = new SimpleEmail(); email.setHostName("mail.myserver.com"); email.addTo(to, "nome do cliente"); email.send(); log.info("E-mail enviado para " + to); } catch (EmailException e) { log.error("problemas enviando email", e); } } } \[/java\]

Ao rodar o programa, se o sistema não conseguir enviar um email, a exception sera capturada e logada. Mas não configuramos onde esse log deve aparecer, então o Log4J nos alertará sobre isso. É bastante comum esse alerta aparecer quando usamos, por exemplo, o Hibernate sem configurar o log:

\[code\] log4j:WARN No appenders could be found for logger (org.hibernate.cfg.annotations.Version). log4j:WARN Please initialize the log4j system properly. \[/code\]

Então, além de [uma configuração básica de log4j.xml](http://gist.github.com/409316) para o seu sistema, você precisa configurar para que o log do Hibernate use um appender, no caso chamado `sysout` que vai jogar pra saída padrão:

\[xml\] <category name="org.hibernate"> <priority value="info" /> <appender-ref ref="sysout" /> </category> \[/xml\]

Ou, no caso do nosso sistema, o _name_ da nossa categoria deve refletir o nome do nosso pacote. Nosso sistema agora loga corretamente a falha, mas ainda há um problema maior: quem pediu para executar a acao de enviar o email, mesmo que ele nao seja enviado, tem a impressão de que tudo ocorreu normalmente. Isto é:

\[java\] cadastra(usuario); EnviadorEmail ee = new EnviadorEmail(); ee.enviaEmailDeCadastro(usuario.getEmail()); System.out.println("sucesso no cadastro"); \[/java\]

O codigo acima executará normalmente, mesmo que haja uma falha no envio do e-mail. É isso que gostaríamos? Em alguns casos, talvez seja interessante não falhar mesmo que um e-mail não seja enviado, mas esse tipo de decisão deve ser tomada pela lógica de negócio, e não pela classe que escrevemos. Deveríamos então ter propagado a exception, que era checked. Podemos fazer isso atraves da mesma exception ou "envelopando-a" numa unchecked que você considere, tal como:

\[java\] try { SimpleEmail email = new SimpleEmail(); email.setHostName("mail.myserver.com"); email.addTo(to, "nome do cliente"); email.send(); log.info("E-mail enviado para " + to); } catch (EmailException e) { log.error("problemas enviando email", e); throw new IllegalStateException("falha ao enviar email para " + to, e); } \[/java\]

Pronto. Agora, além de falhar, vai logar pra gente o motivo da falha. Será que agora temos um logging bom que vai evitar que debuguemos nosso código? Ainda precisamos ajustar diversos detalhes. Um deles é que, nesse mesmo codigo, estamos aplicado o [log and throw antipattern](http://today.java.net/article/2006/04/04/exception-handling-antipatterns), que, no caso de existir alguém, em algum nivel acima, fazendo o try catch, vai provavelmente logar a exception, e ela será logada duas vezes. Devemos logar exceptions apenas quando não a relançamos (wrapped ou não).

Também precisaríamos colocar muito mais logging, acertar os níveis (info, error, warn, debug) e tomar cuidado até mesmo com a performance: apesar das APIs de logging serem incrivelmente rápidas, muitas vezes elas vão acabar invocando métodos seus, como o `toString`:

\[java\] log.debug("cadastrando usuario " + usuario); \[/java\]

Aqui, _usuario.toString()_ será invocado ao concatenar a mensagem, mesmo se o log não estiver definido em debug no seu arquivo de configuração, podendo gastar um tempo precioso se for invocado inúmeras vezes. É por esse motivo que as vezes aparece o seguinte idiomismo evitando chamadas desnecessárias:

\[java\] if (log.isDebugEnable()) { log.debug("cadastrando usuario " + usuario); } \[/java\]

As APIs de log mais modernas, como o SL4J, vão utilizar de varios argumentos ou de varargs, para que não tenha de cair nesse tipo de código, fazendo essas chamadas de toString apenas se aquele nível de log estiver habilitado.

Concluindo: debugar é sim preciso, mas devemos, através de boas práticas e um forte e criterioso logging, diminuir ao máximo a sua necessidade. E siga as boas práticas e faça boas logadas.
