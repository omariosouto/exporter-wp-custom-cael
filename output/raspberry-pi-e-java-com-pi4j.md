---
title: "Raspberry Pi e Java com Pi4j"
date: "2014-03-02"
author: "ettore"
authorEmail: "ettore.luglio@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

 [![piscapiscabotao](https://blog.caelum.com.br/wp-content/uploads/2014/02/piscapiscabotao-300x234.png)](https://blog.caelum.com.br/wp-content/uploads/2014/02/piscapiscabotao.png)

Se buscarmos um meio de acesso ao barramento do Raspberry Pi, acharemos muito material em Python. Que aliás, traz o acrônimo no nome do dispositivo (Pi de Python). Porém, pode ser que nos sentimos mais a vontade com outra linguagem, como C ou java.

Pensando exatamente nisso, podemos usar algumas bibliotecas como por exemplo o [Pi4j](http://pi4j.com "Pi4j") que usa internamente o [WiringPi](http://wiringpi.com/ "WiringPi") para o acesso de baixo nível. Sua instalação é bastante simples, basta pegar o `.deb`:

\[code\] wget http://pi4j.googlecode.com/files/pi4j-0.0.5.deb \[/code\]

depois, extrair e instalar:

\[code\] sudo dpkg -i pi4j-0.0.5.deb \[/code\]

Após a instalação, vamos fazer alguns trechos de código para ver como é simples a utilização:

Primeiro, que tal piscarmos uns leds?

1.Pegue a instancia de GPIO 2.Precisamos dizer se o io será usado como saída. 3.Precisamos inicializar com o GPIO especificado 4.Precisamos estar ou receptar o estado do bit.

Segue o código:

\[code language="java"\] GpioController gpio = GpioFactory.getInstance();</em> GpioPinDigitalOutput myLed = gpio.provisionDigitalOutputPin(RaspiPin.GPIO\_01, ”MyLed”, PinState.LOW);</em> myLed.blink(500); \[/code\]

Ou seja, o GPIO1 é uma saída e é para piscar a cada 500ms. Fácil né? Mas e se eu tiver um botão? Como faço para “ouvir” seu estado quando pressionado?

1.Pegue a instancia de GPIO 2.Declarar o io como entrada. 3.Criar o listener para o botão 4.Fazer algo com o botão clicado!

\[code language="java"\] GpioController gpio = GpioFactory.getInstance(); GpioPinDigitalInput myButton = gpio.provisionDigitalInputPin(RaspiPin.GPIO\_02); myButton.addListener(new GpioPinListenerDigital() { public void handleGpioPinDigitalStateChangeEvent(GpioPinDigitalStateChangeEvent event) {               if(event.getState().isHigh()){ System.out.println(“Pressionou”);               } else{                      System.out.println(“Parou de pressionar”);              }      } }); \[/code\]

Com o código pronto, vc pode compilar assim:

\[code\] javac -classpath .:classes:/opt/pi4j/lib/'\*' -d . PiscaPiscaDetectorDeBotao.java \[/code\]

E finalmente, rodar assim:

\[code\] sudo java -classpath .:classes:/opt/pi4j/lib/'\*' PiscaPiscaDetectorDeBotao \[/code\]

Segue agora um exemplo completo do nosso <em>PiscaPiscaDetectorDeBotao.java</em>

\[code language="java"\] import com.pi4j.io.gpio.GpioController; import com.pi4j.io.gpio.GpioFactory; import com.pi4j.io.gpio.GpioPinDigitalInput; import com.pi4j.io.gpio.GpioPinDigitalOutput; import com.pi4j.io.gpio.PinState; import com.pi4j.io.gpio.RaspiPin; import com.pi4j.io.gpio.event.GpioPinDigitalStateChangeEvent; import com.pi4j.io.gpio.event.GpioPinListenerDigital;

public class PiscaPiscaDetectorDeBotao { public static void main(String\[\] args) { GpioController gpio = GpioFactory.getInstance();             GpioPinDigitalOutput myLed = gpio.provisionDigitalOutputPin(RaspiPin.GPIO\_04, "MyLed", PinState.LOW);             myLed.blink(500);

          GpioPinDigitalInput myButton = gpio.provisionDigitalInputPin(RaspiPin.GPIO\_02);             myButton.addListener(new GpioPinListenerDigital() {                   public void handleGpioPinDigitalStateChangeEvent(                         GpioPinDigitalStateChangeEvent event) {                               if (event.getState().isHigh()) {                                   System.out.println("Pressionou"); } else { System.out.println("Parou de pressionar"); } }            });       } } \[/code\]
