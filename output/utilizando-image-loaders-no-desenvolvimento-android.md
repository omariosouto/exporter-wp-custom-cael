---
title: "Utilizando Image Loaders no desenvolvimento Android"
date: "2015-09-29"
author: "joao.santana"
authorEmail: "joao.santana@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Recentemente participei de um desafio na [Fluid27](http://fluid27.com), uma Startup que está desenvolvendo um app social para compartilhar experiências entre mães, o Mãeguru.

O desafio consistia em desenvolver uma solução mobile com capacidade de lidar com muitas imagens, visando uma melhor experiência do usuário. A tecnologia utilizada ficava a critério do desenvolvedor, então decidi utilizar android nativo. O Layout estava definido nos requisitos do desafio e não precisaria ser igual, mas chegar a algo próximo.

[![Mock_timeline_challenge](https://blog.caelum.com.br/wp-content/uploads/2015/09/Mock_timeline_challenge-144x300.png)](https://blog.caelum.com.br/wp-content/uploads/2015/09/Mock_timeline_challenge.png)

Antes de colocar a mão na massa (ou no código), fiz um checklist do que gostaria de entregar para o usuário:

- Layout dos posts conforme layout, ou próximo da proposta.
- Visualizar as imagens.
- Receber os posts de uma API, para mostrar a aplicação do app mais próximo da realidade.
- Atualizar a lista de posts quando houver um novo.

Criei um Adapter para o layout dos posts, como pode ser visto no código abaixo:

\[code language="java"\] // Código omitido public View getView(int position, View convertView, ViewGroup parent) { View view = activity.getLayoutInflater() .inflate(R.layout.post, parent, false); Post post = posts.get(position);

TextView userName = (TextView) view.findViewById(R.id.post\_user\_name); userName.setText(post.getUserName());

TextView content = (TextView) view.findViewById(R.id.post\_content); content.setText(post.getContent());

new ImageAsyncTask(view).execute(post.getAvatar());

return view; }

\[/code\]

No método `getView()` recupero o layout do [post](https://github.com/Jonss/fluid27/blob/master/app/src/main/res/layout/post.xml) usando o método `inflate()`. Em seguida pego o post naquela posição, então se estou vendo o primeiro post a minha position é 0. Ao ir para o próximo post a position será 1 e assim sucessivamente.

No código recupero a minha view do tipo `TextView` para setar os valores do post, o nome do usuário e a mensagem. Nas linhas abaixo recupero a imagem do post (não tratei o avatar pois estava testando uma hipótese) assincronamente usando a classe `ImageAsyncTask`, filha de **`AsyncTask`**.

Abaixo é possível ver classe que busca os endereços dos meus posts assincronamente e transformava em um bitmap, para que eu pudesse incluir nas Views do tipo `ImageView`.

\[code language="java"\]

public class ImageAsyncTask extends AsyncTask<String,Void,Bitmap> { //Código omitido

@Override protected Bitmap doInBackground(String... urls) { Bitmap image = null; String url = urls\[0\]; try { InputStream inputStream = new URL(url).openStream(); image = BitmapFactory.decodeStream(inputStream); } catch (IOException e) { e.printStackTrace(); } return image; }

@Override protected void onPostExecute(Bitmap bitmap) { ImageView imageView = (ImageView) view.findViewById(R.id.post\_image); imageView.setImageBitmap(bitmap); this.cancel(true); } }

\[/code\]

Sendo filha de `AsyncTask`, o método `doInBackground()` recebe um Array de strings e no método pego a primeira posição. Transformo a URL recebida em um `InputStream()` para então decodificar em um Bitmap. O método `onPostExecute()` faz o set desse Bitmap que tenho em um Objeto `ImageView`, que possibilita o usuário visualizar a imagem. O método `cancel` é executado após o set para que a requisição não continue infinitamente.

Durante essa parte do desenvolvimento, usei um JSON estático no projeto pois receber os posts de uma API era outra parte da solução. Isso me ajudou a resolver esse requisito e pensar nas seguintes mais pra frente, quando aquilo se tornasse realmente uma necessidade. Utilizando a classe filha de `AsyncTask` as imagens podiam ser vistas no app, então marquei os dois primeiros itens do meu checklist como pronto.

- Layout dos posts conforme layout, ou próximo da proposta.
- Visualizar as imagens
- Receber os posts de uma API, para mostrar a aplicação do app mais próximo da realidade.
- Atualizar os posts quando houverem novos.

Ao subir um pouco a lista eu percebi que as imagens desapareciam e uma nova requisição era feita quando eu subia a timeline. Imagine a seguinte situação: Você está passando por sua timeline no Instagram, e decide voltar alguns posts anteriores e esse posts não estão mais no app, demora um certo período para você conseguir ver o post já que uma nova requisição é executada. Pessoalmente, desinstalaria o app e daria uma nota baixa no Google play. O ciclo de vida de um adapter ocorre da seguinte forma: Um item é montado na visualização da tela, e ao fazer o scroll outros itens são montados para visualização. Os primeiros itens, ao saírem da visualização são recolhidos pelo garbage collector para que objetos sem uso não fiquem alocados na memória desnecessáriamente.

Incluí mais um item, que a principio não havia enxergado a necessidade: **Manter as imagens por um tempo no app**.

- Manter as imagens por um tempo no app.
- Receber os posts de uma API, para mostrar a aplicação do app mais próximo da realidade.
- Atualizar os posts quando houver novos.

### Como solucionar isso?

Precisaria deixar as imagens em _cache_ para que a experiência do usuário não se tornasse frustrante. O Google recomenda criar uma classe filha de `AsyncTask` que implementa métodos que lidam com Cache, como pode ser visto na [documentação](http://developer.android.com/training/displaying-bitmaps/cache-bitmap.html).

Mas esse problema já foi resolvido por alguns desenvolvedores, e evitando reinventar a roda utilizei a lib Picasso da [Square](http://square.github.io/picasso) que na sua descrição diz "_A powerful image downloading and caching library for Android_". Problema resolvido, eu não precisava mais ter uma classe filha de `AsyncTask` que transformaria os links em Bitmaps e depois settar estes bitmaps nas `ImageViews`, o Picasso seria responsável por isso. A documentação do Picasso é super simples, com exemplos de fácil entendimento. Alterei a minha classe filha de `Adapter`, responsável pelo Layout dos posts. O método `getView()` ficou assim:

\[code language="java"\] public View getView(int position, View view, ViewGroup parent) { // Código omitido Picasso.with(context).load(post.getImageUrl()).into(holder.postImage); Picasso.with(context).load(post.getAvatarUrl()).into(holder.userAvatar); return view; } \[/code\]

Esse código basicamente diz: Nesse contexto, faça o carregamento dessa imagem nesse endereço em um `ImageView` (`holder.postImage` e `holder.userAvatar`). O [context](http://developer.android.com/reference/android/content/Context.html) nessa situação é a `Activity` onde a `View` será mostrada.

Para importar o Picasso no Android Studio, inclui a seguinte linha em app/build.gradle: `compile 'com.squareup.picasso:picasso:2.5.1'.` O resultado foi muito bom, consegui excluir uma classe que executava um trabalho pesado e usei uma dependência simples de ser entendida no código. Assim consegui atender a os requisitos do desafio, usando uma lib já consolidada no mercado. Usamos a biblioteca Picasso em nosso Curso [Técnicas de Desenvolvimento Android avançado](https://www.caelum.com.br/curso-android-avancado/).

### Satisfação garantida?

Após isso, dei continuidade à construção do App. Criei a API para receber um JSON nas requisições e transformar esse JSON em Objeto Java para incluir em uma `ListView`.

Em um dos testes, incluí um gif de um gato (gifs de gatos movem a internet) e no app ficou uma imagem estática. Esse comportamento não me deixou feliz. Queria ir além e mostrar gifs animados, como pode ser visto em Apps como o 9gag. Mas a lib não provê uma forma de lidar com Gifs então busquei outra alternativa.

### Alternativas para o Picasso

Buscando alternativas para o Picasso, encontrei o [Glide](https://github.com/bumptech/glide). O uso da biblioteca é muito semelhante a lib Picasso. A documentação também é bastante simples, como pode ser visto no exemplo abaixo retirado do github deles:

\[code language="java"\] ImageView imageView = (ImageView) findViewById(R.id.my\_image\_view); Glide.with(this).load("http://goo.gl/gEgYUd").into(imageView); \[/code\]

No app, utilizei da seguinte forma:

\[code language="java"\] public View getView(int position, View view, ViewGroup parent) { //Código omitido Glide.with(context).load(post.getImageUrl()) .diskCacheStrategy(RESULT) .into(holder.postImage);

Glide.with(context).load(post.getAvatarUrl()) .error(R.mipmap.fluid) .diskCacheStrategy(RESULT) .into(holder.userAvatar);

return view; }

\[/code\]

Este código é bastante parecido com o anterior, usando o Picasso, com um destaque para os métodos `diskCacheStrategy(RESULT)` que faz o cache da imagem redimensionada e o método `error()` que recebe uma imagem como padrão em caso de algo dar errado no dowload.

Inclui no app da mesma forma, incluindo a dependencia no `build.gradle`: `compile 'com.github.bumptech.glide:glide:3.5.2'`

O Glide possui algumas vantagens sobre o Picasso, como consumo de memória mais baixo ao renderizar uma imagem. Porém essa vantagem está intrinsecamente ligada a uma desvantagem: **a perda qualidade das imagens** pois seu formato padrão é o RGB\_565. Há uma alternativa para sobrescrever o padrão, settando explicitamente que o `DecodeFormat` deve ser ARGB\_8888.

\[code language="java"\] public class GlideConfiguration implements GlideModule { @Override public void applyOptions(Context context, GlideBuilder builder) { // Apply options to the builder here. builder.setDecodeFormat(DecodeFormat.PREFER\_ARGB\_8888); } // Código omitido }

\[/code\]

O Glide não mostra um delay na visualização da imagem, diferente do Picasso que por padrão possui um efeito de FadeIn. Isso dá a impressão ao usuário que o app é mais rápido, uma vez que o usuário não vê a imagem sendo "montada". A imagem é apresentada o mais rápido possível - variando de acordo com a velocidade do device e a conexão. O cache é feito de uma forma mais esperta, sendo feito apenas após fazer resize da imagem, enquanto o Picasso faz o cache da imagem que recebeu. Então se uma imagem tiver 2560x1600, usando o Picasso este será o tamanho da imagem em cache enquanto usando Glide será o tamanho da `ImageView`, por exemplo (768x432 pixels). Porém há um custo, a lib Glide é maior que a lib Picasso, possui 430kb enquanto o Picasso possui 118kb. Não vejo como um grande problema quanto a essa particularidade, uma vez que a necessidade que eu gostaria de atender - visualizar gifs - foi atendida.

O uso de bibliotecas de terceiros é uma grande ajuda. Nessa situação me poupou bastante tempo. Analisando as duas Libs, não cheguei a uma conclusão de qual das duas é melhor. Usaria a lib Picasso em outra situação, onde nao houvesse necessidade de visualizar gifs e o tamanho de libs de terceiros fosse importante. Para esta necessidade o Glide foi escolhido por possuir features que o Picasso não atende. Existem inúmeras Libs que fazem o mesmo, como a [Fresco](http://frescolib.org/) do Facebook, [DaVinci](https://github.com/florent37/DaVinci), e mais algumas que podem ser vistas [aqui](http://android-arsenal.com). O código da app do desafio está disponível no [github](http://github.com/Jonss/fluid27).

Como você lidaria com essa situação? Já teve que desenvolver algo assim? Qual foi a sua solução? Compartilhe conosco a sua experiência!
