---
title: "RaspberryPi com Grappa"
date: "2014-06-23"
author: "rodrigo.gunisalvo"
authorEmail: "rodrigo.gunisalvo@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma das coisas mais empolgantes em trabalhar com tecnologia são as pequenas revoluções que acontecem frequentemente no mundo da computação. Se você tem acompanhado nosso blog talvez esteja percebendo uma [delas](https://blog.caelum.com.br/raspberry-pi-java-tomcat-e-vraptor-juntos/). Mas não se trata apenas de termos um servidor web portátil, a popularização de __“_single board computers_”__ como [RaspberryPi](http://www.raspberrypi.org/) ou microcontroladores como [Arduino](http://www.arduino.cc/) tornam cada vez mais simples sairmos de um mundo 100% virtual e botarmos nossas habilidades Java para trabalhar em projetos que envolvem tanto hardware como software. Diversos nomes de peso da área tecnológica já estão apostando na ideia de computação embarcada. Por exemplo: a distribuição Linux [Raspbian](http://www.raspbian.org/), desenvolvida especificamente para RaspberryPi, já vem com o [JDK 7 Oracle](http://www.raspberrypi.org/oracle-java-on-raspberry-pi/) instalado. Isso significa que aquelas preciosas habilidades de desenvolvimento estão a sua disposição.

