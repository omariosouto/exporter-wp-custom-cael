---
title: "Mamute: a engine do GUJ, agora opensource"
date: "2014-05-05"
author: "leonardo.wolter"
authorEmail: "leonardo.wolter@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Depois do sucesso no lançamento do [GUJ Respostas](http://guj.com.br), onde ja estamos chegando a 20 mil perguntas com um ótimo feedback, anunciamos agora o [Mamute.org](http://mamute.org). Um [framework opensource](http://github.com/caelum/mamute "mamute") que torna possível de um modo muito simples a criação de um fórum no estilo de Perguntas e Respostas(Q&A), dado o sucesso do Stackexchange e do Quora!

O Mamute nada mais é do que uma estrutura ja pronta de um Q&A customizável, a mesma usada no [GUJ Respostas](http://guj.com.br), e agora no [VRaptor Questions](http://questions.vraptor.org/).

A instalação do framework é muito simples e pode ser feita utilizando o github ou simplesmente baixando o war da versão mais atual.

### Instalando o mamute utilizando o war mais atual

1. Crie uma pasta para o seu projeto
2. Baixe o ultimo war no [site](http://mamute.org)
3. Descompacte o war em uma pasta chamada mamute, dentro do seu projeto
4. Para testar, rode o script mamute/run.sh

### Instalando o mamute utilizando o git

1. Crie um repositório para o seu projeto
2. Clone o repositório do mamute: https://github.com/caelum/mamute
3. Execute o comando mvn package no mamute
4. Copie a pasta mamute/target/mamute-x.x.x para o seu projeto
5. Para testar, rode o script mamute/run.sh

Para facilitar a atualização da versão do mamute, criamos um script chamado `update-mamute.sh`. Este script espera que você tenha clonado o repositório do mamute e este deve ser informado através da variável de ambiente `MAMUTE_DIR`. Exemplo:

MAMUTE\_DIR=~/git/mamute ./update-mamute.sh

Ele vai atualizar todos os arquivos do mamute, exceto os customizáveis pelo usuário. Você pode encontrar a lista de arquivos customizáveis [aqui](http://meta.mamute.org/271-how-can-i-update-mamute-in-my-project).

Vale a pena lembrar que o Mamute.org exige que o banco de dados utilizado seja MySQL atualmente.

Se você tiver alguma pergunta referente ao framework, utilize o [Meta Mamute](http://meta.mamute.org) para solucioná-la. Caso queira testar o framework, disponibilizamos uma [demonstração](http://demo.mamute.org)

Fica o agradecimento a todos os committers e em especial a comunidade do GUJ, que durante quase um ano testou a ferramenta. Há sim muito a ser feito e aprimorado, e a partir de hoje você também pode ajudar. Vale lembrar que ferramentas como fóruns, blogs e Q&As não bastam: para ter sucesso, é preciso saber trabalhar bem a comunidade e engajá-la.
