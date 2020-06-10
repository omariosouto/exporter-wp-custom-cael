---
title: "Conhecendo melhor o método inflate"
date: "2015-01-27"
author: "felipe.torres"
authorEmail: "felipe.torres@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Quando queremos criar itens customizados para uma `ListView` do Android, geralmente criamos um `Adapter` para isso. Algo parecido com isso:

\[code language="java"\] public class MeuAdapter extends BaseAdapter {

private Activity activity; private String\[\] elementos;

public MeuAdapter(Activity activity) { this.activity = activity; this.elementos = new String\[\]{"Elemento 1", "Elemento 2", "Elemento 3", "Elemento 4", "Elemento 5"}; } public View getView(int posicao, View convertView, ViewGroup parent) {

}

public int getCount() { return elementos.length; }

public Object getItem(int posicao) { return elementos\[posicao\]; }

public long getItemId(int posicao) { return 0; } } \[/code\]

Dentro do método `getView` precisamos transformar numa View o xml contendo o _layout_ do item da lista, para isso usamos o método `inflate` do `LayoutInflater`.

Esse código é bastante comum:

\[code language="java"\] public View getView(int posicao, View convertView, ViewGroup parent) { LayoutInflater inflater = activity.getLayoutInflater();

View layout = inflater.inflate(R.layout.item\_da\_lista, null);

//popula o layout...

return layout; } \[/code\]

O que significa aquele `null` no segundo argumento do `inflate`?

\[code language="java"\] public View inflate(int resource, ViewGroup root); \[/code\]

Se você olhar com mais calma, perceberá que existe uma outra assinatura do método `inflate` que recebe três argumentos:

\[code language="java"\] public View inflate(int resource, ViewGroup root, boolean attachToRoot); \[/code\]

Qual a diferença entre esses dois métodos?

Quando fazemos...

\[code language="java"\] inflater.inflate(R.layout.item\_da\_lista, null); \[/code\]

...Estamos omitindo o `root` (ou o pai) desse elemento a ser inflado. Em outras palavras, como o Android não sabe quem é o pai desse _layout_, todas as propriedades que precisam de informações do pai para serem calculadas são simplesmente sobrescritas. Veja um exemplo:

\[code language="xml"\] <LinearLayout android:layout\_width="match\_parent" android:layout\_height="200dp">

<TextView android:text="Texto qualquer" android:layout\_width="wrap\_content" android:layout\_height="wrap\_content"/> </LinearLayout> \[/code\]

Perceba que esse item deve ter uma altura fixa de 200 dp. Porém, ao ser renderizado, esse item não ficará com essa altura. O `inflate` sobrescreverá tanto o `layout_width` quanto o `layout_height` para algo que não dependa de informação do pai (que passamos `null`) e colocará `wrap_content`.

Nesse momento, muitos desenvolvedores resolvem contornar esse "bug" utilizando `padding` nos seus itens. Porém, esse "bug" desaparece quando entendemos a segunda versão desse método `inflate`:

\[code language="java"\] public View inflate(int resource, ViewGroup root, boolean attachToRoot); \[/code\]

Precisamos passar um `root` diferente de `null`. Mas quem seria?

Note que o próprio `getView` nos sugere um `root` (ou um `parent`) válido no seu terceiro argumento:

\[code language="java"\] public View getView(int posicao, View convertView, ViewGroup parent) { LayoutInflater inflater = activity.getLayoutInflater();

View layout = inflater.inflate(R.layout.item\_da\_lista, ??, ??);

//...

return layout; } \[/code\]

Então, vamos passar justamente esse `ViewGroup parent` como _root_ no `inflate`:

\[code language="java"\] inflater.inflate(R.layout.item\_da\_lista, parent, ??); \[/code\]

Agora só precisamos entender esse terceiro argumento: `attachToRoot`.

Quando temos um `parent`, podemos decidir se queremos que nosso item seja anexado a esse pai ou não. Geralmente, quando criamos uma _view_ programaticamente, para que ela seja renderizada, passamos `true` no terceiro argumento.

No entanto, a responsabilidade de anexar nosso item à _view_ não é nossa, é do próprio `Adapter`! Nesse caso, deixamos `false`:

\[code language="java"\] public View getView(int posicao, View convertView, ViewGroup parent) { LayoutInflater inflater = activity.getLayoutInflater();

View layout = inflater.inflate(R.layout.item\_da\_lista, parent, false);

//...

return layout; } \[/code\]

Dessa forma, como nosso `item_da_lista` tem um _parent_ diferente de `null`, suas propriedades serão respeitadas.