![RaspberryPi2](https://blog.caelum.com.br/wp-content/uploads/2014/06/RaspberryPi2-300x223.jpg)

Cada hardware trará possibilidades diferentes. Com o Raspberry, por exemplo, podemos nos comunicar com sensores através das GPIO, [portas programáveis que podem ser conectadas em qualquer circuito digital tradicional](http://caelum.wpengine.netdna-cdn.com/wp-content/uploads/2014/02/piscapiscabotao.png "GPIO") - qualquer é uma promessa perigosa, a tensão máxima recomendada é de 3,3V mas você não deve ter nenhuma dificuldade em achar sensores e relés que funcionem dentro desta faixa. O distribuidor oficial do microprocessador no Brasil é a [Farnell Newark](http://www.farnellnewark.com.br/) que também possui uma seleção de kits e peças eletrônicas para ajuda-lo a começar seus primeiros projetos.

Para construir a interface entre software e hardware existem algumas bibliotecas focadas nesta tarefa. No mundo Java, por exemplo, existe o [Pi4J](http://pi4j.com/) que já estrelou um post no [blog](https://blog.caelum.com.br/raspberry-pi-e-java-com-pi4j/). Uma fez concluído este passo você provavelmente vai querer reaproveita-lo. É um caso de _“boiler plate code”_ fadado ao Ctrl+C e Ctrl+V entre seus projetos. Se você preferir pula-lo pode usar uma solução pronta como o projeto [Grappa](https://github.com/Gunisalvo/Grappa). Sua proposta é ser uma camada fina de abstração do hardware que não impõe nenhum outro framework ou ferramenta de programação. Uma característica importante é que você pode desenvolver em sua máquina com o apoio de um hardware virtualizado. Isso garante alguma segurança para testar suas funcionalidades antes de fazer a implantação real e, melhor ainda, sem nenhuma configuração adicional. Ainda é um framework novo então podem aparecer alguns problemas, mas em sua versão atual já retira bastante código de configuração do caminho. Isso economiza algumas horas de sofrimento criando uma forma de testar e implantar seu projeto.

Vamos vê-lo em ação fazendo um exemplo simples de controle de acesso. Suponha que exista um sensor de presença ligado a nosso Raspberry, ele emite os sinais elétricos que vamos processar para identificar as entradas autorizadas. O primeiro passo é configurar o comportamento das GPIO configurando o pino que receberá o sinal. Essa é a responsabilidade do arquivo grappa.xml:

Arquivo: grappa.xml

\[code language="xml"\] <pin-configuration> <default-pin-type>OUTPUT</default-pin-type><!--1 --> <initial-address>1</initial-address><!--2 --> <final-address>2</final-address><!--3 --> <pins> <pin><!--4 --> <address>2</address> <type>INPUT</type> </pin> </pins> <configuration-package> br.com.caelum.grappaExemplo.servico </configuration-package><!--5 --> </pin-configuration> \[/code\]

Para entender esta configuração vamos seguir a legenda abaixo:

1. os pinos, ou portas, normalmente são saídas digitais, a menos que especificado de outra forma;
2. a primeira porta mapeada corresponde ao pino de posição 1 da placa;
3. a última porta mapeada corresponde a posição 2;
4. o pino 2 é uma entrada digital, ou seja, se alterarmos a corrente elétrica ele está apto a processar esta mudança;
5. o pacote java onde você vai criar as classes que processarão os eventos.

Com este arquivo no classpath de nosso projeto podemos montar nosso sistema. Para ajudar a visualizar a configuração observe o esquema:

 

![grappa-contexto](https://blog.caelum.com.br/wp-content/uploads/2014/06/grappa-contexto4.png)

 

No próximo exemplo vamos simular o envio do sinal elétrico, em uma situação real este código servirá apenas para testes durante o desenvolvimento. A mudança que estamos simulando ficará a cargo de algum tipo de sensor.

\[code language="java"\] //Arquivo: Aplicacao.java package br.com.caelum.grappaExemplo;

//imports omitidos

public class Aplicacao {

public static void main(String\[\] args) { GrappaContext contexto = new DefaultContext(); PhysicalDevice raspberryPi = contexto.getPhysicalDevice(); GrappaInstruction requisicao = new GrappaInstruction(2, PinFormat.LOGIC, Action.WRITE, 1); raspberryPi.processInstruction(requisicao); // Abriu raspberryPi .processInstruction(new LogicInstruction().address(2).write(0)); // Fechou } } \[/code\]

1. a montagem do contexto processa o arquivo grappa.xml mapeando os pinos;
2. de acordo com ambiente em que é invocado o contexto pode dar acesso ao hardware real ou criar uma simulação, em ambos os casos suas funcionalidades são iguais para que seus testes se comportem o mais próximo possível do ambiente real;
3. a Instrução Grappa é uma mensagem padrão para comunicação com o hardware, seu formato é:

- o endereço do pino que vamos operar;
- seu formato – neste exemplo é um sinal lógico com 0 representando falso e 1 verdadeiro;
- a ação, que pode ser de leitura ou escrita ;
- o valor que vamos escrever no recurso endereçado, é ignorado em caso de uma leitura.

1. a comunicação é feita em um sistema de requisição e resposta;
2. apesar de apresentar algumas vantagens, a legibilidade da criação da instrução é ruim, então você pode usar classes utilitárias para deixar seu código mais elegante.

Ao executar a função main acima nada de emocionante acontece. Isso por que, apesar de termos enviado as mudanças de sinal, ainda não designamos nenhum comportamento durante seu processamento. Precisamos criar um Listener que será registrado no contexto Grappa durante sua criação. Nosso esquema precisa ficar assim:

 

![grappa-servico](https://blog.caelum.com.br/wp-content/uploads/2014/06/grappa-servico2.png)

 

Um ponto importante é que, por questões de desempenho, estes Listeners devem estar no pacote assinalado no arquivo de configuração grappa.xml, os outros requisitos são implementar a interface que será usada para processar o sinal elétrico e anotar a classe fornecendo o endereço do pino observado. Vamos ver uma versão simplificada que resolve nosso problema:

\[code language="java"\] //Arquivo: ControleAcesso.java package br.com.caelum.grappaExemplo.servico; // 1

//imports omitidos

@PinListener(addresses = 2)// 2 public class ControleAcesso implements PinService{

private Integer contador = 0;

@Override public void processEvent(Integer estadoPino) { // 3 System.out.println("processando evento eletrico.");

if (estadoPino.intValue() == 1) { // 4 this.contador += 1; String mensagem = this.contador + " entrada(s) autorizada(s)."; System.out.println(mensagem); //5 } } } \[/code\]

1. esta classe deve ser criada no pacote de configurações especificado no arquivo grappa.xml;
2. a anotação marca o pino que enviará a mudança de sinal elétrico, no nosso caso pino 2;
3. o framework especifica um contrato padrão para o processamento da mudança do sinal elétrico;
4. o valor do sinal elétrico é passado como argumento para o método de serviço, neste exemplo o sinal é digital - 0 ou 1;
5. a evento ocorrerá tanto na passagem de negativo para positivo ( 0 para 1 ) como positivo para negativo ( 1 para 0 ), então vamos contar como entrada apenas o primeiro caso.

Executando novamente a função main podemos ver as mensagens impressas no console mostrando que o sistema está funcionando.

Esse projetinho apresenta todos os conceitos básicos do framework, você pode baixar esse código fonte [aqui](https://github.com/Gunisalvo/ArtigoGrappaCaelum). Para um exemplo mais completo que apresenta outras funcionalidades você pode baixar [GrappaWeb](https://github.com/Gunisalvo/GrappaWeb). Nele você encontra integração com serviços REST e SOAP além de eventos ligados a um circuito digital completo.

Então, o que você achou? Se quiser ver o Grappa funcionando em um protótipo funcional não perca o evento **"Internet das Coisas com Java e RaspberryPi"** no dia 26/06/2014 na Caelum Rio:

[http://www.eventick.com.br/raspberrypi](http://www.eventick.com.br/raspberrypi "http://www.eventick.com.br/raspberrypi") Vagas limitadas!
