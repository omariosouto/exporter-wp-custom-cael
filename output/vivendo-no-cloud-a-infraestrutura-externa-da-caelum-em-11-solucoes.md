---
title: "Vivendo no cloud: a infraestrutura externa da Caelum em 11 soluções"
date: "2011-02-03"
author: "slopes"
authorEmail: "slopes@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

[Há muito tempo que falamos de cloud computing na Caelum](https://blog.caelum.com.br/o-cloud-computing-e-inevitavel/), para minimizar a necessidade de infraestrutura própria e especializada. Mas, mais que falar, a Caelum como empresa aprendeu ao longo dos últimos anos a usar serviços disponíveis na nuvem para as mais diversas tarefas. Somos clientes de diversas soluções SaaS, [PaaS](https://blog.caelum.com.br/cloud-computing-na-casa-branca-e-o-paas/) e IaaS. Muitos nos perguntam que serviços utilizamos e com que finalidade. Esse post é um guia rápido com **11 soluções de cloud computing** que usamos na Caelum.

[![](https://blog.caelum.com.br/wp-content/uploads/2011/02/dilbert_cloud.gif "dilbert_cloud")](https://blog.caelum.com.br/wp-content/uploads/2011/02/dilbert_cloud.gif)

**1 ♦ [Google AppEngine](http://appengine.google.com): rodar aplicações Java e Python na infra do Google**

O [site da Caelum](http://www.caelum.com.br/) roda na plataforma do Google desde Setembro de 2009. Hoje temos outras iniciativas por lá inclusive em outras linguagens. Já publicamos um [passo-a-passo sobre o GAE](https://blog.caelum.com.br/escrevendo-e-migrando-aplicacoes-para-o-google-app-engine/), [como usar Ruby no AppEngine](https://blog.caelum.com.br/instalando-sua-aplicacao-ruby-no-cloud-do-google-gae/), e até uma [palestra no QConSP sobre essa nossa experiência](http://www.slideshare.net/caelumdev/mitos-e-verdades-do-cloud-do-google-1-ano-de-experincias-no-appengine-sergio-lopes-qcon-sp-2010), com pontos positivos, negativos e as dificuldades que passamos dada as restrições do ambiente.

**2 ♦ [Amazon EC2](http://aws.amazon.com/ec2/): infra elástica com virtualização**

Rodamos o [TeamCity](http://www.jetbrains.com/teamcity/) e o [Hudson](http://hudson-ci.org/) para integração contínua de um projeto interno grande que executa testes em paralelo em diversas instâncias do EC2. Atualmente, estamos estudando usar a Amazon para eliminar um repositório local dos nossos pacotes `deb` customizados. Colocar nossos sites e aplicações aqui seria uma outra opção (e de facílima migração, diferente de portar uma aplicação para o GAE que exige bastante esforço), mas sendo infraestrutura como serviço, ficaríamos responsáveis por toda a configuração e manipulação de máquinas, não oferecendo a mesma facilidade do GAE.

**3 ♦ [Heroku](http://heroku.com/): PaaS para rodar aplicações Ruby**

Construído em cima do Amazon EC2, o Heroku é considerado o "AppEngine do Ruby". Rodamos uma aplicação interna de controle de turmas desenvolvida pela Caelum Rio.

**4 ♦ [GitHub](http://github.com): repositórios de código em Git**

Antigamente, tínhamos um servidor SVN interno na Caelum (e, antes, um CVS). Em 2009, começamos a migrar para Git quando conhecemos o [GitHub](http://github.com). Desde maio de 2009 somos clientes do GitHub tanto em projetos opensource com [57 repositórios públicos](http://github.com/caelum), tais como os do [VRaptor](http://github.com/caelum/vraptor) ou do [Restfulie](http://github.com/caelum/restfulie), quanto em todos os projetos internos da empresa em mais de 40 respositórios privados.

**5 ♦ [Pivotal Tracker](http://www.pivotaltracker.com): gerenciamento de backlog para projetos ágeis**

Que a Caelum é forte adepta de [Metodologias Ágeis](http://www.caelum.com.br/cursos/agile/) não é novidade. Mas para gerenciar as dezenas de projetos ágeis da empresa, precisamos de uma ferramente simples e poderosa. O Pivotal nos permite gerenciar backlogs, sprints, estimativas, gerar gráficos, simular o kanban e muito mais. O serviço se tornará pago.

**6 ♦ [Mailchimp](http://www.mailchimp.com): gerenciamento e envio de [Newsletters](http://www.caelum.com.br/newsletter)**

A Caelum envia periodicamente uma [newsletter](http://www.caelum.com.br/newsletter/) com notícias de Java, Ruby, Arquitetura e Agile para milhares de interessados que se cadastraram. Usando o [Mailchimp](http://www.mailchimp.com) desde Abril de 2009, ganhamos agilidade no envio dos e-mails, sem preocupação com bloqueio de provedores, sobrecarga da máquina, gerenciamento de unsubscribes, e-mails que voltam e ainda obter estatísticas detalhadas.

**7 ♦ [Dropbox](http://www.dropbox.com): compartilhamento e backup de arquivos**

Nos permite a sincronização de arquivos e documentos de forma simples entre as unidades da Caelum em SP, Rio e Brasília, nosso designer em Goiânia e a qualquer momento em todos os lugares. Substituiu um antigo compartilhamento de rede local Samba/Windows, inviável para controlar arquivos e documentos dos mais de 80 colaboradores nas 3 unidades.

**8 ♦ [Google Apps (GMail, Docs)](http://www.google.com/a/): suíte de aplicativos Google para seu domínio**

Todos os nossos e-mails `@caelum.com.br` estão no Gmail corporativo oferecido pelo Google Apps for Business. Usamos ainda Google Docs para diversos documentos da empresa e Google Talk para comunicação interna.

**9 ♦ [Vimeo](http://www.vimeo.com/caelumdev): publicação de vídeos**

Divulgamos palestras, screencasts e outros vídeos pela [conta oficial da Caelum no Vimeo](http://vimeo.com/caelumdev). Vários posts aqui do blog usam o Vimeo como host dos vídeos, como o recente [Screencast da migração de Rails 2 para 3](https://blog.caelum.com.br/screencast-migrando-uma-aplicacao-para-rails-3/).

**10 ♦ [SlideShare](http://www.slideshare.net/caelumdev): publicação de slides**

Embora diversos instutores usassem há bastante tempo o [SlideShare](http://slideshare.net) para compartilhar suas apresentações, a Caelum começou em setembro a divulgar em [uma conta própria e centralizada](http://www.slideshare.net/caelumdev) as palestras que dá nos [diversos eventos](https://blog.caelum.com.br/retrospectiva-caelum-2010-mais-de-2-milhoes-de-visitas/) que participa.

**11 ♦ [WPEngine](http://www.wpengine.com): hospedagem segura e escalável de Wordpress**

Mantemos o blog desde 2006, mas sempre com problemas em gerenciar a máquina, updates manuais e, pior, momentos de instabilidade em picos de acesso (em especial quando enviamos nossa [newsletter](http://www.caelum.com.br/newsletter/)). Último serviço contratado pela Caelum, o [WPEngine](http://www.wpengine.com) trouxe escalabilidade e tranquilidade para o gerencimento do [Blog da Caelum](https://blog.caelum.com.br).

## Conclusão

Usar soluções de cloud computing é, além de um bonito buzzword de marketing, essencial para a Caelum: a tranquilidade, facilidade e economia de custos que essas soluções trazem são uma grande vantagem, além de eliminar a necessidade de pequenos servidores internos.

O uptime de nosso site melhorou bastante desde o AppEngine, os problemas de infra (hd queimados, atualização de software, mudança de permissões, criação de usuários, etc) se tornaram irrelevantes desde o EC2, Dropbox e Github, a escalabilidade do nosso blog vai ser resolvida com o WPEngine, e não precisamos gastar tempo (e dinheiro) desenvolvendo soluções que Mailchimp, Pivotal e outros nos trazem.

E você, quais serviços de cloud usa em sua empresa? Que parte ainda não está no cloud que gostaria de colocar?
