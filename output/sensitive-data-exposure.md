---
title: "Sensitive Data Exposure"
date: "2019-02-18"
author: "rferreira"
authorEmail: "rodrigo.ferreira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Frequentemente vemos notícias de que hackers invadiram algum sistema de alguma empresa, expondo dados sensíveis de seus usuários, tais como documentos pessoais e cartões de crédito.

Um [caso recente](https://www.tecmundo.com.br/seguranca/129428-vazamento-netshoes-continua-totaliza-dados-2-5-milhoes-clientes.htm) aconteceu aqui mesmo no Brasil com uma empresa de e-commerce, onde dados como nome completo, CPF e data de nascimento de milhares de seus clientes foram expostos na internet.

Toda aplicação precisa lidar com dados de seus usuários, sendo que alguns deles são considerados como sendo sensíveis e por isso deveriam estar bem protegidos para o caso de um possível acesso indevido.

O grande problema é que desenvolvedores somente consideram dados de cartão de crédito como sendo uma informação sensível, tendo então uma preocupação maior com sua manipulação e armazenamento.

Inclusive existe uma norma chamada [PCI-DSS](https://www.pcisecuritystandards.org/) (Payment Card Industry - Data Security Standard), que deve ser seguida por empresas de pagamento online e lojas de e-commerce que lidam com dados de cartão de crédito de seus clientes.

Porém, nem toda aplicação tem funcionalidades relacionadas com pagamentos online e precisará lidar com dados de cartão de crédito. Mas isso não significa que ela não lide com dados sensíveis.

Ninguém gostaria que seus dados pessoais, como documentos, endereços e telefones, fossem expostos na internet para qualquer um acessar. Esse tipo de informação também deveria ser considerado como sendo sensível e deveria ser trafegado e armazenado de maneira segura.

## OWASP Top 10

O **Open Web Application Security Project**, conhecido como [OWASP](https://www.owasp.org), é uma comunidade aberta iniciada em 2001, com o objetivo de capacitar as organizações sobre como desenvolver, adquirir, operar e manter aplicações confiáveis, em relação à segurança. O projeto oferece gratuitamente documentos, ferramentas, fóruns e estudos sobre segurança em aplicações Web.

Um dos documentos mais populares produzidos pelo OWASP é o [Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_2017_Project), que é uma lista elaborada baseada em estudos, contendo os 10 principais e mais críticos riscos existentes em aplicações Web. O documento descreve de maneira detalhada os riscos, mostra exemplos de como eles funcionam e também ensina como se prevenir contra eles.

Na última atualização do Top 10, que saiu em 2017, existe um item chamado **Sensitive Data Exposure**, que justamente fala sobre o risco que existe em aplicações de terem seus dados sensíveis expostos.

Para minimizar esse risco, existem algumas boas práticas que devem ser seguidas pela equipe de desenvolvimento da aplicação.

## Boas práticas para lidar com dados sensíveis

Existem algumas boas práticas que devem ser seguidas ao lidar com dados sensíveis em uma aplicação, diminuindo assim o risco deles serem expostos de maneira indevida.

**Classifique os dados da aplicação**

Classifique **todos** os dados que a aplicação vai gerenciar. Além de analisar do ponto de vista de negócios da aplicação, pesquise também por normas e leis que classificam dados como sendo sensíveis.

**Evite armazenar dados sensíveis**

Discuta se realmente é necessário para a aplicação armazenar os dados sensíveis, buscando por outras alternativas quando possível. Um exemplo de alternativa seria o armazenamento temporário.

**Utilize criptografia**

**Sempre** utilize criptografia ao armazenar os dados classificados como sensíveis, evitando que eles sejam armazenados em _plain text_ no banco de dados. Utilize também criptografia ao trafegar esses dados pela rede, com o auxilio de protocolos seguros.

**Evite algoritmos fracos**

Ao utilizar criptografia, certifique-se de utilizar algoritmos atualizados e que são considerados como sendo fortes. O mesmo é válido para os algoritmos de hashing que são utilizados para armazenar as senhas dos usuários.

**Evite a utilização de cache**

Embora cache seja uma técnica que ajuda bastante com performance, evite o utilizar em requisições que trafegam dados sensíveis.

Quer aprender mais sobre segurança em aplicações Web? Conheça nosso curso focado em como executar pentesting em aplicações Web e também sobre como identificar e corrigir as vulnerabilidades no código fonte, seguindo as boas práticas do desenvolvimento seguro: [Curso Segurança em Aplicações Web](https://www.caelum.com.br/curso-seguranca-aplicacoes-web).

E você, costuma discutir sobre dados sensíveis e criptografia com sua equipe de desenvolvimento? Já utilizou alguma técnica para lidar com esse tipo de informação?

Compartilhe nos comentários sua experiência sobre o assunto :)
