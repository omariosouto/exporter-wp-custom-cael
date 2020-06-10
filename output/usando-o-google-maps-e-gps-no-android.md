---
title: "Usando o Google Maps e GPS no Android"
date: "2017-04-10"
author: "ettore"
authorEmail: "ettore.luglio@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

> _Este é um post criado em 2010 que está sendo atualizado neste ano de 2017. Os ajustes realizados visam a melhora da formatação como também do conteúdo._

[![](https://blog.caelum.com.br/wp-content/uploads/2010/04/Android_icon-281x300.png)](https://blog.caelum.com.br/wp-content/uploads/2010/04/Android_icon.png) O mercado para [Android está cada vez mais agitado](https://blog.caelum.com.br/google-android-uma-nova-plataforma-java-para-celulares/), e a cada dia aparecem novas informações animadoras: a [quantidade de novos aplicativos saltou de 16 mil para 3 milhões](https://www.statista.com/statistics/266210/number-of-available-applications-in-the-google-play-store/) desde 2009 pra cá.

Isso não ocorre sem razão. A capacidade de usar os serviços do Google através do Android é excelente. Um dos capítulos mais interessantes do [curso FJ-57 de Google Android](http://www.caelum.com.br/curso/fj-57-desenvolvimento-google-android/) é o que envolve fazer um mashup com o Google Maps, o GPS do seu celular e dados que gravamos nele.

Essa tarefa se revela inicialmente bastante simples, e depois ainda possibilita inserir uma série de listeners e overlays para poder customizar o uso dos mapas.

## Criando o projeto

Dado que você já sabe [preparar o ambiente para um HelloWorld no Android](http://www.edsongoncalves.com.br/2010/03/22/seu-primeiro-aplicativo-no-google-android-2/), crie uma nova aplicação com uma activity chamada `MapasSimples`. Você vai precisar adquirir uma API KEY do Google Maps para poder ter a permissão de usá-lo em sua aplicação.

## Gerando a chave da API do Maps

Para isso, gere um MD5 da sua chave através do keytool (ou mesmo através do plugin do Android, usando a mesma [do procedimento que você usa para assinar suas aplicações](http://developer.android.com/guide/publishing/app-signing.html)) para depois [se inscrever no Google Android Maps API Key](https://developers.google.com/maps/documentation/static-maps/get-api-key).

## Adicionando o layout do mapa

Depois desse passo, o Google vai te fornecer uma chave de acesso, juntamento com um trecho de XML para ser usado na sua tela. No nosso caso, o `main.xml` ficará parecido com:

\[xml\] <?xml version="1.0" encoding="utf-8"?> <LinearLayout xmlns:android= "http://schemas.android.com/apk/res/android" android:orientation="vertical" android:layout\_width="fill\_parent" android:layout\_height="fill\_parent" > <com.google.android.maps.MapView android:id="@+id/map\_view" android:layout\_width="fill\_parent" android:layout\_height="fill\_parent" android:enabled="true" android:clickable="true" android:apiKey="SUA CHAVE DO GOOGLE MAPS" /> </LinearLayout> \[/xml\]

## Modificando a nossa activity

Agora precisamos alterar a nossa activity `MapasSimples` para, em vez de estender `Activity`, ser filha de [`MapActivity`](https://developers.google.com/maps/documentation/android-api/v1/?csw=1#classes):

\[java\] public class MapasSimples extends MapActivity {

@Override public void onCreate(Bundle savedInstanceState) { super.onCreate(savedInstanceState); setContentView(R.layout.main); }

@Override protected boolean isRouteDisplayed() { return false; } } \[/java\]

## Configurando o manifest

No nosso manifest, precisamos avisar dentro de `application` que vamos usar a biblioteca do Google Maps:

\[xml\] <uses-library android:name="com.google.android.maps" /> \[/xml\]

E também precisamos permitir acesso a Internet, dentro da tag de `manifest`:

\[xml\] <uses-permission android:name= "android.permission.ACCESS\_COARSE\_LOCATION" /> <uses-permission android:name= "android.permission.INTERNET" /> \[/xml\]

Isso já é o suficiente para abrir o Google Maps, mas queremos ir além. Podemos adicionar diversos overlays ao mapa, para colocar informações como as pizzarias mais próximas, a bússola, a nossa posição atual de acordo com o GPS ou mesmo a posição de seus amigos.

## Adicionando mais características no mapa

Dentro do método `onCreate` pode adicionarmos um novo overlay ao mapa, que vai conter tanto a bússola quanto a nossa própria localização (ou de acordo com o GPS ou de acordo com a triangularização de antenas), de maneira simples:

\[java\] MapView mapView = (MapView) findViewById(R.id.map\_view) ; mapView.setClickable(true) ;

MyLocationOverlay mlo = new MyLocationOverlay(this, mapView) ; mlo.enableCompass() ; mlo.enableMyLocation() ; mapView.getOverlays().add(mlo) ; \[/java\]

Pronto! Você já pode gerar sua aplicação obtendo o seguinte resultado rodando em um celular Google Nexus One:

[![Caelum Android Google Maps](http://farm5.static.flickr.com/4051/4506757107_e23ee20769.jpg)](http://www.flickr.com/photos/sergio-lopes/4506757107/ "Caelum Android Google Maps by sergio-lopes, on Flickr")

Você pode incrementar facilmente esse exemplo, como adicionar novos overlays, atualizar sua posição conforme a localização do aparelho, mostrar o trânsito, traçar rotas, além de especificar o seu [LocationProvider](http://developer.android.com/reference/android/location/LocationProvider.html) (GPS ou antena) pelo `LocationManager`.

O Android ainda possibilita acesso total a recursos do aparelho como câmera, acelerômetro, bluetooth e multitoque, além de APIs ricas para construir sua interface, acessar serviços do aparelho (como ligações e SMS) e APIs do Google como o Maps.

E, através de Web Services, podemos criar um mashup poderoso, misturando informações capturadas da internet com os mapas e outras funcionalidades do aparelho. E tudo usando a sintaxe da linguagem Java.

Se estiver com dificuldades, não deixe de colocar uma dúvida no [sistema de dúvidas do GUJ](http://www.guj.com.br/ "GUJ").

O Android é a plataforma mobile que mais cresce no mundo atualmente e a tendência é continuar assim. Com um modelo de programação familiar e recursos poderosos, tem tudo para agradar a comunidade de desenvolvedores.

Para saber mais sobre Android, confira o [FJ-57: Desenvolvimento móvel com Google Android](http://www.caelum.com.br/curso/fj-57-desenvolvimento-google-android/) e também leia [o livro](http://www.casadocodigo.com.br/products/livro-android "Livro Android") lançado pela [Editora Casa do Código](http://www.casadocodigo.com.br "Casa do Código").
