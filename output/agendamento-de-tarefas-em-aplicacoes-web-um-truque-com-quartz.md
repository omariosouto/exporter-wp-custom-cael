---
title: "Agendamento de tarefas em aplicações web: um truque com Quartz"
date: "2010-11-25"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![quartz](https://blog.caelum.com.br/wp-content/uploads/2010/11/quartz.jpg "quartz") O [Quartz](http://www.quartz-scheduler.org/) é a biblioteca mais utilizada para agendar tarefas Java. Simples e muito superior ao mecanismo de Timer do Java EE. Através de sua API, podemos facilmente criar um Job, como um que vai enviar email lembrando a necessidade de mudança de senha, por exemplo:

\[java\] public class EnviaEmail implements Job { public void execute(JobExecutionContext context) { System.out.println("enviando email para evisar mudanca de senha..."); // acessar api de e-mail aqui } } \[/java\]

Se quisermos que esse procedimento seja executado uma vez por dia, à meia noite, utilizamos a API do Quartz:

\[java\] SchedulerFactory sf = new StdSchedulerFactory(); Scheduler sched = sf.getScheduler();

JobDetail job = new JobDetail("dispara\_email", "grupo", EnviaEmail.class); Trigger aMeiaNoite = TriggerUtils.makeDailyTrigger(0, 0); sched.scheduleJob(job, aMeiaNoite); sched.start(); \[/java\]

Poderíamos também ter usado [expressões no formato do cron](http://en.wikipedia.org/wiki/CRON_expression), usando o `CronTrigger`, como por exemplo acionar o Job a cada 20 segundos:

\[java\] CronTrigger trigger = new CronTrigger("20\_segundos", "grupo", "0/20 \* \* \* \* ?"); \[/java\]

A API do Quartz vai muito além desses agendamentos triviais, mas vamos focar nos problemas de criar tarefas dessa maneira. É comum esse código de agendamento inicial aparecer em um ponto de inicialização do contexto da sua aplicação web, como no `init()` de uma servlet, ou em um ContextListener ou em algo equivalente do seu framework.

A classe `EnviaEmail` será então instanciada a meia noite, e terá seu método `execute(JobExecutionContext context)` invocado. Se o seu `EnviaEmail` precisa abrir conexão com o banco, ou uma `session` do Hibernate, conversar com o SMTP ou qualquer outro recurso, terá de fazer dentro desse método. Há também a opção de se passar essas dependências para o `Job` através de um mapa, que pode ser populado pelo `jobDetail.getJobDataMap().put(chave, valor)` e depois recuperado dentro do `Job` através de `context.getJobDetail().getJobDataMap()`.

Se estamos trabalhando com injeção de dependências, passar as referências a factories e outros objetos para esse mapa-contexto quebra a inversão de controle. Pior: seu código possuirá partes que trabalham perfeitamente com injeção, e outras partes farão acesso às dependências através de lookups, factories e singletons, diminuindo flexibilidade e testabilidade.

Para resolver essa situação e tratar todo o nosso sistema de maneira uniforme, isto é, com injeção de dependências, faremos com que nosso Job seja apenas responsável por acessar uma URL. Essa URL então executará o trabalho em si, como enviar os emails lembrando a necessidade de mudar a senha de tempos em tempos. Com isso, nosso Job não se preocupará em receber dependências: ele é apenas uma chamada usando, por exemplo, do [apache httpclient](http://hc.apache.org/httpclient-3.x/). A sua própria aplicação web tratará essa requisição e executará a action correspondente. Como o Job fará essa requisição de tanto em tanto tempo, o sistema funciona da mesma forma que anteriormente.

Há algumas preocupações com essa abordagem. Uma é que qualquer pessoa pode acessar a URL (caso descoberta) que inicia a task. Isso pode ser evitado utilizando filtros/interceptadores para aceitar apenas requisições de determinado host ou com determinado role para as URLs de serviços agendados.

Mesmo com essa barreira, é importante que sua tarefa seja idempotente: uma segunda requisição para a mesma URL com os mesmos parâmetros e contexto não deve alterar o resultado. Isso protege mais ainda o sistema para uma eventual falha do Scheduler ou de segurança.

Há também outras alternativas ao próprio Quartz para agendamento de tarefa. Uma delas é [subverter o Hudson, utilizando-o não apenas como servidor de integração contínua, mas também para gerenciar suas tarefas](http://dev.bizo.com/2009/11/using-hudson-to-manage-crons.html), aproveitando suas características, gerenciamento de log das falhas e UI. Ou até mesmo usar o cron diretamente disparando requisições através de `curl` ou similar. No Google App Engine, por exemplo, há um [serviço de cron próprio](http://code.google.com/appengine/docs/java/config/cron.html) que funciona de maneira similar, disparando requests para determinada URL no momento agendado.
