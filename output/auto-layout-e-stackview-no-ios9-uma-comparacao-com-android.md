---
title: "Auto Layout e StackView no iOS9: uma comparação com Android"
date: "2016-03-09"
author: "felipe.torres"
authorEmail: "felipe.torres@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Ao trabalhar com _layouts_ no Android estamos acostumados com o desafio de criá-los de forma a se adaptar ao maior número possível de telas, pois dada a diversidade de dispositivos, criar um _layout_ que só fica bom para uma única dimensão de tela pode causar problemas para outros usuários.

Esse desafio é tão comum que o próprio Android deu uma ajudinha criando valores para usarmos nas dimensões dos nossos elementos quando queremos um comportamento mais fluido: é o caso do `match_parent` e `wrap_content`.

Então, um _layout_ bastante familiar que deixaria um campo de inserção de texto e um botão, ambos centralizados, ocupando a largura total da tela do aparelho seria assim:

\[code language="xml"\] <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android" android:layout\_width="match\_parent" android:layout\_height="match\_parent" android:orientation="vertical" android:gravity="center">

<EditText android:layout\_width="match\_parent" android:layout\_height="wrap\_content" android:layout\_marginBotton="30dp"/>

<Button android:layout\_width="match\_parent" android:layout\_height="wrap\_content" android:text="Botão" android:background="#FF800080" android:textColor="#FFFFFFFF"/> </LinearLayout> \[/code\]

Já no mundo o iOS as coisas são um pouquinho diferentes. Como no início desse sistema operacional, não haviam iPhones com dimensões diferentes de tela, _layouts_ não-fluidos faziam bastante sentido. No máximo tínhamos que nos preocupar com a aparência da nossa aplicação quando o aparelho estivesse na horizontal, mas ainda assim seriam apenas dois tipos de _layouts_ diferentes.

As coisas começaram a se complicar em 2012 com o lançamento do iPhone 5, que era o primeiro iPhone a ter uma tela de 4 polegadas, enquanto todos os outros até então tinham 3.5 polegadas. Juntamente com essa meia polegada a mais na tela, veio uma preocupação: se quiséssemos nossa aplicação bonita nos iPhones 4 e 5 teríamos que fazer até quatro _layouts_ diferentes, considerando os aparelhos nas orientações vertical e horizontal!

