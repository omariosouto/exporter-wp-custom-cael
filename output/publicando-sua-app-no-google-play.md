---
title: "Publicando sua app no Google Play"
date: "2016-06-21"
author: "andre.verri"
authorEmail: "andre.verri@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Trabalhando com Android, a gente costuma gastar um bom tempo de desenvolvimento preocupados com o funcionamento da app. Seja desenvolvendo a lógica, produzindo o melhor layout para os diferentes dispositivos ou tratando as condições de uso, como nosso usuário estar com algum serviço do celular desabilitado.

É uma grande satisfação ver a app pronta, funcionando no seu device. O próximo passo é garantir que outras pessoas possam usá-la também.

Para isso, basta que você disponibilize seu `.apk` na web, de modo que possa ser baixado por outras pessoas. Entretanto, para garantir algum controle de qualidade, segurança e até mesmo conteúdo impróprio, o Google criou o Google Play, o principal marketplace do universo Android.

A ferramenta criada para esse fim é o Google Play Developer Console que pode ser acessado pela url [https://play.google.com/apps/publish/](https://play.google.com/apps/publish/). Ao acessar essa url pela primeira vez, pede-se que façamos o nosso registro, que é dividido em poucos passos:

- Fazer login numa conta Google
- Ler e aceitar o [Contrato de distribuição do desenvolvedor do Google Play](https://play.google.com/about/developer-distribution-agreement.html)
- Pagar a taxa de registro no valor de 25 dólares. Essa taxa só precisa ser paga uma vez, e o registro tem duração indeterminada.

Depois desse passo, entramos na página principal do Developer Console. Podemos começar a configurar nossa publicação clicando no botão “Adicionar novo app”. No popup que surgir, devem ser definidos o idioma padrão e o título.

![Adicionar novo app](https://blog.caelum.com.br/wp-content/uploads/2016/06/novo_app.png)

Existem duas opções para terminar de criar sua app: Fazer o upload do .apk agora com a opção "Enviar APK", ou deixar pra fazer mais tarde clicando em "Preparar lista na Play Store". Não importa qual opção você escolha, o Developer Console ainda não vai publicar sua app, vai apenas criar um “draft” (rascunho) da sua publicação.

Para publicar basta clicar no botão “Publicar app” no canto direito, mas nesse momento o botão está desabilitado. Isso porque o Google exige que uma série de informações sejam passadas, como em quais países o aplicativo irá circular e até a classificação indicativa.

O bom é que logo acima desse botão tem um link “Porque não posso publicar?” que lista todos os detalhes que faltam.

A primeira coisa que o Google exige é o .apk . Esse talvez seja o passo mais difícil, pois esse .apk precisa ser preparado para publicação, que deixa ele um pouco diferente do .apk de desenvolvimento.

 

**Preparando sua app**

Para começar, o Google sugere que a gente escolha um bom nome de package. Se você não se preocupou muito com isso quando começou a desenvolver, essa é a hora de pensar um pouco nele. Uma vez publicada a app, não podemos mais mudar o nome do package, pois o Google Play associa o nome do package com a publicação.

Em seguida, temos que desativar o log e o debug da app. Desativamos o log tirando do código toda chamada de métodos da classe `Log`. Para desativar o debug, basta mudar o valor do atributo `android:debuggable` para `false` no AndroidManifest.xml (o valor default dele é false, então se ele não existir já está tudo certo).

Falando em AndroidManifest.xml, tem mais algumas coisas a fazer por lá. Uma é revisar se as permissões da sua app estão corretas. Outra é conferir os atributos `android:icon` e `android:label`, que representam o ícone que aparece no device das pessoas que instalarem o sua app e o título que vai aparecer junto do ícone.

O próximo passo é versionar sua app. Existem dois atributos na tag `manifest` do AndroidManifest.xml, o `versionCode` e `versionName`.

O `versionCode` é um número inteiro usado pelo o sistema interno para saber se uma versão é mais recente que a outra. A norma é publicar uma app pela primeira vez com o valor 1, e somar 1 a cada nova versão lançada.

Já o `versionName` é o nome público da versão, que pode ser o que você quiser, como um nome ou um número. Por exemplo, as versões do Android têm nomes de doces (Android **Lollipop**), enquanto o Ubuntu é versionado por número (Ubuntu **14.04**).

![AndroidManifest.xml](https://blog.caelum.com.br/wp-content/uploads/2016/06/androidmanifest.png)

 

**Gerando um .apk para publicação**

Uma vez que todas essas mudanças no código estão prontas, está na hora de gerar o .apk que vai ser disponibilizado para os usuários baixarem.

Para garantir a autenticidade do .apk, o Google Play exige que ele seja assinado por uma dupla de chaves pública-privada. O resultado é como se você gerasse um certificado digital para sua app.

A chave é um hash guardado num arquivo com a extensão .keystore. Tudo pode ser gerado pela ferramenta `keytool`. Para gerar essa chave, usamos o comando abaixo no terminal, modificando o \[nomeDoArquivo\] pelo nome que você quer no seu .keystore e o \[apelido\] por um nome que você queira usar para se referenciar a essa certificação digital:

`$ keytool -genkey -v -keystore [nomeDoArquivo].keystore -alias [apelido] -keyalg RSA -keysize 2048 -validity 10000`

Ele então vai pedir que você defina uma senha para acessar o arquivo, a **keystore password** e a confirme em seguida. Depois, ele pede várias informações como nome, empresa e local. Essas informações são opcionais e não afetam o nível de segurança do seu certificado.

No fim ele vai pedir outra senha, o **key password**. Essa senha é para o acesso à chave dentro do arquivo.

\[caption id="attachment\_7731" align="aligncenter" width="762"\]![Keytool](https://blog.caelum.com.br/wp-content/uploads/2016/06/teminal_keytool_gen.png) Exemplo de geração do .keystore com o keytool\[/caption\]

Com o certificado pronto, falta gerar nossa .apk certificada. O Android Studio permite fazer isso no menu Build > Generate Signed APK

Na janela que aparece, temos que passar as informações do certificado: o arquivo .keystore que contém a chave, o **key store password** para poder acessar o arquivo. O **key alias** é o apelido que você deu para o certificado e o **key password** que é a senha da chave. Depois clique em “Next”

![Generate Signed APK](https://blog.caelum.com.br/wp-content/uploads/2016/06/signed_apk.png)

Na próxima janela, definimos a pasta onde queremos criar o arquivo .apk e escolhemos o "Build Type". Como queremos criar uma .apk para publicação, selecione a opção “release”.

Pronto, seu .apk está pronto para ser enviado para o Google Play. Basta abrir a página "APK" no Developer Console e clicar no botão "Enviar o primeiro APK para produção".

![Enviar o primeiro APK para produção](https://blog.caelum.com.br/wp-content/uploads/2016/06/enviar_primerio_apk-1024x514.png)

 

**Detalhes da App**

Na página “detalhes do app”, precisamos definir as informações que vão aparecer na página do sua app na Google Play. O **título**, uma **breve descrição** e uma **descrição completa** tem que ser informados para cada idioma que você quer dar suporte. Além disso é preciso subir um **ícone em alta resolução**, um **gráfico de recursos** e pelo menos **dois screenshots** da app.

\[caption id="attachment\_7735" align="aligncenter" width="1024"\]![Developer Console à esquerda, e o resultado na Play Store à direita](https://blog.caelum.com.br/wp-content/uploads/2016/06/detalhes_app_playstore-1024x436.png) Developer Console à esquerda, e o resultado na Play Store à direita\[/caption\]

No fim da página, definimos o **tipo**, (app ou jogo) e uma **categoria**. E, por fim, uma **política de privacidade**. A política de privacidade não é obrigatória, mas você deve marcar o checkbox para dizer que não vai ter uma.

_Não se esqueça de salvar suas alterações clicando no botão "Salvar rascunho" no topo da página!_

 

**Classificação indicativa**

O critério e os níveis de classificação indicativa variam bastante de país para país. Por isso, o Google facilita bastante nosso trabalho oferecendo um questionário para determinar a classificação da sua app nos diversos países de publicação. Basta responder a todas as perguntas que ele gera as classificações dos diversos países para você.

 

**Preço e distribuição**

Nessa tela, você deve definir o preço da sua app (ou se é gratuita). Em seguida, você seleciona os países onde a app vai der distribuída - pelo menos um país deve ser selecionado. E depois, se a app contém algum anúncio.

No fim da página, você deve consentir que sua app segue as [diretrizes de conteúdo do Android](https://play.google.com/intl/pt-BR/about/developer-content-policy/) e que  você aceita sua app estar sujeita às leis de exportação dos Estados Unidos, marcando as últimas duas opções da página.

 

**Finalmente**

Com tudo isso pronto, o nosso botão "Publicar app" vai finalmente ficar habilitado. Depois de mandar publicar a app, o Google Play apenas pede para esperar algumas horas para a publicação ser efetuada.

![App publicada](https://blog.caelum.com.br/wp-content/uploads/2016/06/app_publicada-1024x333.png)
