---
title: "O processo de deploy contínuo"
date: "2010-03-01"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Ao término do primeiro sprint, sua aplicação está andando muito bem e tem todas as histórias aprovadas enquanto no ambiente de testes. Passaremos então para a primeira tentativa de colocá-lo em produção/homologação, e logo descobre-se que o sistema não funciona corretamente nesse novo ambiente, e é gasto muita energia para adaptar diversos detalhes que [já eram considerados "prontos"](http://fragmental.tw/2009/12/22/duke-nukem-forever-and-magic-bags-of-money/).

Após esse esforço, com a aplicação [rodando em outra máquina diferente da de desenvolvimento](http://www.codinghorror.com/blog/archives/000818.html), os clientes começam a utilizá-la e a quantidade de bugs específicos desse novo ambiente que aparecem sobrecarregam os desenvolvedores. O estranho de tudo isso é que o código original [rodava na máquina do desenvolvedor sem problemas](http://googletesting.blogspot.com/2007/09/but-it-works-on-my-machine.html), inclusive com testes unitários e de aceite.

Em sistemas que não implementam testes end to end, [mesmo que smoke tests](http://www.pragmaticsw.com/Newsletters/newsletter_2004_09_SP.htm) e que não se planejam para deploys desde o começo do projeto, é comum encontrar um grande número de bugs no primeiro deploy de homologação, além de uma extrema dificuldade para realizar essa implantação inicial.

A equipe passa então a trabalhar para corrigir essa onda de bugs, fica incapaz de adicionar novas funcionalidades em um ritmo adequado e desanima rapidamente.

Enquanto [o processo de deploy não for automatizado](https://blog.caelum.com.br/integracao-continua-deploys-e-aprovacoes-sem-dores-de-cabeca-para-o-cliente/), cada nova tentativa de implantação é seguida por uma enchurrada de bugs novos, sempre com as mesmas consequências negativas.

A solução aqui é automatizar todo o processo de deploy desde o primeiro sprint, e para isso você precisa estruturar muito bem seu ambiente, de forma que ele seja perfeitamente replicável entre as máquinas de desenvolvimento, homologação e produção. Isso é bastante planfletado pelo [movimento _Devops_](http://devopsdays.org/).

Graças aos ciclos mais curtos de deploy, o cliente testa o seu software mais cedo e recebemos [feedback mais rápido](http://en.wikipedia.org/wiki/Lean_software_development) quanto a bugs existentes, portanto o número de bugs novos sai do zero muito mais cedo, diminuindo um possível susto.

Martin Fowler comentou recentemente sobre a [estratégia de manter dois sistemas muito parecidos em paralelo e virar a chave de roteamento de um para o outro para efetuar a transição](http://martinfowler.com/bliki/BlueGreenDeployment.html), processo que pode e deve ser automatizado por completo em aplicações de alta disponibilidade. Fábio Akita da Locaweb explica [uma estratégia simples para deploy contínuo](http://akitaonrails.com/2010/02/13/deploy-com-git-push) através do uso do git.

Tudo isso minimiza o acúmulo de bugs, resultando em um ritmo constante, mas menor, de aparição de problemas, inerentes a qualquer processo.

Com o deploy contínuo através de técnicas como a citada pelo Akita, cada commit terá não só suas baterias de testes rodados, mas entra em homologação. Os bugs aparecem mais cedo durante o desenvolvimento do projeto e o processo de deploy pode ser efetuado mais frequentemente.

Indo além, com um sistema _blue-green_, seu processo de deploy atinge um nível maior ainda de maturidade, com a possibilidade de um rollback robusto a qualquer instante.

Como você está tratando o seu deploy?
