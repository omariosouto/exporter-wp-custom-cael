---
title: "WebStorage: persistência no front-end"
date: "2017-06-06"
author: "jopss.sossoloti"
authorEmail: "jopss.sossoloti@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O mundo front-end está recheado de novas tecnologias para resolver os nossos problemas do dia a dia de forma mais elegante. Falamos bastante aqui sobre a ideia de [aplicativos stateless e tokens](https://blog.caelum.com.br/morte-a-sessao-entenda-esse-tal-de-stateless-session-com-tokens/), e programar com essa abordagem significa deixar a camada cliente mais rica e dinâmica, com possibilidade de armazenar temporariamente alguns informações importantes. Essas informações podem ser desde usuário e token logados até informações de cadastros para aplicativos web offline. Mas, onde e como iremos guardar esses dados?

Existem implementações de APIs que ajudam nessa solução, como visto [neste ótimo artigo](https://www.html5rocks.com/en/tutorials/offline/storage/). Essas libs estão prontas para uso e funcionam até bem. No entanto, há a solução nativa implementada pelos navegadores mais atuais (e mobiles) que possibilita persistir dados no próprio navegador: o WebStorage. Com isso, não precisaríamos adicionar nenhuma lib ao nosso projeto para usufruir dessa opção de uso. A ideia aqui neste post é mostrar o uso nativo do WebStorage com exemplo de código para leitura.

Contudo, usar o WebStorage possui algumas limitações:

- Armazenamento somente de Strings (incluindo Strings vazias).
- Certa lentidão pela necessidade de parsear objetos JSON para Strings (caso necessário).
- Navegadores sem suporte a HTML5 não funcionam.
- Limite de armazenamento, depende do navegador. A média é de 2.5mb a 5 mb.

Dentro do WebStorage existem duas opções de implementação: o localstorage e o sessionstorage. A diferença entre eles é que no SessionStorage os dados são perdidos ao fechar o navegador, e já no LocalStorage eles permanecem mesmo reabrindo o browser.

#### Cadê o código?

Pelo JavaScript, há disponível o objeto window.localStorage para guardarmos qualquer dado que precisamos. Ele é baseado na ideia de chave-valor, então:

\[code language="javascript"\] var objetoQualquer = {nome:'bla', documento:'123'}; window.localStorage\['chavePersistida'\] = JSON.stringify(objetoQualquer); \[/code\]

E para retornar:

\[code language="javascript"\] var valorString = window.localStorage\['chavePersistida'\]; var objetoQualquer = JSON.parse(valorString); console.log( 'Nome: '+objetoQualquer.nome ); console.log( 'Documento: '+objetoQualquer.documento ); \[/code\]

Abra o console do seu navegador e tente executar os comandos acima. Veja que como ele guarda somente textos, usamos o JSON para transformação entre objetos e Strings. No entanto, você pode parsear um array de objetos mais complexos, e assim, simular uma base de dados na camada front-end.

#### Eu queria simular dados mais complexos

Na tentativa de simular um banco de dados de objetos no JavaScript, criei uma generalização do WebStorage [disponível neste GitHub](https://github.com/jopss/JPStorage). Leia a implementação, ela está um tanto quanto simples, mas funcional para este propósito. Note que o objetivo de armazenar dados no client-side é dar suporte a funcionalidades ricas no cliente, mas nunca espelhar regras de negócio do seu cliente. O back-end sempre será a melhor solução em termos de segurança e independência quanto a validação de dados e regras de negócios. Guardar dados sensíveis no cliente pode ser um problema para a sua aplicação, não faça isso!

Foi criado e também disponível no GitHub um Service AngularJS para facilitar o uso do WebStorage. Assim, os exemplos abaixo usarão esta tecnologia.

Imagine um CRUD de cliente com os campos "nome" e "documento". Em AngularJS, existirá um controller para ela. Para efeito do exemplo, irei juntar no mesmo controller tanto as funcionalidades de cadastro e de listagem. Segue os trechos importantes:

\[code language="javascript"\] app.controller("ClienteController", function ($window, Cliente) { var ctrl = this;

//... ctrl.remover = function(id) { Cliente.remover(id).then(function (result) { addMensagemRetorno(ctrl, result); ctrl.lista = result.lista;

}, function (result) { / }); };

ctrl.gravar = function() { Cliente.gravar(ctrl.form).then(function (result) { addMensagemRetorno(ctrl, result); ctrl.irParaListagem(); }, function (result) { addMensagemValidacao(ctrl, result); }); }; //... ctrl.init = function () { Cliente.listarTodos().then(function (result) { ctrl.lista = result.lista; }, function (result) { addMensagemValidacao(ctrl, result); }); }; ctrl.init(); }); \[/code\]

Veja que este controller acessa um Service chamado Cliente. Esse Service é que encapsula o acesso aos dados do Cliente. Em um sistema normal, ele iria ao servidor enviar e buscar JSONs. Mas aqui, iremos simular este acesso ao backend com o uso do WebStorage.

\[code language="javascript"\] app.service("Cliente", \["JPStorage", function (JPStorage) { this.nome = "Cliente"; //identificacao obrigatoria var dadosIniciaisCliente = \[ { id: 1, nome: 'Nome do Cliente Exemplo 01', documento: '12345678900' }, { id: 2, nome: 'Nome do Cliente Exemplo 02', documento: '98765432100', } \]; JPStorage.prepararBaseStorage(this, dadosIniciaisCliente);

this.gravar = function (form) { return JPStorage.atualizarObjetoStorage(this, form); }

this.remover = function (id) { return JPStorage.removerObjetoStorage(this, id); }

this.buscarPorId = function (id) { return JPStorage.buscarObjetoPorIdStorage(this, id); }

this.listarTodos = function () { return JPStorage.buscarTodosStorage(this); }

this.filtrarPorNomeOuDocumento = function(form){ return JPStorage.listarPorFiltrosStorage(this, {'nome':\[form.pesquisa,'LIKE'\], 'documento':\[form.pesquisa,'EQUALS'\]}); } } \]); \[/code\]

O objeto JPStorage possui funções básicas de CRUD para guardar, remover, retornar e filtrar objetos, encapsulando internamente o parse de objeto para texto, o acesso ao LocalStorage e simulando a estrutura de retorno de um "promise". Assim, o seu uso se parece muito com acesso real ao servidor como $http ou Restangular. Por fim, para usar esse exemplo, deve-se registrar o modulo JPStorageModule na raiz do seu app.js.

Note que inicialmente criamos alguns dados de exemplo para popular o "banco" de clientes no storage. Como o Service do AngularJS é um singleton, ele somente irá criar esses dados no primeiro acesso ao Service. Por fim, veja que temos a possibilidade de filtrar dados simulando um "where" de consultas simples.

#### Mas qual o uso disso?

Bom, um dos usos para WebStorage é não quebrar seu aplicativo web caso a conexão do usuário caia, fazendo-o parecer um aplicativo offline (standalone). Isso funciona bem, mas é necessário pensar em estratégias de sincronia dos dados do servidor com o que está no LocalStorage. Outro uso que pode ser comum é o cache temporario de algum dado importante para melhoria de performance. Imagina um relatório pesado ou um dashboard com gráficos administrativos que não necessitam de atualização em tempo real.

Outra possibilidade de uso pode ser em relação a criação de MVP de um produto. Imagine que você precisa provar uma ideia de produto em um curto espaço de tempo, sendo que depois da avaliação e feedback, este aplicativo irá morrer. Hoje em dia, criar um back-end pode até ser simples, mas organizar banco de dados, acesso REST, configurações, etc, pode demorar um tempo (que talvez você não tenha). Com o uso dessa abordagem, pode-se criar um MVP simples persistindo os dados locais no WebStorage.

Uma outra opção seria a criação de mockups de tela para validação simples ao cliente, tudo meio funcional e com tecnologias Front-end.

#### E os Cookies?

Você pode estar se perguntando porque não usamos o bom e velho Cookie para guardar os dados. Pode sim ser usado, mas são abordagens distintas e resolvem problemas distintos. Por exemplo, caso você queira que os dados não se misturem entre abas do próprio navegador, não funcionará com Cookie, já que ele é compartilhado, diferente da implementação SessionStorage. Já em comparação com o LocalStorage (que também é compartilhado), o Cookie sempre troca dados entre o cliente e servidor a toda Request, enquanto que os dados no LocalStorage permanecerão somente no navegador (esse dado nunca irá ao servidor).

Então, qual deve-se usar, depende do que você quer implementar e da solução a ser resolvida. Mas diz aí, você costuma usar WebStorage? Para qual tipo de abordagem?

E não deixe de conferir nossas [Trilhas de Front-end no Alura](https://www.alura.com.br/cursos-online-front-end) e dos [Cursos Presencias na Caelum](https://www.caelum.com.br/).
