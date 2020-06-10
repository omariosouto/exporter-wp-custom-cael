---
title: "Enviando mensagens curtas para o Android via C2DM"
date: "2012-06-12"
author: "felipe.torres"
authorEmail: "felipe.torres@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

_Atenção: o Google C2DM não é mais recomendado pelo Google. Há agora o [Google Cloud Messaging for Android (GCM)](http://developer.android.com/guide/google/gcm/index.html), considerado sua nova versão_.

[![](https://blog.caelum.com.br/wp-content/uploads/2500/12/classic_blue_android-150x150.png "classic_blue_android")](https://blog.caelum.com.br/wp-content/uploads/2500/12/classic_blue_android.png) Há um tipo de preocupação que aparece bastante nas turmas do [nosso curso de Android](http://www.caelum.com.br/curso/fj-57-desenvolvimento-google-android/): você precisa enviar uma notificação para um celular que instalou sua aplicação. Quais são as alternativas? Enviar um SMS e capturá-lo via API? Criar um sistema de push notification? Inventar um botão de sincronização? Uma thread que pinga o servidor de tempos em tempos? Todas essas são válidas, mas há uma possibilidade que pode se adequar melhor em certos casos: o **C2DM**, um sistema próprio do Google, criado justamente para o envio de mensagens curtas.

O [C2DM (Cloud to Device Messaging)](https://developers.google.com/android/c2dm/) é um serviço que permite o envio de mensagens de um computador para um celular, previamente cadastrado, por meio da infraestrutura de cloud do Google.

Para utilizá-lo, temos que nos cadastrar no C2DM. Preencha o formulário em [https://developers.google.com/android/c2dm/signup](https://developers.google.com/android/c2dm/signup) com os seus dados. No campo **_package name of your Android app_** guarde o nome que você escrever, pois o usaremos futuramente.

Após o término do cadastro, será enviado um e-mail para o endereço que foi colocado em **_Contact email_**; esse e-mail é um sinal de que o Google aceitou o seu cadastro e que já podemos criar aplicativos para usufruir do C2DM.

Como esse serviço funciona apenas para Android maior ou igual à versão 2.2, no Eclipse, crie um novo projeto com um SDK compatível. No campo _package name_, coloque o nome que foi previamente usado no cadastro. Na sua Activity, coloque o seguinte código para registrarmos nosso aparelho:

\[java\] private void register() { Intent intent = new Intent("com.google.android.c2dm.intent.REGISTER");

intent.putExtra("app", PendingIntent.getBroadcast(<wbr>this, 0, new Intent(), 0)); intent.putExtra("sender", seu-email-usado-no-cadastro-<wbr>do-c2dm);

  startService(intent); } \[/java\]

E de dentro do callback `onCreate`, invoque esse novo método, além de deixar algo visível para nosso teste:

\[java\] public void onCreate(Bundle savedInstanceState) {   super.onCreate(savedInstanceState);

  TextView tv = new TextView(this);   tv.setText("Aplicação usando C2DM!");   setContentView(tv);

  register(); } \[/java\]

Após isso, crie uma nova classe que mostrará o Id do nosso aparelho gerado pelo C2DM, assim que receber uma mensagem do servidor. O action do intent que será passado indicando o registro é "`com.google.android.c2dm.intent.REGISTRATION`":

\[java\] public class C2DM\_RegistrationReceiver extends BroadcastReceiver { public void onReceive(Context context, Intent intent) { String action = intent.getAction();

if ("com.google.android.c2dm.intent.REGISTRATION".equals(action)) {             String registrationId = intent.getStringExtra("registration\_id");      Toast.makeText(context, "O Id recebido é " + registrationId,           Toast.LENGTH\_LONG).show();         }     } } \[/java\]

Agora que podemos cadastrar nosso aparelho, temos que fazer uma classe que reconhecerá uma notificação vinda do C2DM e fará algo com ela. Para isso, crie a seguinte classe:

\[java\] public class C2DM\_MessageReceiver extends BroadcastReceiver { public void onReceive(Context context, Intent intent) { String action = intent.getAction();

if ("com.google.android.c2dm.intent.RECEIVE".equals(action)) { String message = intent.getStringExtra("message"); Toast.makeText(context, message, Toast.LENGTH\_LONG).show(); } } } \[/java\]

Nesse ponto já temos uma aplicação simples que recebe mensagens do C2DM e as exibe na tela, mas ainda não sabemos como enviar uma mensagem por esse serviço. Temos que cadastrar um enviador! Para isso, pela linha de comando, execute o seguinte comando, que criará um enviador vinculado ao seu usuário do Google:

\[code\] curl https://www.google.com/accounts/ClientLogin -d Email=seu\_email\_do\_google \\ -d Passwd=sua\_senha -d accountType=GOOGLE -d source=Google-cURL-Example -d service=ac2dm \[/code\]

Na resposta, guarde com carinho o valor gigantesco que estiver em **Auth**, pois ele será usado para identificá-lo como enviador. Pronto! Para enviar uma mensagem do seu computador para o celular cadastrado, basta usar o seguinte comando pelo terminal:

\[code\] curl --header "Authorization: GoogleLogin auth=hash-do-auth" "https://android.apis.google.com/c2dm/send" \\ -d registration\_id=registration-id-do-device -d "data.message=Minha mensagem aqui." -d collapse\_key=0 -k \[/code\]

Obviamente você não enviará mensagens através do `curl`. Para isso, utilizará alguma biblioteca como o [Apache http client](http://hc.apache.org/httpclient-3.x/).

Para usarmos o C2DM, temos que nos assegurar de que a nossa aplicação satisfaça duas exigências principais:

- Ela não deve enviar mensagens grandes (há um limite de 1024 bytes);
- Nossa aplicação não deve depender da sequência de envio das mensagens;
- O uso ideal do C2DM é enviar um sinal para que sua aplicação efetue uma determinada ação, como, por exemplo, atualizar um arquivo de configuração, uma fonte de dados ou sincronizar suas informações.

Em conjunto com as [classes auxiliares para sincronização de conteúdo](http://developer.android.com/resources/samples/SampleSyncAdapter/index.html), o C2DM facilita muito o nosso trabalho.
