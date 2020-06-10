---
title: "Branches e integração contínua: o problema de feature branches"
date: "2011-01-03"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Integrar o código criado pelos desenvolvedores o mais frequentemente possível, com espaços de tempo mínimos, para que o feedback e consequências do código criado por um desenvolvedor entre em contato o mais rapidamente possível com os outros é o processo chamado de integração contínua.

Mas em diversos momentos existe a tentação de criar branches, linhas separadas de desenvolvimento, para cada funcionalidade: um feature branch, em cada um deles um conjunto de desenvolvedores trabalha de maneira isolada. [O problema de desenvolver muito tempo sem integrar é que no momento que um feature branch terminar integrando-se ao mainline, os outros branches terão um processo de merge grande e custoso a ser feito](http://martinfowler.com/bliki/FeatureBranch.html).

Variações do "feature branch" são branches por cliente, onde cada um possui um conjunto distinto de funções que está disponível. Gerenciar esse trabalho é difícil e propenso a erro como por exemplo quando bugs que são corrigidos em determinados branches não são passados para outros. Para mantermos os benefícios da integração contínua, tentamos minimizar o impacto dos feature branches e encontrar uma maneira de remover o efeito negativo dos mesmos, mantendo os benefícios.

Se o branch vive menos de um dia, por exemplo, a prática de feature branches não vira uma discussão: o merge e seu respectivo impacto é pequeno, não é necessário se preocupar com o impacto dessa decisão na integração contínua pois o tempo no qual a integração não é contínua é relativamente baixo.

Por outro lado, existem dois possíveis motivos para um branch viver mais de um dia. O primeiro deles é o de criar uma história muito grande, que para ser implementada é necessário muitas horas de trabalho. A solução aqui é simples: quebrar as histórias em pedaços menores permitirá que os desenvolvedores terminem mais cedo algo entregável que possivelmente resultará em feedback para os próximos trechos a serem criados.

O segundo motivo, mais complicado, é para manter um branch master (ou trunk) limpo que pode ser deployado para produção a qualquer instante. O motivo é justo e nobre uma vez que boas práticas como integração contínua andam de mãos dadas com entrega contínua e feedback frequente mas ao invés de criarmos um bloqueio para algo melhor (a integração contínua), é mais interessante ganhar o benefício de ambos.

Durante o tempo de compilação, build do artefato de deploy ou ainda execução podemos utilizar técnicas para não registrar componentes de funcionalidades ainda não prontos. O exemplo a seguir mostra como utilizar um container simples de injeção de dependências para controlar diversos ambientes com features distintas. Todos os métodos que retornam objetos do tipo Ambiente são utilizados por convenção:

\[java\] public class Ambientes { public Ambiente desenvolvimento() { return homologacao().com(OpenIdLogin.class); }

public Ambiente homologacao() { return producao().com(ExcelExport.class); }

public Ambiente producao() { return new Ambiente(new Container()).com(ClienteController.class).e(ProjetoController.class).e(LoginController.class); } } \[/java\]

Da mesma maneira, em aplicações web, arquivos jsp templates de qualquer template engine podem ser customizados para mostrar determinadas partes de acordo com o ambiente atual:

\[xml\] <feature name="OpenId"> ... codigo html ... </feature> \[/xml\]

E em qualquer ponto do sistema é possível verificar a disponibilidade de uma feature:

\[java\] public class LoginController { private final FeatureSet features; public LoginController(FeatureSet features) { this.features = features; }

public Usuario login(Map parametros) { LoginFeature login = features.for(Login.class); return login.valida(...); } } \[/java\]

[Toggles limpos e bem elaborados como esses](http://blog.jayfields.com/2010/10/experience-report-feature-toggle-over.html) permitem total customização do seu sistema sem ser um peso tão grande no desenvolvimento. Outras opções de implementações menos rebuscadas mas tão eficientes quanto envolvem a simples leitura de arquivos de propriedades. [Ferramentas como o envie](https://github.com/caelum/envie) adicionam suporte a feature toggles em qualquer ponto de uma aplicação. O mesmo container acima funciona em Ruby da seguinte maneira:

\[ruby\] production = Envie.production.with(:oauth) development = production.derive.with(:openid)

feature(:oauth) do # codigo que utiliza oauth end

feature(:openid) do # codigo que utiliza openid end \[/ruby\]

Utilizando tais ferramentas de toggle, que são baseadas na criação de enviroments, é possível automatizar o processo de oficialização de uma feature. Dada que a feature "openid" foi aceita, um script é capaz de adiciona-la no ambiente de produção, ou uma variação que removeria as definições de chamada ao métod feature, resultando em um código mais limpo a partir do próximo commit.

Feature branches e toggles são uma alternativa extremamente popular no desenvolvimento open source e muito bem feita em alguns projetos como o kernel do linux que pode ser construído utilizando inúmeras combinações. Testes de integração não são mais capazes de garantir o comportamento através de todas as possíveis combinações mas continuam adicionando valor.

[Durante o www 2010, Ian Robinson](http://iansrobinson.com/) descreveu como o Neo4j pode ser utilizado como alternativa para propragação da configuração de nós diferentes em clusters de máquinas de determinadas aplicações de larga escala na web. A abordagem de feature toggles como esses é interessante e positiva para projetos grandes que envolvem a constante necessidade de integrar funcionalidades ainda não aprovadas por completo e serve como segunda opção durante a utilização de feature branches. Também serve como solução simples para feature toggle por grupos de clientes.

A primeira regra no instante de criar um feature branch é: evite criá-lo. A segunda regra é, integre-o ao mainline o mais rápido possível.
