---
title: "Inteligência artificial na educação"
date: "2012-07-11"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Como detectar se um aluno terá dificuldades no andamento de seu curso e interferir para ajudá-lo? Como conectar alunos de níveis semelhantes para que aprendam junto?

Desde que a Caelum decidiu enfrentar o desafio de [criar uma plataforma online](http://www.caelum.com.br/online) nos perguntamos como poder ajudar o aluno de maneiras diferentes. Desde então participamos de diversos cursos online nas mais variadas plataformas como os disponibilizados por Stanford através do [Coursera](http://www.coursera.org).

Após [estudar o curso de Machine Learning](https://www.coursera.org/course/ml) do instrutor [Andrew Ng, um dos responsáveis pelo resultado de auto-detecção de faces e gatos do Google](http://research.google.com/archive/unsupervised_icml2012.html), participamos do [Intelligent Tutor Systems 2012](http://its2012.teicrete.gr/) na Grécia, onde discutimos abordagens de AI para a educação com equipes especializadas na área, em especial com desenvolvedores das plataformas de Carnegie Mellow e MIT.

Diversas das perguntas citadas anteriormente podem ser resolvidas com algoritmos de AI e muitas delas [são utilizadas faz muito tempo](http://echo.edres.org:8080/irt/baker/).

Tomando como exemplo a deteção de aluno que está ficando atrasado no curso, temos os dados de quanto tempo cada aluno gasta para fazer um exercício:

Paulo: exercício 1, 12 minutos. exercício 2, 150 minutos. exercício 3, 3 minutos. exercicio 4, 15 minutos. Ana: exercício 1, 4 minutos. exercício 2, 170 minutos. exercício 3, 32 minutos. exercicio 4, 25 minutos. Maria: exercício 1, 1 minuto. exercício 2, 10 minutos. exercício 3, 3 minutos. exercicio 4, 25 minutos. Carlos: exercício 1, 12 minutos. exercício 2, 20 minutos. exercício 3, 31 minutos. exercicio 4, 15 minutos.

E sabemos que o Paulo e a Ana demoraram mais do que o tempo desejado para obter aproveitamento máximo do conteúdo apresentado. Isto é, classificamos eles como sendo alunos que atrasaram mais de 3 meses e portanto não aproveitaram tanto o curso.

Podemos representar esses dados através de uma matriz onde cada linha é um aluno, as 4 primeiras colunas o tempo gasto. Em Ruby, teríamos algo como:

\[ruby\] alunos = \[\] alunos << \[12, 150, 3, 15\] alunos << \[4, 170, 32, 25\] alunos << \[1, 10, 3, 25\] alunos << \[12, 20, 31, 15\] \[/ruby\]

Podemos também definir zero como sendo a classificação de quem atrasou e gostaríamos de motivá-lo, e um como quem não atrasou. Como temos que os dois primeiros não atrasaram e os dois últimos atrasaram, podemos dar um label para cada um deles:

\[ruby\] labels = \[1, 1, 0, 0\] \[/ruby\]

Note que cada aluno fez muito mais de 4 exercícios para acabar o curso, mas só estou analisando os 4 primeiros exercícios. Com essas informações e um novo aluno (Guilherme), como poderíamos dizer se o Guilherme terá dificuldades ou não? Existem diversos algoritmos e técnicas que podem ser utilizados. Nesse simples exemplo veremos a aplicação de [Support Vector Machines](http://en.wikipedia.org/wiki/Support_vector_machine) através da [libsvm](http://www.csie.ntu.edu.tw/~cjlin/libsvm/) em Ruby (o código original é em C e portanto para dezenas de diferentes linguagens):

\[ruby\] require 'libsvm' problem = Libsvm::Problem.new problem.set\_examples(labels, alunos.map {|ary| Libsvm::Node.features(ary) }) \[/ruby\]

Configuramos os parâmetros do algoritmo, que no nosso caso não estudaremos mas em uma aplicação real é importante encontrar parâmetros que otimizem seu aprendizado:

\[ruby\] parameter = Libsvm::SvmParameter.new parameter.cache\_size = 4 parameter.eps = 0.001 parameter.c = 1 \[/ruby\]

Por fim, pedimos para o algoritmo ser treinado e criar um modelo de como os estudantes funcionam:

\[ruby\] model = Libsvm::Model.train(problem, parameter) \[/ruby\]

E agora dado o Guilherme:

\[ruby\] guilherme = \[ 6, 140, 25, 10 \] \[/ruby\]

O algoritmo é capaz de prever:

\[ruby\] prediction = model.predict(Libsvm::Node.features(guilherme)) puts prediction \[/ruby\]

E o resultado é 1, isto é, o Guilherme vai atrasar no curso e é importante avisá-lo para que não perca o ritmo. Claro, tudo isso aconteceu com uma base de dados muito pequena e irreal. Note que o segundo exercício é visualmente um indicador se o aluno vai ou não atrasar: acima de 100, todos atrasaram! O código Octave abaixo gera um gráfico que fica mais fácil visualizar:

\[code\] X = \[12,150,3,15,1;4,170,32,25,1;1,10,3,25,0;12,20,31,15,0\]; y = \[1 1 0 0\]'; primeiro = X(:,1); segundo = X(:,2); terceiro = X(:,3);

plot3(primeiro, segundo, terceiro, '^'); \[/code\]

O gráfico a seguir mostra essas três primeiras dimensões do exercício:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/07/dimensoes_1-300x219.png "dimensoes_1")](https://blog.caelum.com.br/wp-content/uploads/2012/07/dimensoes1.png)

Já o gráfico a seguir tenta "reduzir" os 4 exercícios em 3 (usando PCA), para que possamos "visualizar" todos os 4 exercícios em 3 dimensões, para tentar detectar um padrão:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/07/dimensoes_3-300x219.png "dimensoes_3")](https://blog.caelum.com.br/wp-content/uploads/2012/07/dimensoes_3.png)

Pode ser que o algoritmo tenha pego essa regra, que até mesmo um ser humano detecta rapidamente. O que aconteceria com dados reais?

     2.81670     7.40000     0.63330     0
     0.55000    21.65000     4.76670     0
     1.25000    53.36670    43.05000     1
     1.20000    36.10000    12.00000     0
     0.83330   180.00000    22.41670     1
     1.06670   180.00000   180.00000     0
     1.33330    95.26670    42.83330     0
     2.06670   180.00000    23.13330     0
     5.51670   180.00000    10.78330     1
   180.00000   180.00000    12.65000     1

Esses são alguns dados reais de 3 exercícios de um curso. Note como não existe uma regra visual tão fácil que indique se o aluno terá ou não dificuldade para continuar o curso em um andamento bom para seu aprendizado:

[![](https://blog.caelum.com.br/wp-content/uploads/2012/07/dimensoes_2-300x226.png "dimensoes_2")](https://blog.caelum.com.br/wp-content/uploads/2012/07/dimensoes_2.png)

O aluno `[1.06670 180 180]` demorou muito nos dois exercícios mas pode ser que ele fez uma viagem no fim de semana e por isso não respondeu naquela hora, mas mesmo assim conseguiu terminar o curso a tempo.

Treinando o SVM com `C=0.08` o algoritmo é capaz de detectar um padrão muito mais complexo. Mas mesmo assim o algoritmo é capaz de prever com exatidão 86% dos casos.

C utilizado        ACERTOS NO TREINO    PREVISOES COM ACERTO
0.08	           93.18%	        85.71%

Por fim, ao fazer o fine tuning do algoritmo, encontramos funções capazes de detectar com muita exatidão se o aluno terá dificuldade em completar o curso a tempo.

C utilizado		ACERTOS NO TREINO	PREVISOES COM ACERTO
0.001			70.45454545454545	61.904761904761905
0.04			88.63636363636364	71.42857142857143
0.08			93.18181818181819	85.71428571428571
0.2			100.0			100.0

Essa implementação básica de SVM em Ruby é capaz de ir além prevendo o quão difícil será um exercício para um aluno, mas na prática utilizamos Octave, uma alternativa que balanceia melhor a simplifidade do código e o desempenho de algoritmos matemáticos como esse.
