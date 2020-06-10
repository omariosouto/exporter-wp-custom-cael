---
title: "Você acredita no seu código? Testes, build e integração contínua"
date: "2006-09-08"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Como você faz para saber que seu software funcionou?

É impressionante perceber que estamos quase na primavera de 2006 e é tão fácil encontrar aplicações e bibliotecas sem uma única linha de código de teste.

Os problemas que surgem são os clássicos da época do cartão perfurado:

- **alterou uma coisa aqui**, resultou em um **bug ali**
- o responsável vai lançar um update novo do projeto mas **não faz idéia** se o que foi alterado **está funcionando** ou não
- o "testador" verifica manualmente aquilo que foi alterado, mas acaba **esquecendo** de testar alguns itens
- um bug gravíssimo foi encontrado portanto o gerente lança o projeto no meio de uma modificação. Resultado: código que não foi testado está em produção
- conversa entre o usuário final e o desenvolvedor: usuário: **Isso aqui não está funcionando**, programador: **na minha máquina funcionou**

A última frase, por mais natural que pareça, é uma das coisas mais estranhas que existem na área. O programador foi contratado para o sistema funcionar em determinada arquitetura/so/etc, portanto enquanto o sistema não funcionar na máquina do seu cliente, não faz sentido dizer que ela funciona.

Hoje em dia trabalhamos com o desenvolvimento voltados a testes - quase todos os tipos deles - automatizados. Tanto na Caelum, quanto nos projetos open source que participamos. No [XStream](http://xstream.codehaus.org/), por exemplo, nada pode ser comitado sem testes, e se for um novo recurso você também precisa comitar um pequeno tutorial, para que nada passe como "feature avançada não documentada".

Isto é, usando ferramentas como o [JUnit](http://www.junit.org), [Cargo](http://cargo.codehaus.org), [FIT](http://fit.c2.com/) e [Fitnesse](http://www.fitnesse.org) além do controlador [VRaptor](http://www.vraptor.org), o servidor [Jetty](http://www.mortbay.org), a biblioteca [Hibernate](http://www.hibernate.org) e de bancos como o [HSQLDB](http://www.hsqldb.org) e o [Derby](http://db.apache.org/derby), é possível criar diversos ambientes de teste, produzindo os seguintes resultados:

**Integração contínua**

- Suas alterações são feitas e você efetua o **commit** para o controle de versão (cvs, subversion etc)
- O sistema de integração contínua percebe o commit e, **cerca de 1 minuto depois**, efetua o processo de **build**
- Ferramentas: BeetleJuice, [CruiseControl](http://cruisecontrol.sourceforge.net) ou até mesmo o [TeamCity](http://www.jetbrains.com/teamcity/)

**Processo de build**

1. Baixa a última versão do controle de versão **(checkout**)
2. Compila o código fonte
3. Executa os testes unitários, verificando que suas classes **funcionam sozinhas** como deveriam, usando JUnit
4. Levanta um servidor web embutido (**jetty**) usando o cargo
5. Levanta um banco de dados embutido (hsqldb)
6. Executa os testes de integração e aceite, onde o código html e os cliques que o cliente fazem são simulados, usando JUnit e VRapor
7. Levanta um servidor web igual ao de produção (websphere, por exemplo), usando o cargo
8. Levanta um banco de dados igual ao de produção (oracle, por exemplo)
9. Executa os testes de integração e aceite na réplica da produção

Se algum desses passos falhar, o sistema notifica o time e o responsavel por quebrar a integração. Note que a integração é continua, isto é, ela percebe os commits, e não espaçada - sendo rodada uma vez por dia.

Nem sempre atingimos 100% de code coverage, dado que podemos extrair usando o [Clover Report](http://www.cenqua.com/clover/), mas saber que 70% ou mais do seu código foi testado e funciona como antigamente ajuda a efetuar modificações no seu código.

Sinceramente, não dá para confiar mais em código não testado automaticamente. Quais são as garantias que você tem que amanhã, aquilo que funcionava hoje não deixará de funcionar?

Lembro de quando o Carlos Villela falava de fazer todas essas etapas de teste e build "complexos" e achava coisa a mais. É impressionante como após fazer isso uma vez você não consegue mais viver sem ela.

Para aqueles que se motivaram a testar, divirtam-se, para os outros, boa sorte.
