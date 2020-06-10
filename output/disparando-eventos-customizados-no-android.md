---
title: "Disparando eventos customizados no Android"
date: "2013-04-23"
author: "erich.egert"
authorEmail: "erich.egert@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Na plataforma Android, o uso de Fragments para flexibilizar o desenvolvimento é uma prática cada vez mais difundida, em especial para aplicações que precisam rodar em tablets. Através dos Fragments é possível isolar o comportamento de pequenos trechos da tela, ajudando a reduzir a quantidade de código nas activities e permitindo maior flexibilidade no momento de fazer a mesma aplicação rodar em Smartphones e Tablets.

Uma questão relevante nesse cenário é: O que devemos fazer quando um evento em um Fragment impacta em outros Fragments?

Suponha uma tela de um Tablet no qual existam 3 Fragments. À esquerda uma listagem de posts de um blog, no canto superior direito um Fragment que mostra os detalhes do post selecionado, no canto inferior direito para a criação de uma resposta para o post selecionado da listagem:

[![tela_3_fragments](https://blog.caelum.com.br/wp-content/uploads/2013/04/tela_3_fragments.png)Tela da aplicação com 3 fragments](https://blog.caelum.com.br/wp-content/uploads/2013/04/tela_3_fragments.png)

Quando clicamos em um item do `ListView` precisamos passar esse item para os demais Fragments, podemos fazer isso diretamente no `Fragment` que apresenta a listagem na tela:

\[code language="java"\] getListView().setOnItemClickListener(new OnItemClickListener() { public void onItemClick(AdapterView<?> adapter, View view, int posicao, long id) { BlogPost postSelecionado = (BlogPost) adapter.getItemAtPosition(posicao); FragmentManager manager = getFragmentManager(); PostSelecionadoFragment telaDetalhesPost = (PostSelecionadoFragment) manager.findFragmentById(R.id.direita\_superior); telaDetalhesPost.lidaCom(postSelecionado); NovoPostFragment telaNovoPost = (NovoPostFragment) manager.findFragmentById(R.id.direita\_inferior); telaNovoPost.lidaCom(postSelecionado); PostsActivity activity = (PostsActivity) getActivity(); activity.lidaComSelecao(postSelecionado); } }); \[/code\]

Mas será que é responsabilidade de um `Fragment` conhecer os demais? Podemos delegar a responsabilidade de lidar com a tela como um todo para a `Activity` que já a está gerenciando , isolando todo o código acima:

\[code language="java"\] getListView().setOnItemClickListener(new OnItemClickListener() { public void onItemClick(AdapterView<?> adapter, View view, int posicao, long id) { BlogPost postSelecionado = (BlogPost) adapter.getItemAtPosition(posicao); PostsActivity activity = (PostsActivity) getActivity(); activity.lidaComSelecao(postSelecionado); } }); \[/code\]

O problema é que acoplamos nosso `Fragment` de listagem com uma `Activity` em específico, dificultando seu reaproveitamento. Se resolvermos dar suporte a smartphones, inclusive de tamanho de tela pequena, precisaremos quebrar essa tela em duas ou até três, tornando o método da Activity que lida com a seleção cada vez mais complexo.

Uma maneira de reduzir o acoplamento é trabalhar com mensagens, de maneira semelhante à que os desenvolvedores no iOS fazem com o NSNotificationCenter.

Na seleção de um item, podemos usar o `LocalBroadcastManager` para disparar uma mensagem de broadcast que não sairá dos limites de nossa aplicação (ajudando nos quesitos segurança e performance).

\[code language="java"\] getListView().setOnItemClickListener(new OnItemClickListener() { public void onItemClick(AdapterView<?> adapter, View view, int posicao, long id) { BlogPost postSelecionado = (BlogPost) adapter.getItemAtPosition(posicao); Intent intent = new Intent("post-foi-selecionado");

intent.putExtra("blogpost", post); LocalBroadcastManager.getInstance(context).sendBroadcast(intent); } }); \[/code\]

Agora podemos registrar os Fragments interessados nesse evento:

\[code language="java"\] public class BlogPostSelecionado extends BroadcastReceiver { private FragmentObservador observador;

public static BlogPostSelecionado registraParaLidarComASelecao(FragmentObservador o) { BlogPostSelecionado receiver = new BlogPostSelecionado(); receiver.observador = o; LocalBroadcastManager.getInstance(o.getContext()) .registerReceiver(receiver, new IntentFilter("post-foi-selecionado"));

return receiver; } } \[/code\]

Agora os Fragments interessados em nosso evento apenas precisam implementar a interface `FragmentObservador`:

\[code language="java"\] public interface FragmentObservador { Context getContext(); void lidaCom(Post post); } \[/code\]

Dessa forma invertemos a responsabilidade de propagar o evento para a plataforma, reduzindo o acoplamento e facilitando a manutenção.

Querendo aprender desenvolvimento Android? [Conheça o nosso curso](http://www.caelum.com.br/curso-android/)!
