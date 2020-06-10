---
title: "Ruby, Capistrano e administração remota"
date: "2007-08-06"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Algumas vezes nos deparamos com a seguinte situação:

Temos uns 15 computadores em rede e precisamos executar um mesmo comando em cada um deles, como por exemplo instalar a JavaVM...

Uma primeira solução seria logar como root máquina por máquina, e executar o comando em todas... Muito ruim...

Agora, se você souber um pouquinho de bash script, em cinco minutos conseguirá fazer um script que loga via ssh em todas as máquinas e executa o comando nelas. Mas ainda tem o problema de você ter que digitar o password para cada máquina, e de ter que assistir a execução do comando, caso ele precise de alguma confirmação, ou algo do tipo...

Felizmente existe uma ferramenta muito útil que pode nos ajudar nessa tarefa entediante. E ela se chama **Capistrano** ([www.capify.org](http://www.capify.org)). Com os mesmos 5 min você consegue escrever um script que execute o comando que você quer, digite as confirmações pra você nos lugares necessários, e você só precisará digitar a senha uma única vez!

O Capistrano requer que você o use em sistemas compatíveis com bash, e que exista um mesmo usuário em todas as máquinas que tenha uma única senha.

Para o script funcionar você precisa, com o capistrano instalado corretamente:

- Definir o usuário a que você vai se conectar nos micros:
    
    `set :user = "root" #pode ser qualquer usuário`

- Definir os ips dos micros aos quais você irá se conectar:
    
    `role :micros = "192.168.0.10", "192.168.0.11"`

- Ou se for uma faixa de IPs:
    
    `role :micros = *("192.168.0.10" .. "192.168.0.25").to_a`

- E, finalmente, definir a tarefa que você quer executar, ou seja, o(s) comando(s). Para isso temos que passar os roles a que queremos nos conectar (os micros) \[ruby\] task :instalaJava, :roles =&gt; :micros do run "apt-get install java-virtual-machine" end \[/ruby\]
Pronto! O comando será executado em todas as máquinas! Ou melhor, quase pronto... Você sabe que o apt-get pode pedir confirmação para continuar instalando o programa pedido... Você pode automatizar isso usando um recurso muito útil do capistrano:

\[ruby\] run "apt-get install java-virtual-machine" do |channel, stream, data| #channel é um objeto que nos permite enviar dados para o comando #stream identifica se a saída do comando foi a padrão (:out) ou a de erros(:err) #data é uma string com a saída do comando if data =~ /Do you want to proceed/ #se na saída do comando tiver essa frase channel.send\_data "y\\\\n" #manda pro comando um y e um enter end puts data #pra saída do comando aparecer na tela end \[/ruby\] Agora sim, o script vai fazer o que a gente queria: executar o comando e confirmar! O comando run vai ser executado em todos os micros **paralelamente**, e o bloco de código entre do e end do run vai ser executado toda vez que o comando "apt-get" jogar algum texto na tela ou na saída de erros.

Pra executar o script basta salvar o código que escrevemos como "capfile" e chamar pela linha de comando cap + o nome da nossa task:

`$ cap instalaJava`

O comando irá mostrar qual task está sendo executado e pedirá uma vez a senha. E é só ficar assistindo, ou ir fazer algo mais produtivo.

Nesse arquivo capfile você pode usar toda a sintaxe e as funcionalidades de um rakefile e, consequentemente, código em ruby. Então dá pra fazer coisas bem legais, como chamar tasks dentro de tasks, popular um role dinamicamente, executar uma tarefa antes e/ou depois da task chamada, enfim, muitas coisas...

Você só terá que se preocupar com uma coisa: se um dos micros que estiver no role da task não estiver acessível pela rede, ou se acontecer um erro fatal qualquer, o capistrano não irá executar a task em **nenhum** dos micros.

É isso. Divirta-se!
