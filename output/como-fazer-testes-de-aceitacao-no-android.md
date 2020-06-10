---
title: "Como fazer testes de aceitação no Android?"
date: "2014-05-28"
author: "felipe.torres"
authorEmail: "felipe.torres@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Nos [cursos de Android da Caelum](http://www.caelum.com.br/cursos-mobile/) é bastante comum a seguinte pergunta: **Como fazer testes de aceitação para uma aplicação Android no Eclipse?**

É importante perceber que, como esse tipo de teste deverá utilizar as classes do Android, precisaremos chamar classes que estão definidas no _android.jar_. No entanto, as classes que estão definidas no _android.jar_ não possuem implementação real, são apenas _stubs_ para "enganar" o compilador, pois o código real do Android está nos aparelhos e emuladores.

Dessa forma, se quisermos rodar algum teste fora do ambiente do Android, ao chamar na mão algum método do _android.jar_ receberemos uma `RuntimeException("Stub!")` para nos lembrar de que esses métodos não possuem implementação.

Então, como fazer um teste que utilize as classes do Android?

Primeiramente, vamos criar um novo _Android Application Project_ contendo uma _Activity_ com um menu:

\[code language="java"\] public class PrimeiraActivity extends Activity {

@Override protected void onCreate(Bundle savedInstance) { super.onCreate(savedInstance); setContentView(R.layout.activity\_primeira); }

@Override public boolean onCreateOptionsMenu(Menu menu) { getMenuInflater().inflate(R.menu.menu, menu); return super.onCreateOptionsMenu(menu); } } \[/code\]

O nosso _menu.xml_ possuirá apenas um item chamado `menu_segunda_activity`:

\[code language="xml"\] <menu xmlns:android="http://schemas.android.com/apk/res/android" >

<item android:id="@+id/menu\_segunda\_activity" android:showAsAction="always" android:title="Segunda Activity"/>

</menu> \[/code\]

Ainda na `PrimeiraActivity`, vamos dizer que ao clicar no item `menu_segunda_activity`, vamos abrir a `SegundaActivity`:

\[code language="java"\] public class PrimeiraActivity extends Activity {

//código digitado anteriormente...

@Override public boolean onMenuItemSelected(int featureId, MenuItem item) { if(item.getItemId() == R.id.menu\_segunda\_activity) { Intent segunda = new Intent(this, SegundaActivity.class); startActivity(segunda); } return super.onMenuItemSelected(featureId, item); } } \[/code\]

Vamos, agora, criar a `SegundaActivity` com o seguinte código:

\[code language="java"\] public class SegundaActivity extends Activity{

@Override protected void onCreate(Bundle savedInstanceState) { super.onCreate(savedInstanceState); setContentView(R.layout.activity\_segunda); } } \[/code\]

Agora que temos nosso projeto principal pronto, podemos criar um teste para verificar se a `SegundaActivity` é realmente chamada no clique do _options menu_. Para isso, vamos criar um projeto novo do tipo _Android Test Project_: um projeto separado do projeto principal, que será o responsável pelos testes!

Criar esse projeto é tão simples quanto criar um _Android Application Project_: basta fazer

\[code\] Ctrl+N -> Android -> Android Test Project \[/code\]

Na próxima tela, basta dar um nome a esse projeto e, ao clicar em _Next_, selecionar o projeto que será testado.

Com isso, já podemos criar nosso teste para verificar se a `SegundaActivity` realmente será chamada ao clicar no item do menu!

No nosso _Android Test Project_, vamos criar uma classe chamada `PrimeiraActivityAcceptanceTest` e torná-la filha de `ActivityInstrumentationTestCase2`. Além disso, temos que dizer no tipo genérico qual classe será testada (no caso, `PrimeiraActivity`):

\[code language="java"\] public class PrimeiraActivityAcceptanceTest extends ActivityInstrumentationTestCase2<PrimeiraActivity> {

public PrimeiraActivityAcceptanceTest(){ super(PrimeiraActivity.class); } } \[/code\]

Como nosso teste deve simular um toque na tela para chamar o menu, vamos habilitar esse teste para usar a tela e recuperar uma instância da `PrimeiraActivity` para usar no teste. Além disso, como precisamos utilizar a infraestrutura do Android para processar esses eventos, precisamos de um elemento chamado `Instrumentation`, que justamente simula essa estrutura do Android para que possamos simular uma chamada a um item do menu.

