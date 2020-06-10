---
title: "Segurança: detectando bibliotecas vulneráveis com Snyk"
date: "2018-09-26"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Hoje em dia desenvolver uma aplicação do zero é uma tarefa bem complicada. Além de se preocupar com as regras de negócio da aplicação em si, precisamos nos preocupar com coisas mais "**técnicas**", tais como: banco de dados, transações, protocolos, servidores, configurações, etc.

É bem provável que a aplicação precise de algumas funcionalidades comuns, como envio de e-mails, geração de relatórios e gráficos e integração com serviços externos.

Já discutimos [aqui no blog](https://blog.caelum.com.br/voce-nao-e-pago-para-programar/) anteriormente que desenvolvedores de software não são pagos para programar, mas sim para gerar "dinheiro" para a organização, seja por meio da criação de novos produtos que vão gerar novas receitas ou pela automatização de processos que vai simplificar o trabalho e com isso reduzir custos. Ou seja, o software é um **meio** para se resolver problemas de negócio.

E isso é algo que vemos, em parte, acontecer naturalmente no processo de desenvolvimento de um software. Por exemplo, quando precisamos acessar um banco de dados em uma aplicação, não é comum que tenhamos que implementar manualmente o seu protocolo de comunicação específico. Geralmente utilizamos alguma biblioteca que já faz esse trabalho para nós, abstraindo assim toda a sua complexidade, e nos poupando tempo, dinheiro e algumas dores de cabeça.

O mesmo é válido para as diversas outras necessidades que eu havia citado, como o envio de e-mails e a geração de relatórios. Existem diversas bibliotecas prontas, nas mais variadas linguagens de programação, que já implementam e abstraem tais necessidades.

Sendo assim, é comum utilizarmos dezenas de bibliotecas nas aplicações que desenvolvemos. Algumas delas são pagas, mas a grande maioria é gratuita e muitas delas inclusive são open source, permitindo que qualquer pessoa possa contribuir com ajustes e melhorias.

Mas uma preocupação que às vezes não nos atentamos ao utilizar tais bibliotecas é em relação à segurança. Será que essas bibliotecas são seguras? Será que elas possuem vulnerabilidades conhecidas?

Segurança em aplicações é um assunto bastante discutido hoje em dia. Existem movimentos como o [DevSecOps](http://www.devsecops.org), que visam em construir o mindset de que segurança não deveria ser uma área separada, mas sim estar presente na organização como um todo, onde **todos são responsáveis por ela**.

Muitos desenvolvedores de software se preocupam bastante com segurança em suas aplicações. Isso é bastante visível quando vemos algumas práticas e técnicas sendo utilizadas em suas aplicações, tais como:

- Controle de autenticação e autorização
- Uso de protocolos seguros, como o HTTPS
- Não utilização de algoritmos de hashing inseguros, como o MD5 e o SHA2
- Não armazenamento de senhas em plain-text no banco de dados
- Proteção contra os ataques mais comuns, como SQL Injection, XSS e CSRF

Mas todas essas práticas e técnicas estão focadas em proteger a aplicação em si, ou seja, garantir que o código que escrevemos está seguro.

Mas quando utilizamos bibliotecas de terceiros em nossa aplicação, não há como garantir que quem as desenvolveu também teve essas mesmas preocupações. Se houverem vulnerabilidades graves em tais bibliotecas, todo esse trabalho em proteger nossa aplicação pode ir por água abaixo.

Uma prática importante de segurança a ser seguida é a de sempre manter as bibliotecas que utilizamos em nossas aplicações atualizadas, pois as novas versões costumam corrigir vulnerabilidades de segurança.

Outra prática de segurança muito importante é a de sempre verificar se não existem vulnerabilidades conhecidas nas bibliotecas que estamos utilizando em nossas aplicações. Inclusive, utilizar componentes com vulnerabilidades conhecidas é listada pelo [OWASP](https://www.owasp.org/) como uma das [dez vulnerabilidades](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) mais comuns em aplicações Web.

Mas aí vem uma questão: Como fazer isso?

Você pode fazer esse processo de verificação manualmente, por exemplo, acessando o site do [Common Vulnerabilities and Exposures](https://cve.mitre.org) e pesquisando por vulnerabilidades conhecidas nas bibliotecas que suas aplicações estiverem utilizando.

Uma outra maneira, mais eficiente, é utilizar alguma ferramenta que faz esse processo de maneira automatizada. Uma dessas ferramentas é o [Snyk](https://snyk.io).

![](https://blog.caelum.com.br/wp-content/uploads/2018/09/Screen-Shot-2018-09-21-at-16.55.11.png)

 

Snyk é uma ferramenta que automatiza o processo de detecção e correção de vulnerabilidades nas dependências de uma aplicação. Inicialmente a ferramenta tinha como público alvo aplicações Node.js, fazendo verificações nas bibliotecas disponibilizadas via [NPM](https://www.npmjs.com/), mas hoje ela já suporta diversas outras linguagens, como Java, PHP, Ruby, Python, dentre outras.

Snyk também pode ser integrado ao build do projeto, podendo ser configurado para interromper o processo caso identifique que alguma biblioteca utilizada pela aplicação possui vulnerabilidades.

Há também a possibilidade de integração com ferramentas de CI/CD, como Jenkins, Team City, Travis, etc., além de também se integrar com serviços de repositórios de projeto, como GitHub, Bitbucket e GitLab.

Para utilizar o Snyk é necessário criar uma conta e isso pode ser feito nessa [página](https://app.snyk.io/signup). O Snyk possui alguns planos que são pagos, porém existe o plano gratuito no qual está inclusa a integração com o GitHub e também o monitoramento e alertas contínuos em seus projetos.

## Monitoramento de projetos

Após criar sua conta e efetuar o login, é possível adicionar seus projetos que estão hospedados no GitHub/GitLab/Bitbucket à sua conta do Snyk, para que ele possa fazer o monitoramento quanto à existência de dependências com vulnerabilidades.

Na tela de Dashboard do Snyk serão listados os projetos que possuem vulnerabilidades e quantas cada um possui.

![](https://blog.caelum.com.br/wp-content/uploads/2018/09/Screen-Shot-2018-09-21-at-18.20.34.png)

 

Toda vulnerabilidade encontrada pelo Snyk é classificada em um nível de severidade, que pode ser Low, Medium ou High. Essa classificação leva em consideração o impacto da vulnerabilidade e também sua facilidade de exploração.

Ao detalhar um projeto que contenha vulnerabilidades, o Snyk mostra os detalhes de cada uma delas e também como fazer para corrigi-las.

![](https://blog.caelum.com.br/wp-content/uploads/2018/09/Screen-Shot-2018-09-21-at-18.30.57.png)

 

Uma coisa bacana do Snyk é que ele possui uma funcionalidade que pode criar um Pull Request no projeto, para corrigir as vulnerabilidades. Porém, nem todas as vulnerabilidades ele conseguirá criar o Pull Request de correção =/

![](https://blog.caelum.com.br/wp-content/uploads/2018/09/Screen-Shot-2018-09-21-at-18.34.52.png)

## **Integrando o Snyk ao build de uma aplicação Java**

Outra funcionalidade bacana do Snyk é sua integração ao processo de build de uma aplicação. No caso de aplicações Java, é comum a utilização de ferramentas de build e gerenciamento de dependências, como o Maven ou o Gradle, e o Snyk possui plugins que se integram com tais ferramentas.

No caso de aplicações que utilizam o Maven, basta adicionar o plugin do Snyk ao arquivo pom.xml do projeto.

\[code language="xml"\] <plugin> <groupId>io.snyk</groupId> <artifactId>snyk-maven-plugin</artifactId> <version>1.1.1</version> <executions> <execution> <id>snyk-test</id> <phase>test</phase> <goals> <goal>test</goal> </goals> </execution> <execution> <id>snyk-monitor</id> <phase>install</phase> <goals> <goal>monitor</goal> </goals> </execution> </executions> <configuration> <apiToken>${SNYK\_API\_TOKEN}</apiToken> <failOnSeverity>medium</failOnSeverity> <org></org> </configuration> </plugin> \[/code\]

Será necessário informar o seu API TOKEN, que pode ser obtido na tela de configurações da sua conta do Snyk.

![](https://blog.caelum.com.br/wp-content/uploads/2018/09/Screen-Shot-2018-09-21-at-18.44.59.png)

 

Agora, ao executar o build do projeto, o Snyk vai verificar suas dependências, buscando por vulnerabilidades conhecidas e poderá interromper o processo de build, dependendo de como você configurou o plugin.

![](https://blog.caelum.com.br/wp-content/uploads/2018/09/Screen-Shot-2018-09-21-at-18.47.56.png)

 

Essas são apenas algumas das principais funcionalidades do Snyk. Vale a pena dar uma conferida em todos os recursos que essa ferramenta incrível possui, visando em melhorar a segurança de suas aplicações.

E você, se preocupa com vulnerabilidades nas dependências de seus projetos? Utiliza o Snyk ou outra ferramenta similar? Conte-nos sua experiência! :)