No entanto, juntamente com esse novo aparelho, na [Apple World Wide Conference (WWDC) de 2012](https://developer.apple.com/videos/play/wwdc2012-202/) o iOS 6 foi anunciado com uma funcionalidade bastante interessante que existe até hoje: o [Auto Layout](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/index.html), uma forma bem legal de criarmos _layouts_ que se adaptam aos diferentes tamanhos de dispositivos!

Hoje em dia o Auto Layout está bastante integrado ao Xcode, tornando seu uso bastante simples. Vamos fazer uma tela parecida com a definida no Android, contendo um campo de texto e um botão ocupando a largura total da tela:

Como estamos focados somente no _layout_, podemos criar uma aplicação qualquer contendo já uma telinha para manipularmos. No Xcode, vamos escolher _Single View Application_:

![Single View Application](https://blog.caelum.com.br/wp-content/uploads/2016/02/SingleViewApplication.png)

Na sequência, podemos colocar um nome para a app de TesteAutoLayout:

![Nome para a app](https://blog.caelum.com.br/wp-content/uploads/2016/02/NomeDaApp.png)

Abrindo o arquivo `Main.storyboard`, podemos começar a construir nosso _layout_! Primeiramente, vamos arrastar os dois componentes que teremos, um `Text Field` e `Button` que estão no painel no canto inferior direito da IDE:

![Painel de elementos](https://blog.caelum.com.br/wp-content/uploads/2016/02/ArrastarElementos.png)

Com o `Button` selecionado, vamos selecionar o _Attributes Inspector_ e colocar um _background_ nele para visualizarmos suas dimensões:

![Attributes Inspector](https://blog.caelum.com.br/wp-content/uploads/2016/02/AttributesInspector.png)

![Background](https://blog.caelum.com.br/wp-content/uploads/2016/02/Background.png)

Ao final, teremos uma tela com essa cara no nosso _storyboard_:

![Projeto sem Auto Layout](https://blog.caelum.com.br/wp-content/uploads/2030/02/ProjetoSemAutoLayout.png)

Porém, ao rodar essa tela num simulador qualquer (no meu caso, estou usando um simulador para o iPhone 6), olha como ela ficará:

![Resultado sem Auto Layout](https://blog.caelum.com.br/wp-content/uploads/2030/02/EmuladorSemAutoLayout.png)

Além disso, as coisas ficam um pouco piores quando rotacionamos a tela. Apertando CMD + seta, temos esse layout:

![Emulador rotacionado sem Auto Layout](https://blog.caelum.com.br/wp-content/uploads/2030/02/EmuladorSemAutoLayout2.png)

Vamos usar o Auto Layout para que nossos componentes se adaptem a quaisquer dimensões de tela!

Para facilitar a centralização de múltiplas _views_ em relação a um mesmo ponto, o iOS 9 possui uma funcionalidade chamada [Stack View](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/), que nos permite criar uma _view_ que funcionará como container e manipular suas propriedades!

Vamos criar uma `Stack View` selecionando o `Button` e o `Text Field` e clicando em `Editor -> Embed In -> Stack View`

Com isso, nosso _layout_ ficou completamente estranho:

![Stack View](https://blog.caelum.com.br/wp-content/uploads/2030/02/StackView.png)

Com o `Stack View` selecionado, vamos no _Attribute Inspector_ e colocaremos em `Spacing` um espaçamento entre as _views_ filhas de 30.

![Spacing](https://blog.caelum.com.br/wp-content/uploads/2030/02/Spacing.png)

Agora, podemos usar as propriedades de centralização do _Auto Layout_ apenas clicando no nosso `Stack View`, apertando o CTRL, arrastando para o lado e selecionando a opção _Center Vertically in Container_:

![Center Vertically in Container](https://blog.caelum.com.br/wp-content/uploads/2030/02/CenterVertically.png)

Vamos fazer a mesma coisa, mas agora arrastando para o lado **direito** e selecionando a opção _Trailing Space to Container Margin_:

![Trailing Space to Container Margin](https://blog.caelum.com.br/wp-content/uploads/2030/02/TrailingSpace.png)

Por fim, vamos arrastar para o lado **esquerdo** e selecionaremos a propriedade _Leading Space To Container Margin_.

![Leading Space to Container Margin](https://blog.caelum.com.br/wp-content/uploads/2030/02/LeadingSpace.png)

Clicando em ambas linhas-guia azuis, vamos definir essa constante para 30 pt:

![Horizontal Space constant](https://blog.caelum.com.br/wp-content/uploads/2030/02/HorizontalSpaceConstant.png)

Por fim, vamos clicar no último botão no canto inferior direito da nossa tela (_Resolve Auto Layout Issues_) e, em seguida, _Update Frames_:

![Update Frames](https://blog.caelum.com.br/wp-content/uploads/2030/02/UpdateFrames.png)

Com isso, temos nosso _layout_ fluido, centralizado em **qualquer** tela! Faça o teste e rode a aplicação!

![Resultado na vertical com Auto Layout](https://blog.caelum.com.br/wp-content/uploads/2030/02/Resultado1.png)

![Resultado na horizontal com Auto Layout](https://blog.caelum.com.br/wp-content/uploads/2030/02/Resultado2.png)

Veja que conseguimos esse resultado bastante interessante com apenas com alguns cliques! E isso é apenas o começo para demonstrar o poder do Auto Layout juntamente com views agrupadas.

E você, já havia usado Auto Layout nas suas aplicações ou achava que era um bicho de sete cabeças?
