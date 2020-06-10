---
title: "Injeção de dependências no Android com RoboGuice"
date: "2012-03-15"
author: "toshi"
authorEmail: "andrew.kurauchi@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

[![](https://blog.caelum.com.br/wp-content/uploads/2012/03/guice2.jpeg "guice2")](https://blog.caelum.com.br/wp-content/uploads/2012/03/guice2.jpeg) "É quase impossível escrever uma app Android que não se pareça com uma app Android". Com essas palavras Michael Burton apresentou o [RoboGuice](http://code.google.com/p/roboguice/) (lê-se "robojuice"), uma ferramenta de injeção de dependências no Android, aos desenvolvedores brasileiros na AndroidConf Brasil 2011. A estrutura do código de uma app Android é fortemente baseada na classe `[Context](http://developer.android.com/reference/android/content/Context.html)`, usada, entre outras coisas, para:

- Criar componentes de tela (`View`s);
- Acessar recursos pré-definidos pelo desenvolvedor (imagens, arquivos de áudio, xml's de configuração, etc - disponíveis no diretório `res`);
- Acessar o sistema de arquivos;
- Criar bancos de dados;
- Enviar mensagens para telas, serviços rodando em background e receptores de bradcast (`Intent`s);
- Obter objetos responsáveis (managers) por algumas funcionalidades do aparelho (enviar SMS, verificar sensores, alarme, entre outros).

Por esse motivo, em muitos pontos do código dependemos de uma instância de `Context`. Acabamos tendo algo parecido como uma variável global, pior ainda, muitas vezes vamos passá-la por referência para todos os lados.

As telas de um aplicativo Android estão sempre acompanhadas de uma classe filha de `Activity` (que por sua vez também é um `Context`), responsável pela lógica associada. Essa classe define o comportamento em cada etapa do ciclo de vida da tela. Para isso é possível implementar alguns métodos que serão chamados pelo sistema em cada etapa:

\[java\] public class MinhaTela extends Activity { protected void onCreate(Bundle savedInstanceState) {        // Chamado assim que a Activity é criada } protected void onStart() { // Chamado logo após o onCreate } protected void onResume() { // Chamado após a tela ser criada depois do onStart } protected void onPause() { // Chamado antes da tela não ser mais visível para o usuário } protected void onStop() {        // Chamado depois que a tela não é mais visível    }    protected void onDestroy() {        // Chamado antes da Activity ser destruída    } } \[/java\]

Muitas vezes precisamos utilizar algumas das funcionalidades disponíveis em `Context` (chamar outra `Activity`, por exemplo). É comum fazermos isso diretamente no código da `Activity` atual, já que ela herda de `Context`. Assim não é raro encontrar `Activities` grandes e com muitas responsabilidades. Tome como exemplo a seguinte `Activity` de uma tela de cotações. Ela busca o valor das cotações atualizadas na internet:

\[java\] public class TelaCotacoes extends Activity {    private TextView textoCotacaoDolar;    private TextView textoCotacaoEuro;    private TextView textoCotacaoPeso;

protected void onCreate(Bundle savedInstanceState) {        super.onCreate(savedInstanceState);        setContentView(R.layout.tela\_cotacoes);

       textoCotacaoDolar = (TextView) findViewById(R.id.cotacaoDolar);        textoCotacaoEuro = (TextView) findViewById(R.id.cotacaoEuro);        textoCotacaoPeso = (TextView) findViewById(R.id.cotacaoPesoArgentino);         String urlDolar = getString(R.string.url\_dolar); String urlEuro = getString(R.string.url\_euro); String urlPeso = getString(R.string.url\_peso);        String cotacaoDolar = buscaCotacao(urlDolar);        String cotacaoEuro = buscaCotacao(urlEuro);        String cotacaoPeso = buscaCotacao(urlPeso);

textoCotacaoDolar.setText(cotacaoDolar); textoCotacaoEuro.setText(cotacaoEuro); textoCotacaoPeso.setText(cotacaoPeso); } private String buscaCotacao(String urlDaCotacao) { HttpClient client = new DefaultHttpClient(); HttpGet get = new HttpGet(urlDaCotacao); String cotacao = null; try { HttpResponse response = client.execute(get); cotacao = EntityUtils.toString(response.getEntity()); } catch (ClientProtocolException e) { // Trata exception } catch (IOException e) { // Trata exception } if(cotacao == null) { cotacao = "Cotação indisponível"; } return cotacao; } protected void onStart() { // Chamado logo após o onCreate } protected void onResume() { // Chamado após a tela ser criada depois do onStart } protected void onPause() { // Chamado antes da tela não ser mais visível para o usuário } protected void onStop() {        // Chamado depois que a tela não é mais visível    }    protected void onDestroy() {        // Chamado antes da Activity ser destruída    } } \[/java\]

Acabamos com um código muitas vezes pouco orientado a objetos, com muitas responsabilidades concentradas na `Activity`. A estrutura do código de uma app Android nos induz a esse tipo de design. Repare que uma parte considerável do código é responsável pela busca das cotações atualizadas na internet. Podemos então extrair esse comportamento para outra classe:

\[java\] public class ValoresDeCotacao { public String buscaDolar() { String urlDolar = // Preciso chamar o método getString(R.string.url\_dolar) a partir de um Context return buscaCotacao(urlDolar); }

public String buscaEuro() { String urlEuro = // Preciso chamar o método getString(R.string.url\_euro) a partir de um Context return buscaCotacao(urlEuro); }

public String buscaPesoArgentino() { String urlPeso = // Preciso chamar o método getString(R.string.url\_peso) a partir de um Context return buscaCotacao(urlPeso); }

private String buscaCotacao(String urlDaCotacao) { HttpClient client = new DefaultHttpClient(); HttpGet get = new HttpGet(urlDaCotacao); String cotacao = null; try { HttpResponse response = client.execute(get); cotacao = EntityUtils.toString(response.getEntity()); } catch (ClientProtocolException e) { // Trata exception } catch (IOException e) { // Trata exception } if(cotacao == null) { cotacao = "Cotação indisponível"; } return cotacao; } } \[/java\]

Aqui temos um problema para conseguir as `String`s das URLs que queremos acessar (definidas em `strings.xml`), pois precisamos de uma instância de `Context` para chamar o método `getString`. Podemos então receber um `Context` no construtor de nossa classe:

\[java\] public class ValoresDeCotacao { private final Context context;

public ValoresDeCotacao(Context context) { this.context = context; }

public String buscaDolar() { String urlDolar = context.getString(R.string.url\_dolar); return buscaCotacao(urlDolar); }

public String buscaEuro() { String urlEuro = context.getString(R.string.url\_euro); return buscaCotacao(urlEuro); }

public String buscaPesoArgentino() { String urlPeso = context.getString(R.string.url\_peso); return buscaCotacao(urlPeso); }

private String buscaCotacao(String urlDaCotacao) { HttpClient client = new DefaultHttpClient(); HttpGet get = new HttpGet(urlDaCotacao); String cotacao = null; try { HttpResponse response = client.execute(get); cotacao = EntityUtils.toString(response.getEntity()); } catch (ClientProtocolException e) { // Trata exception } catch (IOException e) { // Trata exception } if(cotacao == null) { cotacao = "Cotação indisponível"; } return cotacao; } } \[/java\]

Nosso problema foi resolvido, entretanto existe o inconveniente de se ter que passar uma instância de `Context` para o nosso `ValoresDeCotacao`. Pior ainda, todas as classes que precisarem realizar qualquer interação com o sistema precisarão receber um `Context`.

Acontece que em muitos casos não precisamos do `Context` em si, mas de algum recurso fornecido por ele, como `String`s, `Manager`s, ou `View`s. Ou seja, o `Context` acaba agindo como uma grande Factory global.

No nosso caso o que realmente precisamos são as `String`s das URLs guardadas em `strings.xml`. Seria interessante já recebermos diretamente essas `String`s ao invés de receber todo o `Context`. O RoboGuice é capaz de `injetar` essas dependências da seguinte maneira:

\[java\] public class ValoresDeCotacao { @InjectResource(R.string.url\_dolar) private String urlDolar; @InjectResource(R.string.url\_euro) private String urlEuro; @InjectResource(R.string.url\_peso) private String urlPeso; public String buscaDolar() { return buscaCotacao(urlDolar); }

public String buscaEuro() { return buscaCotacao(urlEuro); }

public String buscaPesoArgentino() { return buscaCotacao(urlPeso); }

private String buscaCotacao(String urlDaCotacao) { HttpClient client = new DefaultHttpClient(); HttpGet get = new HttpGet(urlDaCotacao); String cotacao = null; try { HttpResponse response = client.execute(get); cotacao = EntityUtils.toString(response.getEntity()); } catch (ClientProtocolException e) { // Trata exception } catch (IOException e) { // Trata exception } if(cotacao == null) { cotacao = "Cotação indisponível"; } return cotacao; } } \[/java\]

A `TelaCotacoes` agora depende de uma instância de `ValoresDeCotacao`. Para isso devemos herdar de `RoboActivity` ao invés de `Activity` e pedir para o RoboGuice **injetar** essa dependência:

\[java\] public class TelaCotacoes extends RoboActivity { @Inject private ValoresDeCotacao cotacoes;

protected void onCreate(Bundle savedInstanceState) { super.onCreate(savedInstanceState); setContentView(R.layout.main);

TextView textoCotacaoDolar = (TextView) findViewById(R.id.cotacaoDolar); TextView textoCotacaoEuro = (TextView) findViewById(R.id.cotacaoEuro); TextView textoCotacaoPeso = (TextView) findViewById(R.id.cotacaoPesoArgentino); textoCotacaoDolar.setText(cotacoes.buscaDolar()); textoCotacaoEuro.setText(cotacoes.buscaEuro()); textoCotacaoPeso.setText(cotacoes.buscaPesoArgentino()); } } \[/java\]

Repare que o código da `TelaCotacoes` ficou mais enxuto, mas ainda temos código que não está associado à lógica da tela (busca dos `TextView`s e a configuração do layout). As chamadas a `findViewById` e `setContentView` são uma infraestrutura necessária para a lógica. O problema é que se tivermos muitas `View`s as chamadas a `findViewById` podem dificultar o entendimento da nossa lógica de negócios, pois poluirá o código. Podemos mais uma vez recorrer ao RoboGuice para injetar essas dependências no nosso código:

\[java\] @ContentView(R.layout.tela\_cotacoes) public class TelaCotacoes extends RoboActivity { @Inject private ValoresDeCotacao cotacoes; @InjectView(R.id.cotacaoDolar) private TextView textoCotacaoDolar; @InjectView(R.id.cotacaoEuro) private TextView textoCotacaoEuro; @InjectView(R.id.cotacaoPesoArgentino) private TextView textoCotacaoPeso; protected void onCreate(Bundle savedInstanceState) { super.onCreate(savedInstanceState); textoCotacaoDolar.setText(cotacoes.buscaDolar()); textoCotacaoEuro.setText(cotacoes.buscaEuro()); textoCotacaoPeso.setText(cotacoes.buscaPesoArgentino()); } } \[/java\]

A extração de classes e métodos para aumentar a divisão de responsabilidades é possível (e recomendável) mesmo sem o RoboGuice, mas o simples fato de muitas vezes precisarmos de uma instância de `Context` pode nos desestimular a fazer isso. Com a injeção de dependências não temos mais essa preocupação, já que ele se responsabiliza por guardar essa instância, permitindo que foquemos mais em boas práticas de orientação a objetos.

Além da divisão de responsabilidades, utilizando injeção de dependências facilitamos a criação de testes de unidade, já que podemos testar cada uma das dependências e depois injetar mocks em seu lugar, sem ficar sempre mockando a infraestrutura do Android, como a classe `Context`.
