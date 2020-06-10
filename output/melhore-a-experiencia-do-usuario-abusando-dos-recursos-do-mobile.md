---
title: "Melhore a experiência do usuário abusando dos recursos do mobile"
date: "2016-03-03"
author: "marco.bruno"
authorEmail: "marco.bruno.br@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Quando estava projetando o [curso de UX da Caelum](https://www.caelum.com.br/curso-ux-usabilidade-mobile-web/) fiquei com a seguinte dúvida. O projeto que cada time (grupo de 4 pessoas) escolhe no primeiro dia na sala de aula pra desenvolver, com o objetivo de aprender UX deveria ser um app ou um projeto web desktop? O que me fez escolher um app em vez do site desktop ou de um sistema web foram os recursos que estão disponíveis ou são utilizados de uma forma mais efetiva no mundo mobile. Por exemplo:

1. [Câmera](#camera)
2. [Landscape e Portrait](#landscapePortrait)
3. [Geolocalização](#geolocalizacao)
4. [LED Notification](#ledNotification)
5. [Agenda de contatos](#agendaDeContatos)
6. [Scroll vertical incomoda menos no mobile](#scroll)
7. [Push Notification](#pushNotification)

## 1\. Câmera

Eu chuto que tirar selfies é forma que os usuários mais utilizam a câmera dos smartphones. Mas a câmera também é utilizada pra ler QRCodes e código de barras, gravar momentos engraçados, guardar momentos únicos pra compartilhar com amigos em redes sociais. É um recurso que podemos utilizar pra deixar a experiência do usuário mais única e feliz. Por exemplo se você tem que pagar uma conta com código de barras, é muito mais feliz e ágil utilizar a câmera pra ler o código de barras do que digitar uns 40 dígitos pra pagar sua conta:

![Screenshot_2016-02-10-22-48-15](https://blog.caelum.com.br/wp-content/uploads/2016/02/Screenshot_2016-02-10-22-48-15-1024x576.png)

O App do itaú não conseguiu ler o Octocat com seus filhinhos, mas funciona muito bem pra ler código de barras.

## 2\. Landscape e Portrait

Já temos um espaço reduzido no mobile comparado com o desktop por isso é um boa aproveitar da melhor forma o espaço da tela. Um app que faz bom proveito do espaço da tela é o Youtube. Quando estamos assistindo o vídeo no Youtube e o vídeo está difícil de entender ou muito pequeno, temos a opção de deixar em tela cheia. A parte realmente boa é que se o vídeo foi gravado em portrait, ele fica ocupando a tela toda nessa posição. Se o vídeo foi gravado em landscape ele é apresentado nessa posição também ocupando a tela toda.

### Portrait sem tela cheia

![portrait](https://blog.caelum.com.br/wp-content/uploads/2016/02/portrait-e1455228540984.png)

### Portrait em tela cheia

![Screenshot_2016-02-11-16-53-11](https://blog.caelum.com.br/wp-content/uploads/2016/02/Screenshot_2016-02-11-16-53-11-576x1024.png)

### Landscape sem tela cheia

![landscape](https://blog.caelum.com.br/wp-content/uploads/2016/02/landscape-e1455228803144.png)

### Landscape em tela cheia

![Screenshot_2016-02-11-16-54-43](https://blog.caelum.com.br/wp-content/uploads/2016/02/Screenshot_2016-02-11-16-54-43-1024x576.png)

## 3\. Geolocalização

Essa é uma funcionalidade que também temos no desktop, mas a o precisão dela no mobile é muito melhor. E saber onde o usuário esta é bem vantajoso. Por exemplo, o Google Now descobre onde fica sua casa caso você deixe ele acessar sua localização. Eu gosto bastante da forma que o Waze utiliza a geolocalização. Com ela podemos marcar locais com acidentes, marcar buracos na rua, carro quebrado no acostamento ou no meio da rua, polícia a vista ou escondida, também podemos mandar a nossa localização pra um amigo. Mas a funcionalidade mais feliz do Waze é ele mostrar o trânsito moderado, intenso ou parado quase em tempo real, essa funcionalidade é tão boa que a Globo mostra ela no jornal pra falar como está em São Paulo.

![Screenshot_2016-02-11-17-15-46](https://blog.caelum.com.br/wp-content/uploads/2016/02/Screenshot_2016-02-11-17-15-46-e1455229402547.png)

## 4\. LED Notification

LED Notification é aquela luz que fica piscando quando recebemos um mensagem no Whatsapp. Por padrão a cor do LED é branca mas podemos configurar a cor do LED:

![Screenshot_2016-02-11-17-53-03](https://blog.caelum.com.br/wp-content/uploads/2016/02/Screenshot_2016-02-11-17-53-03-e1455229101638.png)

Poder configurar a cor do LED no Whatsapp é bem útil pois podemos saber de qual app é a notificação sem a necessidade de desbloquear a tela. Infelizmente não são todos os apps que tem essa configuração e tem alguns aparelhos que não tem o LED Notification em outra cor além do branco.

## 5\. Agenda de contatos

Toda vez que vou instalar um app e ele pede pra ter acesso a agenda de contatos eu não fico muito feliz.

O único app que eu instalei que não me incomodei, pelo contrário, fiquei feliz de dar acesso a minha agenda de contatos foi o Itaú Tokpag. Ele precisa da sua agenda de contatos porque a funcionalidade do Tokpag precisa buscar na sua lista de contatos quais dos seus contatos também tem instalado o Tokpag. Com isso ele permite você transferir dinheiro pra esses contatos sem a necessidade de saber a agência ou conta do seu amigo.

![Screenshot_2016-02-12-17-50-41](https://blog.caelum.com.br/wp-content/uploads/2016/02/Screenshot_2016-02-12-17-50-41-e1455369750257.png)

## 6\. Scroll vertical incomoda menos no mobile

Redes sociais como Facebook e Twitter utilizam muito o bem o scroll vertical pra manter o usuário por mas tempo navegando. Apenas scrollando (verbo estranho mas utilizamos) verticalmente sabemos o que nossos amigos e amigos dos nossos amigos estão compartilhando, apenas com essa interação que me lembra a leitura de pergaminho. Apesar de ser uma funcionalidade simples de usar tem o mesmo problema do pergaminho. Se você gostou de algum conteúdo que achou interessante e quer voltar nele não é um caminho tão feliz, pois precisa rever tudo que você não gostou no processo contrário no scroll vertical. Por isso que é importante implementar um funcionalidade similar ao Like do Twitter.

![Screenshot_2016-02-11-20-29-50](https://blog.caelum.com.br/wp-content/uploads/2016/02/Screenshot_2016-02-11-20-29-50-e1455229895462.png)

## 7\. Push Notification

Tem muitos apps que utilizam esse recurso de forma tão chata que nos faz desinstalá-lo. Por outro lado o app da NuBank utiliza isso de uma forma feliz. Toda vez que você faz uma compra com o cartão deles o app te notifica o valor da compra e local em poucos segundos.

![IMG_3180](https://blog.caelum.com.br/wp-content/uploads/2016/02/IMG_3180-e1455224506983-1024x905.png)

Esses são algumas dos recursos que podemos utilizar de forma favorável pro usuário. Você já implementou ou utilizou alguma app que utilizou algum desses recursos ou outros do mobile com o objetivo de melhorar a experiência do usuário?
