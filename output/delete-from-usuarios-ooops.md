---
title: "DELETE FROM usuarios; Ooops!"
date: "2015-10-13"
author: "gas"
authorEmail: "guilherme.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Todo mundo já deu um enter antes da hora. Ou esqueceu só um detalhezinho numa linha antes de dar aquele enter gostoso. Não importa o motivo, `DELETE`s e `UPDATE`s são instruções perigosas em SQL.

Sempre que for escrever um DELETE no console do banco de dados comece escrevendo o WHERE primeiro.

`WHERE spam = true;`

Coloque o select:

`SELECT subject FROM emails WHERE spam = true;`

Confira o resultado, é como se fosse um preview, são as linhas que queria remover?

`| subject | | Ganhe 100 mil reais | | Ganhe 200 mil reais | | Ganhe 300 mil reais |

3 results` Por fim, troque o select por delete:

`DELETE FROM emails WHERE spam = true;`

Assim você evita que um enter acidental apague sua tabela toda.

Claro que é interessante evitar executar tais operações em produção, pelo perigo que elas apresentam, mas mesmo localmente diversas vezes apagamos algo que estamos testando e sofremos para correr atrás do prejuízo, restaurando o banco novamente.

Não só no mundo SQL, em qualquer outro banco de dados onde executamos operações destrutivas é importante conferirmos o resultado de nossas ações primeiro verificando quais elementos serão afetados.

Quer fugir de outros pepinos graves do SQL e facilitar o seu dia a dia com banco de dados? Receba dicas e boas práticas do nosso expert Guilherme Silveira, uma por dia, na [série de Dicas e Boas práticas de SQL](https://docs.google.com/forms/d/12aRctzNcpHVpWMTwa_xbUbHFe5gj9r2UNoBU0tysqQA/viewform).

Ou se quer ir direto para um aprendizado mais profundo, não deixe de conhecer os [diversos cursos de banco de dados, SQL e NOSql](https://www.alura.com.br/cursos-online-banco-de-dados) que o Alura possui.