Tudo isso pode ser feito no método `setUp()`, que é chamado antes dos testes:

\[code language="java"\] public class PrimeiraActivityAcceptanceTest extends ActivityInstrumentationTestCase2<PrimeiraActivity> {

private PrimeiraActivity primeiraActivity; private Instrumentation instrumentation;

public PrimeiraActivityAcceptanceTest(){ super(PrimeiraActivity.class); }

@Override protected void setUp() throws Exception { super.setUp(); setActivityInitialTouchMode(true); primeiraActivity = getActivity(); instrumentation = getInstrumentation(); } } \[/code\]

Agora podemos fazer nosso teste!

Como o _Android Test Project_ utiliza um runner criado sobre o JUnit 3 para rodar os testes, todos os nossos métodos de teste devem seguir as convenções do JUnit 3, como por exemplo, começar com "test".

\[code language="java"\] public class PrimeiraActivityAcceptanceTest extends ActivityInstrumentationTestCase2<PrimeiraActivity> {

private PrimeiraActivity primeiraActivity; private Instrumentation instrumentation;

public PrimeiraActivityAcceptanceTest(){ super(PrimeiraActivity.class); }

@Override protected void setUp() throws Exception { super.setUp(); setActivityInitialTouchMode(true); primeiraActivity = getActivity(); instrumentation = getInstrumentation(); }

public void testAoClicarNoMenuDeveChamarSegundaActivity() throws Exception {

} } \[/code\]

Nesse teste, vamos simular a chamada ao _options menu_...

\[code language="java"\] public void testAoClicarNoMenuDeveChamarSegundaActivity() throws Exception { sendKeys(KeyEvent.KEYCODE\_MENU); } \[/code\]

... clicar num item específico desse menu utilizando o instrumentation...

\[code language="java"\] public void testAoClicarNoMenuDeveChamarSegundaActivity() throws Exception { sendKeys(KeyEvent.KEYCODE\_MENU);

instrumentation.invokeMenuActionSync( primeiraActivity, R.id.menu\_activity\_proxima, 0); } \[/code\]

... e verificar se a _Activity_ chamada é realmente a `SegundaActivity`. Mas como verificar que a `SegundaActivity` foi realmente chamada? O próprio _instrumentation_ fornece uma forma de fazer isso: por meio de um `ActivityMonitor`.

A ideia é monitorar o teste e ver se a `SegundaActivity` aparece na tela até 1000ms depois do clique no menu:

\[code language="java"\] public void testAoClicarNoMenuDeveChamarSegundaActivity() throws Exception {

ActivityMonitor monitor = instrumentation.addMonitor( SegundaActivity.class.getName(), null, false); sendKeys(KeyEvent.KEYCODE\_MENU);

instrumentation.invokeMenuActionSync( primeiraActivity, R.id.menu\_segunda\_activity, 0);

SegundaActivity segundaActivity = (SegundaActivity) monitor .waitForActivityWithTimeout(1000); } \[/code\]

Caso a `SegundaActivity` apareça, teremos sua referência na variável `segundaActivity`; do contrário, será _null_. Então, esse teste só deve passar se `segundaActivity` não for _null_:

\[code language="java"\] public void testAoClicarNoMenuDeveChamarSegundaActivity() throws Exception {

ActivityMonitor monitor = instrumentation.addMonitor( SegundaActivity.class.getName(), null, false); sendKeys(KeyEvent.KEYCODE\_MENU);

instrumentation.invokeMenuActionSync( primeiraActivity, R.id.menu\_activity\_segunda, 0);

SegundaActivity segundaActivity = (SegundaActivity) monitor .waitForActivityWithTimeout(1000);

assertNotNull(segundaActivity); } \[/code\]

Nosso teste está pronto! Para rodar os testes, basta clicar com o botão direito no projeto e

\[code\] Run As... -> Android JUnit Test \[/code\]

Esse projeto será instalado como um _Android Application Project_! Além disso, por ser um teste de aceitação, quando o Android executar o teste é possível ver a execução de cada passo na tela do aparelho ou emulador!
