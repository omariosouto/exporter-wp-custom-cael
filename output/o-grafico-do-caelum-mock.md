---
title: "O gráfico do Caelum Mock"
date: "2007-01-29"
author: "gmoreira"
authorEmail: "guilherme.moreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Eu gostaria de começar esse post já comentando de como eu criei o gráfico, mas eu recomendo aos leitores que visitem a página [Caelumock](http://www.caelum.com.br/caelummock/), façam um teste e vejam o gráfico de aproveitamento no final dele, pois é sobre ele que escreverei a partir de agora.

Em geral no JFreechart temos que seguir alguns passos para montar um gráfico, o primeiro deles é definir qual o tipo de gráfico que desejamos montar, pois a partir dessa escolha é que vamos montar o conjunto de dados, o _Dataset_.

Como conferimos no site, o tipo do gráfico escolhido foi um do tipo de barras. Um gráfico com barras requer um _Dataset_ do tipo categoria. \[java\] DefaultCategoryDataset dataset = new DefaultCategoryDataset(); \[/java\]

Este tipo de _Dataset_ permite ao usuário definir vários conjuntos, que iriam gerar várias barras com cores diferentes para cada conjunto. No exemplo atual vamos utilizar apenas um conjunto, o de "Acertos (%)".

O primeiro dado que inseriremos no gráfico é a porcentagem geral de acertos, repare que além do valor e do conjunto, também temos que avisar qual o nome daquela barra, ou seja primeiro parâmetro é o valor, o segundo é o conjunto e o terceiro é o nome da barra, o segundo e o terceiro devem ser `Comparable`'s pois o Jfreechart vai comparar os grupos para organizar a ordem e as cores das barras e vai comparar os nomes das barras para poder juntar as que tiverem o mesmo nome. \[java\] dataset.addValue(mockStatistics.getPercentageOfTotal(), "Acertos (%)", "Geral"); \[/java\]

Agora os próximos dados são as taxas de acerto por categoria. Os dados pertencem à outra classe, que não está em questão, mas o nome já é auto-explicativo.

\[java\] for (int i = 0; i < mockStatistics.getListOfCategories().size(); i++) { dataset.addValue(mockStatistics.getPercentageByCategory(i), "Acertos (%)", mockStatistics.getListOfCategories().get(i).getId().getName()); } \[/java\]

Já temos os dados mas ainda falta criar o gráfico. O JFreechart disponibiliza uma classe (abstrata) que cria os gráficos, ela se chama ChartFactory (nada sugestivo hein ;) ) e contém métodos estáticos que devolvem objetos do tipo JFreechart.

No caso do gráfico em barras temos que passar 8 (oito) parâmtros: 1º - `String` - título (pode ser null) 2º - `String` - nome que vão aparecer nas categorias (pode ser null) 3º - `String` - nome que vai aparecer ao lado dos dados (pode ser null) 4º - `CategoryDataset` - _Dataset_ que criamos 5º - `PlotOrientation` - indica se o gráfico vai ser na horizontal ou vertical (o JFreechart já tem uma ajudinha pronta pra isso) 6º - `boolean` - mostrar ou não legendas 7º - `boolean` - gerar ou não tooltips 8º - `boolean` - gerar ou não url's

Assim, na pŕoxima instrução vamos usar o ChartFactory (abstrata) para criar um gráfico de Barra 3D, definindo o título como "Sua Pontuação", o nome das categorias não precisa de nada, o nome dos dados como "Pontos (%)", utilizando o _Dataset_ criado anteriormente, passando a constante HORIZONTAL da classe PlotOrientation para avisar que o gráfico é na horizontal, mostrando as legendas e ignorando tooltip e as URL's.

\[java\] JFreeChart chart = ChartFactory.createBarChart3D("Sua Pontuação", null, "Pontos (%)", dataset, PlotOrientation.HORIZONTAL, true, false, false); \[/java\]

Finalizando, temos que colocar o gráfico no _response_ da Servlet (no site uso o VRaptor assim eu tenho uma lógica que não devolve view nenhuma, apenas um _response_ do tipo "image/png").

Primeiro mudamos o tipo de do conteúdo do response para image/png

\[java\] response.setContentType("image/png"); \[/java\]

Com o objeto _response_, pegamos o seu _OutputStream_ e atribuimos a uma nova variável que será usada na hora de renderizar o gráfico.

\[java\] OutputStream os = response.getOutputStream(); \[/java\]

O JFreechart também disponibiliza algumas ferramentas que auxiliam os programadores a renderizar os gráficos de várias maneiras, como, renderizar direto para um arquivo, para um outputstream ou mesmo no formato JPEG ou PNG, definir qualidade e etc.. \[java\] ChartUtilities.writeChartAsPNG(os, chart, 500, 300); \[/java\]

Depois é só fechar o Outputstream e fim! \[java\] os.close(); \[/java\]

Além do apresentado ainda podemos utilizar algumas opções um pouco mais avançadas, como mudar o range dos números (como o gráfico representa valores em porcentagem, o mais correto a se fazer é deixar o limite dos números entre 0 à 100), mudar a cor das barras:

Para ambas modificações temos que que pegar o CategoryPlot do gráfico: \[java\] CategoryPlot plot = chart.getCategoryPlot(); \[/java\]

Mudar o range(limite) para números de 0 à 100: \[java\] plot.getRangeAxis().setRange(0, 100); \[/java\]

Mudar as cores das barras é um pouco mais complicado, mas não se assustem esse pouco mais complicado é bem pouco, do objeto plot você deve pegar o Renderer, mas ele vai devolver um CategoryItemRender, então você deve fazer um cast para o tipo BarRender. Depois o método a ser chamado no renderer é o setSeriesPaint, que vai pintar uma série escolhida, que no caso é a primeira (índice 0) e da cor escolhida. \[java\] BarRenderer renderer = (BarRenderer)plot.getRenderer(); renderer.setSeriesPaint(0, Color.BLUE); \[/java\]

Outra coisa que ainda podemos mudar é a altura do gráfico dinâmicamente, pois a altura vai depender de quantas categorias a prova teve. Assim podemos especificar uma altura mínima, e somar com uma altura multiplicada por quantas categorias a prova teve:

\[java\] int height = dataset.getColumnCount()\*45 + 65; ChartUtilities.writeChartAsPNG(os, chart, 500, height); Obs: Os valores da altura foram obtidos através de testes! \[/java\]

Obs: Os valores da altura foram obtidos através de testes!

Veja o código completo: \[java\] @Viewless public void render() { DefaultCategoryDataset dataset = new DefaultCategoryDataset(); dataset.addValue(mockStatistics.getPercentageOfTotal(), "Acertos (%)", "Geral"); for (int i = 0; i < mockStatistics.getListOfCategories().size(); i++) { dataset.addValue(mockStatistics.getPercentageByCategory(i), "Acertos (%)", mockStatistics .getListOfCategories().get(i).getId().getName()); }

JFreeChart chart = ChartFactory.createBarChart3D("Sua Pontuação", "", "Pontos (%)", dataset, PlotOrientation.HORIZONTAL, true, false, false); CategoryPlot plot = chart.getCategoryPlot(); plot.getRangeAxis().setRange(0, 100); BarRenderer renderer = (BarRenderer)plot.getRenderer(); renderer.setSeriesPaint(0, Color.BLUE); try { response.setContentType("image/png"); OutputStream os = response.getOutputStream(); int height = dataset.getColumnCount()\*45 + 65; ChartUtilities.writeChartAsPNG(os, chart, 500, height); os.close(); } catch (IOException e) { System.err.println("Problem occurred creating chart."); } } \[/java\]
