---
title: "JAXB - XML e Java de mãos dadas"
date: "2008-02-27"
author: "gmoreira"
authorEmail: "guilherme.moreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Você já participou de um projeto que precisou ler um arquivo de configuração em xml? Já precisou consumir um xml e transformá-lo em objeto? O que você usou? Quem já trabalhou com xml sabe da dificuldade que podemos encontrar pelo caminho, e é esse tipo de dificuldade que a especificação _Java Architecture for XML Binding_ ou simplesmente [JAXB](http://java.sun.com/developer/technicalArticles/WebServices/jaxb/) tenta resolver.

Imagine a seguinte situação: Precisarmos enviar os dados contidos em um objeto para um outro servidor. Temos muitas opções para fazer o envio, como por exemplo colocar essas informações em um arquivo de texto seguindo uma máscara pré-definida. Porém apenas as aplicações que conhecessem essa máscara entenderiam os dados, e perdemos portabilidade. Usando xml a situação já é outra: qualquer aplicação, independende de linguagem, entenderá os dados contidos no arquivo xml.

Antes de falarmos sobre o JAXB vamos primeiro conferir alguns conceitos:

**XML** XML é uma linguagem de marcação que serve para guardar dados de uma forma estruturada. Essa estrutura é definida pelo próprio usuário ou por um _schema_. Um xml é um arquivo de texto puro, portanto independente de plataforma, por isso é muito utilizado para transmitir dados entre diferentes aplicações e sistemas. Exemplo: carro.xml

\[xml\] <?xml version="1.0" encoding="UTF-8"?> <carro> <nome>Fusca</nome> <portas>2</portas> <motoristas> <motorista> <nome>Guilherme</nome> </motorista> <motorista> <nome>Leonardo</nome> </motorista> </motoristas> </carro> \[/xml\]

**XSD** XSD é o _schema_ citado na seção anterior, ele define quais são as regras que a estrutura do xml deve seguir, possibilitando a validação desse xml. Exemplo:

\[xml\] <?xml version="1.0" encoding="UTF-8"?> <xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema"> <xsd:element name="carro" type="Carro" /> <xsd:complexType name="Carro"> <xsd:sequence> <xsd:element name="nome" type="xsd:string" minOccurs="1" maxOccurs="1" nillable="false"/> <xsd:element name="portas" type="xsd:int" minOccurs="1" maxOccurs="1" nillable="false"/> <xsd:element name="motoristas" type="Motorista" minOccurs="0" maxOccurs="unbounded"/> </xsd:sequence> </xsd:complexType> <xsd:complexType name="Motorista"> <xsd:sequence> <xsd:element name="nome" minOccurs="1" maxOccurs="1" type="xsd:string" nillable="false"/> </xsd:sequence> </xsd:complexType> </xsd:schema> \[/xml\]

O primeiro ponto da especificação apresenta uma ferramenta chamada _Binding Compiler_, cuja função é transformar um [xsd](http://www.w3.org/XML/Schema) em um conjunto de classes que tenham uma estrutura compatível com a estrutura do xml que esse xsd define.

No XSD de exemplo definimos a seguinte estrutura: Um elemento carro deve ter um elemento _nome_ e um elemento _motoristas_ (do tipo Motorista), seguindo essa ordem, primeiro _nome_ e depois _motoristas_. Depois definimos o tipo Motorista que deve conter apenas um _nome_.

O Binding Compiler é independente da implementação do JAXB, ou seja, quem define como ele será executado é quem implementa a especificação, porém a maioria e inclusive a própria _RI(Reference Implementation)_ cria um comando que pode ser chamado pela linha de comando do Sistema Operacional, o _xjc_. Por exemplo no Linux: `xjc carro.xsd -d src -p br.com.caelum`

Se você já está usando o Java 6, o JAXB já vêm junto com o JDK.

Com esse comando o Binding Compiler gera três classes: Carro.java, Motorista.java e a ObjectFactory.java. As classes Carro e Motorista seguem a estrutura do xsd.

**Gerando e Lendo XML** A segunda parte da especificação define o que temos que fazer para transformar objetos em xml e vice-versa. A API do JAXB é quem se responsabiliza por essas transformações.

**Transformando objetos em xml** O processo de transformar um objeto em xml é chamado de **Marshal**. Com o JAXB para transformar um objeto em xml precisamos de um JAXBContext, esse _context_ é quem fornecerá o Marshaller. O Marshaller é quem finalmente transforma um objeto (JAXBElement) em xml. O JAXBElement contém o objeto de verdade a ser serializado e algumas propriedades do xml. É aqui que entra a importância do ObjectFactory criado pelo Binding Compiler, ele é responsável por criar uma instância do JAXBElement apropriada para o tipo de objeto a ser serializado.

\[java\] JAXBContext context = JAXBContext.newInstance("br.com.caelum"); Marshaller marshaller = context.createMarshaller(); JAXBElement<Carro> element = new ObjectFactory().createCarro(carro); marshaller.marshal(element, System.out); \[/java\]

**Parseando xml em objetos java** Para fazer o caminho contrário, ou seja popular um objeto java com dados de um xml também precisamos de um JAXBContext, porém agora temos que pegar um Unmarshaller. O Unmarshaller recebe um arquivo xml e devolve um JAXBElement contendo um objeto populado.

\[java\] JAXBContext context = JAXBContext.newInstance("br.com.caelum"); Unmarshaller unmarshaller = context.createUnmarshaller(); JAXBElement<Carro> element = (JAXBElement<Carro>) unmarshaller.unmarshal(new File("resources/carro.xml")); Carro carro = element.getValue(); \[/java\]

**Conclusão** O JAXB facilita muito a vida dos programadores java, fazendo o consumo e criação de xml menos trabalhosos. Essa API também fornece outros recursos como, validação, geração de _schema_ (a partir de classes java, cria um xsd), opções para trabalhar com Namespace e etc. Comente nesse post outras oções do JAXB e outras bibliotecas que você usa no seu dia-a-dia.
