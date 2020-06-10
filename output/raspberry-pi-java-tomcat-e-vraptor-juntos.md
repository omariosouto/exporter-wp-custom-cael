---
title: "Raspberry Pi, Java, Tomcat e VRaptor juntos"
date: "2014-01-07"
author: "ettore"
authorEmail: "ettore.luglio@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![rPi tomcat vraptor java](https://blog.caelum.com.br/wp-content/uploads/2014/01/1715347-300x210.png)](https://blog.caelum.com.br/wp-content/uploads/2014/01/1715347.png)

Sim, todos juntos e vou falar das minhas aventuras com essa plaquinha poderosa!

O [Raspberry Pi](http://www.raspberrypi.org/ "Raspberry Pi") é uma placa do tamanho de um cartão de crédito com saídas HDMI, RCA, audio, USB, RJ45 (No modelo B) e uma entrada para SDCard que portará o sistema operacional. Fora isso, ela possui um barramento com entradas e saídas para podermos ligar dispositivos diversos, portanto, um prato cheio para quem conhece eletrônica, os entusiastas e quem estiver disposto a aprender. Querem saber o mais incrível, existe uma versão com 256Mb de memória que custa $25 e uma de 512Mb por $35! São respectivamente os modelos A e B. É um processador de 700Mhz mais uma GPU com capacidade de produzir imagens a 1080p. Ele nasceu de uma iniciativa em 2006 de produzir computadores baratos para crianças quando perceberam uma baixa de alunos de Ciência da Computação.

Vamos para a parte prática. O Raspberry Pi precisa de um sistema operacional, então usaremos o [raspian](http://www.raspberrypi.org/downloads "Raspian") que é a distribuição oficial. Para colocarmos a imagem no SD Card, precisamos antes saber aonde ele está! Usaremos o comando `df -h` que nos dará um retorno parecido com a imagem a seguir:

[![tela dd rPi](https://blog.caelum.com.br/wp-content/uploads/2014/01/Captura-de-Tela-2013-12-28-às-19.00.08-300x51.png)](https://blog.caelum.com.br/wp-content/uploads/2014/01/Captura-de-Tela-2013-12-28-às-19.00.08.png)

Desmonte o seu cartão com o seguinte comando: “sudo diskutil unmount /dev/disk1s1” e para copiar a imagem, o famoso dd com o comando: “sudo dd bs=1m if=<sua imagem> of=/dev/rdisk1”.

Quando acabar, basta colocá-lo no dispositivo e energizar com uma fonte igual de celular, com 5V e pelo menos 500mA. O usuário padrão é pi e a senha é raspberry

Na primeira rodada, aparecerá o `raspian-config` para que algumas coisas sejam configuradas, como por exemplo, o acesso por ssh. No terminal, configure a rede/internet, pois precisaremos dela.

Vamos usar o nano: `sudo nano /etc/network/interfaces` e pode deixar algo parecido com o modelo abaixo:

\[code\] auto lo iface lo inet loopback iface eth0 inet static address 192.168.0.200 netmask 255.255.255.0 gateway 192.168.0.1

allow-hotplug wlan0 iface wlan0 inet static wpa-ssid “<o nome da sua rede>” wpa-psk “<a sua senha>” address 192.168.2.200 netmask 255.255.255.0 gateway 192.168.2.1 iface default inet dhcp \[/code\]

Não se esqueça de reiniciar após alterar os arquivos de configuração. Pelo terminal basta fazer um `sudo reboot` ou desligue e ligue na tomada! Vamos agora instalar o java da oracle que tem a melhor [performance](https://blogs.oracle.com/jtc/entry/comparing_jvms_on_arm_linux "Performance Java rPi") até agora, com o `sudo apt-get install oracle-java7-jdk`.

Vamos instalar também o tomcat6. Baixe-o e descompate, além de dar a devida permissão:

\[code\] tar xzf apache-tomcat-6.0.37.tar.gz cd apache-tomcat-6.0.37/conf nano tomcat-users.xml \[/code\]

E abaixo de <tomcat-users> adicione a linha: `<user username="system" password="raspberry" roles="manager-gui">`

Depois é só mandar rodar:

\[code\] cd ../bin sudo sh startup.sh \[/code\]

Baixe o [projeto em branco](https://code.google.com/p/vraptor3/downloads/detail?name=vraptor-blank-project-3.5.3.zip&can=2&q= "Blank Project") do site do [Vraptor](http://vraptor.caelum.com.br/pt/ "VRaptor") e exporte o war como preferir.

Após este procedimento, entre em qualquer browser e acesse: `http://<endereço do raspberry pi>:8080/VRAPTOR`

[![vraptor rPi](https://blog.caelum.com.br/wp-content/uploads/2014/01/Captura-de-Tela-2013-12-28-às-19.23.21-300x98.png)](https://blog.caelum.com.br/wp-content/uploads/2014/01/Captura-de-Tela-2013-12-28-às-19.23.21.png)

O que vocês acham? Um servidor web rodando java, tomcat e vraptor por $35. No Brasil ele é encontrado por algo em torno de R$ 180,00. Não gosta de java? Não tem problema, pode fazer em php, pode programar em python, c++, só escolher. Na internet existem muitos tutoriais para transformá-lo em câmera de vigilância, por exemplo, ou você pode usar a imaginação e criar o seu próprio circuito!
