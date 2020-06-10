---
title: "Criando um WebService com a JSR 181"
date: "2006-08-17"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

O Paulo colocou um capítulo de webservices no [curso de EJB](http://www.caelum.com.br/curso/fj-31-java-ee-web-services/), com o intuito de aumentar a abrangência do curso em relação as novidades do Java EE 5 e aos poucos diminuir a pesada carga da 1.4, conforme o mercado assimila mais a nova versão.

Fui ver então como ele podia inserir um tema que tem tantos detalhes e conceitos em um curso que já era pesado. Ele está usando a [JSR 181](http://jcp.org/en/jsr/detail?id=181), que define anotações para a criação de um webservice para qualquer bean que você deseje deixar acessível. Fiquei muito impressionado com a simplicidade. Vamos direto ao código: \[java\] @WebService public class AgenteDeReservaBean { @WebMethod public boolean reserva(@WebParam(name = "nome") String nome, @WebParam(name = "voo") String voo) {

// acesso ao EntityManager injetado // logica de negocios, ou delegacao para o BO return false } } \[/java\]

`@WebParam` é opcional, mas como o bytecode do java 5 não retém os nomes dos parâmetros (em outras palavras, por reflection você não obtém essa informação), o WSDL gerado iria gerar nomes automáticos para os parâmetros (`arg0`, `arg1`, etc).

Como ativar esse webservice? XMLs gigantes de configuração? Não! Basta um container que já faz tudo isso para você.

Existem algumas opções de implementações da JSR-181. O pessoal do [GUJ](http://www.guj.com.br/) gosta do [XFire](http://xfire.codehaus.org/), mas como ele não é um container Java EE 5, você precisa fazer algumas configurações (tais como o web.xml e um outro xml dele próprio) para realizar o deploy.

O [JBossWS](http://labs.jboss.com/portal/jbossws) é uma opção extremamente simples: anote sua classe com `@Stateless`, instale o JBoss 4.0.x com suporte a ejb3, ligue-o, gere o `viagens.jar` com essa única classe, deploy, e acesse:

`http://localhost:8080/viagens/AgenteDeReserva?wsdl`

Seu webservice já está respondendo nesse memo endereço (sem o `?wsdl` no final, claro). Um monte de defaults foram usados (nome do serviço, das operações, do resultado, complex types, etc), você poderia configurar tudo isso através das anotações.

Você ainda pode gerar a interface do endpoint necessária através do `wstools` que acompanha o jboss. Uma maneira extramamente mais simples é já criar um endpoint, definindo essa interface como `@WebService`, e no seu bean você pode indicar que essa interface é o seu endpoint, evitando assim a geração de código java.

É, e você pensava que o [Apache Axis](http://ws.apache.org/axis/) te ajudava bastante para criar um webservice...
