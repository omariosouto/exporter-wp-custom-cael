---
title: "Invocando métodos assíncronos com Spring"
date: "2017-08-02"
author: "jopss.sossoloti"
authorEmail: "jopss.sossoloti@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

O ecossistema Spring é muito usado por várias aplicações Java mundo afora e uma das features interessantes desse framework é a possibilidade de criarmos chamadas assíncronas entre nossos métodos. O [tutorial do site do Spring](https://spring.io/guides/gs/async-method/) é bem explicativo, mas vamos aqui dar um exemplo mais real que passei em um dos projetos em que trabalho.

Por mais que um sistema tende a ser automatizado existe a necessidade de importar ou exportar arquivos para integrações, e algumas vezes, estes arquivos são muito grandes e pesados, levando minutos para processamento. Ao criar uma tela de upload, o usuário teria que ficar esperando esses minutos sem sair da tela para poder ver o resultado da importação. Com as chamadas assíncronas podemos processá-los em paralelo, exibindo uma mensagem independente de onde ele esteja no sistema.

Outro ponto de atenção é que se você configurou no seu sistema o [OpenEntityManagerInViewFilter](https://blog.caelum.com.br/enfrentando-a-lazyinitializationexception-no-hibernate/) e acessar uma funcionalidade mais demorada, independente do que seja, pode ocasionar timeout de transação. Criar essa chamada de forma assíncrona resolveria esse tipo de problema.

### Configurações

A configuração para usar métodos assíncronos depende de um TaskExecutor. Esse executor é um cron Quartz interno do Spring e pode ser configurado pelo SpringBoot ou por xml. Por padrão, o Spring cria uma instancia da classe SimpleAsyncTaskExecutor, que é um executor que não cria um pool de thread, e assim, cada ação assíncrona cria uma nova thread no seu sistema. Mas, podemos configurar para usar o ThreadPoolTaskExecutor, que como o nome diz, cria o pool.

A diferenciação qual dessas duas classes usar depende de um único parâmetro. Configurando assim:

\[code language="xml"\] <task:executor id="executorSimples"/> <task:annotation-driven executor="executorSimples"/> \[/code\]

Criarmos o TaskExecutor implementado pela classe SimpleAsyncTaskExecutor. Agora, se usarmos assim:

\[code language="xml"\] <task:executor id="executorComPool" <strong>pool-size="5" queue-capacity="500"</strong>/> <task:annotation-driven executor="executorComPool"/> \[/code\]

Configuramos implementado pelo ThreadPoolTaskExecutor. De prontidão, reaproveitar threads com pool seria a melhor opção. Caso esteja usando SpringBoot, a configuração ficaria dessa forma:

\[code language="java"\] @SpringBootApplication @EnableAsync //... public class Application {

//...

@Bean public Executor asyncExecutor() { ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor(); executor.setCorePoolSize(2); executor.setMaxPoolSize(5); executor.setQueueCapacity(500); executor.initialize(); return executor; } } \[/code\]

Uma última configuração necessária é a anotação @Async no método do seu @Service que você queira que seja invocado assincronamente. Esse método pode ter qualquer tipo de parâmetro, mas seu retorno deverá ser uma implementação da interface Future, mais precisamente, a classe CompletableFuture. Essa classe é utilizada para criar funcionalidades reativas em java, onde a [execução assíncrona das regras](http://www.baeldung.com/java-completablefuture) é feita por uma Thread paralela, não bloqueando a execução principal. O método principal dessa classe é o "get()", que aguarda até as regras serem executadas para poder retornar o resultado.

Veremos como montar um método para ser chamado assincronamente.

\[code language="java"\] @Async public CompletableFuture<String> metodoAssincrono(String parametro1, Integer parametro2) { //...

return CompletableFuture.completedFuture("Bla"); } \[/code\]

Ao invocar esse método você já irá receber a resposta instantaneamente, pois sua execução foi iniciada por uma outra Thread. O retorno CompletableFuture é um promisse, uma promessa de retorno, e você pode verificar por ele se a execução já foi concluída ou se ainda esta em andamento. Caso já tenha sido concluída, é possível pegar o conteúdo configurado como retorno, no meu caso, o texto "Bla". Caso ainda esteja em andamento, pode-se aguardar a sua conclusão.

Um último ponto é que esta configuração contem dentro do jar core do Spring. Então, basta a configuração de jars padrão no seu pom.xml que já provê essa possibilidade.

### Carregando um arquivo grande

Com a possibilidade de execução assíncrona, criei um exemplo de importação de um arquivo contendo milhões de dados. Nesse exemplo teremos a opção de execução sincronizada, como qualquer ação normal, e outra para execução assíncrona, tratado neste post.

Pensando em um sistema web, quando criamos chamadas assincronas, a verificação do estado da execução, ou seja, se está em andamento ou já concluída, deve ser feito periodicamente. Podemos criar uma conexão Websocket para enviar o aviso de conclusão ao usuário da aplicação, ou de forma bem simples, mas não tão performática, criar um pooling javascript, onde o navegador fica "perguntando" ao servidor se já está concluído. Para fins deste exemplo, usarei o pooling, mas em uma aplicação real, considere o Websocket.

No controlador, teremos os seguintes métodos:

\[code language="java"\] @Controller @RequestMapping(value = "/pessoa/exec") public class PessoaController { @Autowired private ImportacaoService importacaoService; //... @RequestMapping(value = "/assinc", method = RequestMethod.GET) public String requisicaoAsync(Model m, HttpSession session){

CompletableFuture<AsyncForm> promisse = importacaoService.importarAsync(); this.addSessaoPromisse(session, promisse); return "pagina-assinc"; }

@ResponseBody @RequestMapping(value = "/assinc/verificar", method = RequestMethod.GET) public AsyncForm verificarAsync(HttpSession session) throws InterruptedException, ExecutionException {

CompletableFuture<AsyncForm> promisse = this.getSessaoPromisse(session); if (promisse == null) { return null; } else { return promisse.getNow(new AsyncForm(this.getSessaoLog(session))); } }

private void addSessaoPromisse(HttpSession session, CompletableFuture<AsyncForm> promisse) {

session.setAttribute("FUTURE\_IMPORTACAO", promisse); }

private CompletableFuture<AsyncForm> getSessaoPromisse(HttpSession session) {

return (CompletableFuture<AsyncForm>) session.getAttribute("FUTURE\_IMPORTACAO"); }

//... } \[/code\]

A classe **ImportacaoService** é onde temos o nosso método assincrono, chamado pelo **requisicaoAsync** do controlador. Essa execução para o controller é instantânea: ele não aguarda a invocação ser finalizada e já faz o response da requisição. No nosso exemplo, além da execução assincrona, eu quero poder avaliar a conclusão com o pooling javascript. No entanto, como uma request não conhece a anterior, a requisição do pooling não teria acesso ao CompletableFuture para validar o resultado. Dessa forma, o que faço aqui é guardar o promisse na sessão do meu usuário corrente, para futura verificação da conclusão, que é feita pelo método **verificarAsync**. Este faz um retorno JSON para a chamada Ajax do meu html.

O método **getNow** do **Future** tem a caracteristica de retornar o conteúdo da ação, caso ela tenha sido concluída, mas caso não, ele retorna um padrão. Esse padrão foi criado no parâmetro do método **getNow**. Existe ainda um outro método chamado somente **get**, que trava e espera até que a conclusão aconteça, e um outro **isDone** para verificar se a execução já foi concluída ou não.

Agora veja um trecho do Service.

\[code language="java"\] @Async public CompletableFuture<AsyncForm> importarAsync() { List<Pessoa> pessoas = this.importarArquivoCSVGrande(); for(Pessoa p : pessoas){ this.pessoaServico.gravar(p); } AsyncForm form = new AsyncForm(); form.addLog("Importado "+pessoas.size()+" itens."); form.setPessoas(pessoas);

return CompletableFuture.completedFuture(form); } \[/code\]

O método **importarAsync** será executado por uma Thread separada da execução principal, onde o promisse **CompletableFuture** terá o resultado após a conclusão.  Com isso, o tempo de importação e gravação, que pode demorar minutos, não interrompe o usuário de navegar pelo sistema, e ele será devidamente avisado da conclusão. Para ver o código mais detalhado, acesse [este GitHub](https://github.com/jopss/spring-async).

Mas diz aí, já precisou de alguma chamada assincrona no servidor? Não deixe de conferir [nossas turmas presenciais de Java e Web](https://www.caelum.com.br/todos-os-cursos).
