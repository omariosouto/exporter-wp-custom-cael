---
title: "Novo Caelum Stella 2.0"
date: "2013-06-04"
author: "rodrigo.turini"
authorEmail: "rodrigo.turini@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Você já pode baixar a nova versão do Caelum Stella, a 2.0, na [página de downloads](https://github.com/caelum/caelum-stella/wiki/Download) do projeto, ou via repositório do maven, descrito na mesma página.

[![stella](https://blog.caelum.com.br/wp-content/uploads/2013/06/stella.jpg)](https://blog.caelum.com.br/wp-content/uploads/2013/06/stella.jpg)

O Stella é um framework open-source que nasceu dentro da Caelum com o propósito de resolver os problemas do dia a dia no desenvolvimento de software brasileiro. Em sua nova versão, dentre várias [pequenas melhorias](https://github.com/caelum/caelum-stella/commits/master), as principais mudanças estão nos módulos de geração de boleto, validação, conversor de número por extenso e core do Caelum Stella.

A API do Stella Boleto foi refatorada para padrão 100% brasileiro. Para criação de um boleto você poderá fazer algo como:

\[code language="java"\] Emissor rodrigo = Emissor.novoEmissor().comCedente("Rodrigo Turini") .comAgencia(1234).comCarteira(157).comContaCorrente(123456) .comNossoNumero(123456789l).comDigitoNossoNumero("6");

Sacado paulo = Sacado.novoSacado().comNome("Paulo Silveira");

Boleto boleto = Boleto.novoBoleto().comDatas(datas).comEmissor(rodrigo) .comBanco(banco).comSacado(paulo).comValorBoleto(2680.16) .comNumeroDoDocumento("123456"); \[/code\]

Para gerar um boleto em formato PDF, por exemplo, você poderá fazer:

\[code language="java"\] new GeradorDeBoleto(boleto).geraPDF("arquivo.pdf"); \[/code\]

Além das alterações no design das classes, todos os boletos foram revisados e atualizados de acordo com as normas especificadas pelos diferentes Bancos e carta circular do banco central.

No Stella Bean Validation, todas as validações de Inscrições Estaduais foram atualizadas de acordo com as normas do site [sintegra](http://www.sintegra.gov.br/) e documentos especificos dos estados. Além disso, os validadores agora tem um construtor para habilitar/desabilitar dígitos repetidos, e para o caso da anotação `@CPF`, criamos um novo parametro (`ignoreRepeated`) para controlar o uso dessa opção. Para validar o campo cpf de um modelo com o hibernate validator a anotação fica da seguinte forma:

\[code language="java"\] @Entity public class Exemplo { @CPF(ignoreRepeated=true) private String cpf; } \[/code\]

Além disso, com a contribuição do [Wagner Francisco](https://github.com/wagnerfrancisco), agora temos um novo módulo, o [Stella Flex](https://github.com/caelum/caelum-stella/tree/master/stella-flex). Ele possibilita validação de inscrições estaduais via ActionScript/Flex.

O projeto Caelum Stella é uma iniciativa open-source para facilitar a vida do desenvolvedor brasileiro e seu código fonte está disponível em um [repositório no github](https://github.com/caelum/caelum-stella). Fica registrado nosso agradecimento para todos da comunidade que colaboraram com o projeto. Você pode contribuir com novas ideias, implementando novas features, notificando bugs, e participando de discussões sobre passos futuros do projeto. Contamos com seu feedback, e toda ajuda será bem vinda!

Para tirar mais duvidas sobre o Stella você pode visitar nossa [wiki](https://github.com/caelum/caelum-stella), [site](http://stella.caelum.com.br/) ou [postar suas duvidas no GUJ](http://guj.com.br/perguntas).

O que achou das mudanças? Quais devem ser os próximos passos do framework?
