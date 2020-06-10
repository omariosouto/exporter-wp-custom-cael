---
title: "Física em jogos com Chipmunk2D"
date: "2014-05-07"
author: "vitor.mattos"
authorEmail: "vitor.mattos@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Uma dúvida de muitos que iniciam no desenvolvimento de jogos é: "Como farei para simular a física?" Lidar com física nunca é uma tarefa simples. Temos que considerar gravidade, como ela afeta os corpos presentes no espaço, colisão entre os corpos, força de impulso, formas geométricas etc.

![chipmunk](https://blog.caelum.com.br/wp-content/uploads/2014/12/chipmunk-300x98.png)

Para que o desenvolvedor não precise se preocupar com todos esses aspectos, diversas engines de física para jogos foram criados. Veremos o quão fácil é trabalhar com a engine [Chipmunk2D](https://github.com/slembcke/Chipmunk2D "Github do projeto Chipmunk2D"), usando o framework [Cocos2d-JS](https://github.com/cocos2d/cocos2d-js "Github do projeto cocos2d-js") para criar nosso jogo! Você pode conferir o resultado final do que faremos [nesse link](http://caelumsoccer.herokuapp.com/game/).

[![jogo](https://blog.caelum.com.br/wp-content/uploads/2014/12/jogo-290x300.png)](http://caelumsoccer.herokuapp.com/game/)

Diversos tutoriais básicos sobre Cocos2d podem ser encontrados na [página da documentação](http://www.cocos2d-x.org/wiki/Cocos2d-JS) e o download está disponível [aqui](http://www.cocos2d-x.org/download "Cocos2d Download").

Para começarmos a usar a engine de física do Chipmunk2D, temos que declarar nas configurações do Cocos2d-JS que usaremos o _chipmunk_. Para isso, alteramos o arquivo **cocos2d.js** na pasta `cocos2d` e adicionamos a seguinte instrução:

\[code language="javascript"\] chipmunk:true, \[/code\]

Agora toda vez que carregarmos o Cocos2d, o jsloader carregará também a biblioteca do Chipmunk2D.

Para de fato usarmos a física em uma cena, precisamos de um objeto chamado **Space**. Nesse objeto definiremos todas as regras de física do nosso "espaço", como a gravidade, por exemplo. É comum criarmos um método (_initPhysics_) na cena (_Scene_) que iniciará todas as configurações de física:

\[code language="javascript"\] var PhysicScene = cc.Scene.extend({ space: null,

initPhysics:function() { //criação do objeto Space this.space = new cp.Space(); },

onEnter:function () { this.\_super(); this.initPhysics(); var layer = new GameLayer(this.space); this.addChild(layer);

this.scheduleUpdate(); }, update: function(dt) { this.space.step(dt); } }); \[/code\]

Pronto! Agora já temos um objeto representando o espaço. Um vez criado podemos definir a força da gravidade nele. Para tal, o objeto _space_ possui uma propriedade _gravity_ que recebe um vetor no plano cartesiano:

\[code language="javascript"\] //dentro da função initPhysics, logo após ter criado o space this.space.gravity = cp.v(0, -650); \[/code\]

O próximo passo é criar um **GameLayer**. Nesse _Layer_ (ou camada da cena) acontecerão todas as animações. É a camada principal que junta todos os elementos do jogo.

O **GameLayer** recebe o objeto _space_ criado anteriormente como parâmetro no construtor. O código seguinte initializa o **GameLayer**:

\[code language="javascript"\] var GameLayer = cc.Layer.extend({

ctor : function(space){ this.\_super(); this.space = space; this.init(); },

init:function(){ this.\_super(); //aqui vamos fazer mais coisas ainda }, }); \[/code\]

Vamos simular um corpo sendo afetado pela gravidade em nosso espaço. Representaremos esse corpo com o **sprite** de uma bola que fica salvo dentro de um diretório especial chamado _res/Normal/_:

![ball](https://blog.caelum.com.br/wp-content/uploads/2014/12/ball.png)

Para a simulação, no **GameLayer** dentro da função _init_ criaremos o sprite da bola e o associaremos a um corpo físico:

\[code language="javascript"\] init:function(){ this.\_super();

// cc.PhysicsSprite cria um sprite que representará um corpo var bola = cc.PhysicsSprite.create("ball.png"); this.addChild(bola);

var raio = 32;

// Criando o corpo físico que obedecerá as leis do espaço var corpo = this.space.addBody(new cp.Body(10, cp.momentForCircle(10, 0, raio, cp.v(0,0))));

// Podemos escolher a forma do corpo, a elasticidade e a fricção dele! forma = this.space.addShape(new cp.CircleShape(corpo, raio, cp.v(0,0))); forma.setElasticity(0.6); forma.setFriction(1);

var s = cc.Director.getInstance().getWinSize(); corpo.setPos(cp.v(s.width/2, s.height/2));

bola.setBody(corpo) }, \[/code\]

Rode a aplicação e verifique o resultado. A bola está passando desaparecendo tela! Precisamos criar um chão para que ela permaneça na área visível.

Vamos criar 4 paredes, uma em cada lado da tela, transformando a área jogável em uma espécie de "caixa". No método **initPhysics**, que está no `PhysicScene`:

\[code language="javascript"\] var winSize = cc.Director.getInstance().getWinSize();

var staticBody = this.space.staticBody;

var paredes = \[ new cp.SegmentShape(staticBody, cp.v(0,0), cp.v(winSize.width,0), 0 ), // bottom

new cp.SegmentShape(staticBody, cp.v(0,winSize.height), cp.v(winSize.width,winSize.height), 0), // top

new cp.SegmentShape(staticBody, cp.v(0,0), cp.v(0,winSize.height), 0), // left

new cp.SegmentShape(staticBody, cp.v(winSize.width,0), cp.v(winSize.width,winSize.height), 0) // right \];

for( var i=0; i < paredes.length; i++ ) { var forma = paredes\[i\]; forma.setElasticity(1); forma.setFriction(1); this.space.addStaticShape( forma ); } \[/code\]

Agora nossa bola bate no chão e quica! Podemos facilmente aplicar um impulso ao corpo. Na função _init_ do _GameLayer_:

\[code language="javascript"\] var forcaX = -4000; var forcaY = 3000;

corpo.applyImpulse(cp.v(forcaX, forcaY), cp.v(0, 0)); \[/code\]

Agora a bola começa já com um impulso. O primeiro parâmetro da função _applyImpulse_ é um vetor com a direção do impulso. O segundo parâmetro é um _offset_ indicando (a partir do centro do corpo) onde o impulso será aplicado. Poderíamos usar valores diferentes de 0 se quiséssemos causar uma rotação na bola.

Vimos que é muito simples simularmos física em nossos jogos do Cocos2d-html5 usando a engine Chipmunk2D. Só precisamos de um objeto _space_, onde podemos configurar, por exemplo, a gravidade. E nele teremos os corpos que serão afetados pela física.

Falta mencionar que Cocos2d já vem com o Cocos2d-html5 e o Cocos2d-x Javascript Binding, para podermos compilar nosso código para rodar nativamente em dispositivos mobile. Além disso, a biblioteca do Cocos2d também possui suporte para outras engines de física, como por exemplo o [Box2D](http://box2d.org/).

O código completo desse projeto pode ser encontrado no [Github](https://github.com/vmattos/chipmunk-physics) e se quiser saber mais sobre Cocos2d-html5, não deixe de visitar a [MobileConf RJ 2014](http://www.mobileconf.com.br/)!
