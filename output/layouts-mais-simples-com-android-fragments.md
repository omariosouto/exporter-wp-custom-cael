---
title: "Layouts mais flexíveis com Android Fragments"
date: "2012-01-24"
author: "erich.egert"
authorEmail: "erich.egert@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Desenvolver um aplicativo Android com uma boa interface com o usuário não é fácil: os diferentes tamanhos de tela podem dar trabalho para chegar a um resultado satisfatório em múltiplos dispositivos. Quando levamos em conta os tablets surge um problema ainda maior: como criar uma aplicação para smartphone que, ao rodar em um tablet, aproveite o espaço extra que o dispositivo oferece? Mais ainda: como fazer isso de maneira fácil?

A partir da versão 3 do Android, através da [a API de Fragments](http://developer.android.com/guide/topics/fundamentals/fragments.html), podemos dividir nossa tela e dar comportamento a cada pedaço (fragmento) de uma view com um `Fragment`.

Como exemplo vamos usar uma aplicação que contém uma listagem de nomes de arquivos de fotos em um `ListView`. Queremos que o evento de clique em um item da lista nos mostre a imagem selecionada.

Caso nosso Tablet esteja na horizontal gostaríamos que a listagem e a imagem escolhida apareçam na mesma tela, conforme ilustrado na imagem abaixo:

\[caption id="attachment\_4795" align="aligncenter" width="500" caption="Comportamento da seleção de uma imagem no Tablet na horizontal"\][![Comportamento da seleção de uma imagem no Tablet na horizontal](https://blog.caelum.com.br/wp-content/uploads/2012/01/tablet_horizontal_antes_e_depois.png "tablet_horizontal_antes_e_depois")](https://blog.caelum.com.br/wp-content/uploads/2012/01/tablet_horizontal_antes_e_depois.png)\[/caption\]

No caso do tablet estar na vertical gostaríamos de ver apenas a listagem ou a imagem na tela: \[caption id="attachment\_4796" align="aligncenter" width="353" caption="Comportamento da seleção da imagem em um Tablet na vertical"\][![Comportamento da seleção da imagem em um Tablet na vertical](https://blog.caelum.com.br/wp-content/uploads/2012/01/tablet_vertical_antes_e_depois.png "tablet_vertical_antes_e_depois")](https://blog.caelum.com.br/wp-content/uploads/2012/01/tablet_vertical_antes_e_depois.png)\[/caption\]

Para que isso seja possível precisamos ter layouts diferenciados para o tablet em landscape. Podemos fazer isso colocando [qualifiers nas pastas de layout](http://developer.android.com/guide/practices/screens_support.html). Em nosso caso telas grandes em landscape.

![Esquema de qualifiers nas pastas de Layout](https://blog.caelum.com.br/wp-content/uploads/2012/01/pastas_layout.png)

Na view em landscape podemos usar a tag fragment:

\[xml\] <LinearLayout ...> <fragment android:id="@+id/fragment1" android:name="br.com.caelum.hubbletablet.ListaImagensFragment" .../>

<fragment android:id="@+id/fragment2" android:name="br.com.caelum.hubbletablet.ImagemFragment" .../> </LinearLayout> \[/xml\]

Enquanto a outra será apenas um framelayout para inflar:

\[xml\] <LinearLayout ...> <FrameLayout android:id="@+id/ambos\_fragments" .../> </LinearLayout> \[/xml\]

Para criarmos um `Fragment` basta herdarmos da classe e implementar o método `onCreateView`, onde receberemos um inflater o qual podemos usar para inflar uma view que queremos associar ao `Fragment`.

\[java\] public class ListaImagensFragment extends Fragment { public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {

View viewLista = inflater.inflate(R.layout.lista, null); ListView listaImagens = (ListView) viewLista .findViewById(R.id.listaImagens);

colocaDadosNoListView(listaImagens); } // ... } \[/java\]

O controle da tela ainda é feito pela Activity e através dela manipularemos os Fragments. Caso o tablet esteja na horizontal, a Activity estará associada à versão do `main.xml` que usa a tag `fragment`, que informa ao Android as classes dos Fragments que desejamos usar, então o próprio Android se encarregará de instanciá-los. Caso o tablet esteja na vertical, a Activity estará associada à versão do main.xml que contem apenas uma tag `FrameLayout`, nesse caso precisaremos criar o fragment que trará a lista e associá-lo ao `FrameLayout`. Fazemos isso no `onCreate` da Activity:

\[java\] getFragmentManager().beginTransaction() .add(R.id.ambos\_fragments, new ListaImagensFragment()) .commit(); \[/java\]

Para termos a funcionalidade de apertar o botao de back no Android e o mesmo empilhar e desempilhar os fragments, podemos fazer:

\[java\] getFragmentManager().beginTransaction() .replace(R.id.ambos\_fragments, new ListaImagensFragment()) .addToBackStack(null) .commit(); \[/java\]

O código acima só deve ser executado se o tablet estiver na horizontal, portanto precisamos fazer essa verificação, uma maneira de fazer isso é através do [Activity.getResources().getConfiguration().orientation](https://gist.github.com/1664963).

No `onCreateView` do `ListaImagensFragment` podemos guardar em um atributo o endereço da imagem selecionada quando houver um clique em um item na lista. Como fazemos agora para passar essa informação para o `ImagemFragment` para que ele possa mostrar a imagem?

Quando estamos trabalhando apenas com Activities temos que usar o `putExtra` em `Intents` para enviar dados de uma `Activity` para outra. O envio de informações de um Fragment para o outro pode ser feito na Activity de uma forma menos orientada a Strings e maps, tornando nosso código mais orientado a objetos.

Se o tablet estiver na horizontal:

\[java\] ListaImagensFragment listaFragment = (ListaImagensFragment) getFragmentManager().findFragmentById(R.id.fragment1); ImagemFragment imagemFragment = (ImagemFragment) getFragmentManager().findFragmentById(R.id.fragment2); imagemFragment.setImagem(listaFragment.getImagemSelecionada()); imagemFragment.atualizaImagem(); \[/java\]

Se estiver na vertical:

\[java\] ListaImagensFragment listaFragment = (ListaImagensFragment) getFragmentManager().findFragmentById(R.id.ambos\_fragments); ImagemFragment imagemFragment = new ImagemFragment(); imagemFragment.setImagem(listaFragment.getImagemSelecionada()); getFragmentManager().beginTransaction() .replace(R.id.ambos\_fragments, imagemFragment) .addToBackStack(null) .commit(); \[/java\]

Essa solução ficou tão interessante que os desenvolvedores da plataforma resolveram criar [um pacote de compatibilidade](http://developer.android.com/sdk/compatibility-library.html) para poder usar praticamente as mesmas ideias nos dispositivos antigos, sem precisar de um dispositivo moderno com versão 3.x ou 4.x, [de maneira bem simples](http://mobile.tutsplus.com/tutorials/android/android-compatibility-working-with-fragments/).

Um grande cuidado que devemos ter com o uso de Fragments é evitar espalhar por toda aplicação if's que verificam se a tela é grande ou o aparelho está na horizontal, etc. Podemos criar diferentes classes que implementam o comportamento em aparelhos grandes e em pequenos e então fazê-las implementar uma interface java. Assim podemos utilizar o polimorfismo e chegar a uma solução mais elegante.
