---
title: "Caelum Stella - o cinto de utilidades para o desenvolvedor brasileiro"
date: "2008-05-21"
author: "fkung"
authorEmail: "fabio.kung@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Durante o Falando em Java 2008 anunciamos o lançamento do novo [Caelum Stella](http://stella.caelum.com.br). Em 2013 saiu a versão 2.0!

O projeto vem para auxiliar os desenvolvedores brasileiros, suprindo algumas das necessidades comumente encontradas em aplicações desenvolvidas aqui no Brasil. Atualmente, o [Caelum Stella](http://stella.caelum.com.br) fornece uma biblioteca de validadores, formatadores e conversores para documentos brasileiros, tais como CPF, CNPJ e PIS/PASEP.

\[java\] String cpf = "867.554.707-24"; CPFValidator vld = new CPFValidator(); for(ValidationMessage error : vld.invalidMessagesFor(cpf)) { System.out.println(error.getMessage()); } \[/java\]

Há uma alternativa que lança [uma exceção](https://github.com/caelum/caelum-stella/wiki/Stella-Core) caso ocorra algum problema de validação:

\[java\] new CPFValidator().assertValid("867.554.707-24"); \[/java\]

O Stella também inclui módulos extras, como o de geração de boletos bancários, adaptadores para JSF, VRaptor, JBoss Seam e Hibernate Validator. Veja um exemplo de validação para CPFs usando o Caelum Stella junto ao Hibernate Validator:

\[java\] @Entity public class Modelo { @CPF private String cpf;

public String getCpf() { return cpf; } } \[/java\]

O módulo Stella Faces conta com alguns validadores compatíveis com a especificação JSF, que você pode adicionar aos seus componentes:

\[xml\] <h:inputText id="cpf" value="#{usuarioBean.cpf}"> <stella:validateCPF/> </h:inputText> \[/xml\]

O Stella Boleto procura fornecer um idioma mais fluente para a geração de boletos, através do encadeamento de métodos, gerando PDFs, PNGs e em breve TXT, RTF e HTML:

\[java\] Emissor rodrigo = Emissor.novoEmissor().comCedente("Rodrigo Turini") .comAgencia(1234).comCarteira(157).comContaCorrente(123456) .comNossoNumero(123456789l).comDigitoNossoNumero("6");

Sacado paulo = Sacado.novoSacado().comNome("Paulo Silveira");

Boleto boleto = Boleto.novoBoleto().comDatas(datas).comEmissor(rodrigo) .comBanco(banco).comSacado(paulo).comValorBoleto(2680.16) .comNumeroDoDocumento("123456");

new GeradorDeBoleto(boleto).geraPDF("arquivo.pdf"); \[/java\]

Estão ainda previstas funcionalidades como JSP taglibs, rotinas JavaScript para máscaras, validação e suporte a formulários, seleção de cidades dependente da seleção de estados, suporte a mais documentos, geração da **nota fiscal eletrônica**, webservices para busca de endereços através de CEP, entre muitas outras.

Todas estas funcionalidades estão divididas em diversos módulos dentro do Stella. Atualmente são quatro: [Stella Core](https://github.com/caelum/caelum-stella/wiki/Stella-Core), [Stella Bean Validation](https://github.com/caelum/caelum-stella/wiki/stella-bean-validation) (para usar também com Hibernate e JPA), [Stella Faces](https://github.com/caelum/caelum-stella/wiki/Stella-faces) e [Stella Boleto](https://github.com/caelum/caelum-stella/wiki/Stella-boleto). Cada um com um propósito diferente, mas todos relacionados aos problemas do dia a dia recorrentes no mercado brasileiro.

Como de costume em qualquer projeto open-source, o código fonte está disponível em um repositório [no github](https://github.com/caelum/caelum-stella/). Para tirar as suas dúvidas, sugerir funcionalidades, apontar bugs e discutir sobre o projeto, não deixe de postá-la no [GUJ](http://www.guj.com.br).

Visite, use, comente e participe do desenvolvimento do projeto!
