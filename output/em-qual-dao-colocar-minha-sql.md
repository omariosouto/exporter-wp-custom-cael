---
title: "Em qual DAO colocar minha SQL?"
date: "2014-12-16"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Separação de responsabilidades é fundamental em qualquer sistema orientado a objetos. Afinal, a vida já nos ensinou que aquele código ASP que escreve HTML, contém regras de negócio, faz pipoca e acessa banco de dados é impossível de ser mantido a longo prazo. E uma das boas práticas em separar responsabilidades (e que os desenvolvedores já fazem bem há bastante tempo) é isolar acesso a banco de dados do resto do código. Sempre colocamos nossas consultas SQLs em nossos DAOs.

O problema é quando nosso sistema cresce, e passa a ter muitos DAOs e SQLs. A pergunta que vem a cabeça é: onde colocar essa SQL? Veja, por exemplo, a SQL abaixo. Em qual DAO ela pertence? Pertence a _Projects_, _Commits_ ou _Artifacts_?

\[code\] SELECT  p.name as projectName, c.id as commitId, a.name as artifactName, a.path as artifactPath FROM Projects p JOIN Commits c on c.project\_id = p.id JOIN Artifacts a on a.commit\_id = c.id WHERE p.repository = 'Apache';  \[/code\]

Não é uma decisão fácil. O desenvolvedor precisa conhecer bem o contexto do domínio para tomar essa decisão. No exemplo acima, talvez o melhor lugar para essa SQL seria o _ArtifactDAO_, afinal essa busca devolve uma lista de artefatos. E se por acaso ele tomar uma má decisão e colocar esse método em um DAO que não é o melhor? Outro desenvolvedor, ao procurar pela SQL, não a encontrará, e provavelmente escreverá uma réplica dela em outro lugar. E código repetido é sempre problema.

Descobrir métodos que podem não estar em seus melhores DAOs pode ser algo útil quando pensamos em medir a [dívida técnica](http://en.wikipedia.org/wiki/Technical_debt) dos nossos projetos. Para isso, podemos fazer uso de heurísticas para detectar esse tipo de método. Heurísticas e métricas de código são maneiras rápidas e baratas para detectar problemas em nosso código.

Decidimos então bolar algumas regras, baseadas em nossa experiência, de quando um método está no DAO mais correto. Imagine que estamos olhando para os métodos da classe _XDao_. A boa prática é que _XDao_ seja a classe responsável por acesso aos dados da entidade _X_. Então, basicamente olhamos para a assinatura de cada método desse DAO: se na assinatura, ele de alguma forma faz uso de X (retorna X, recebe X como parâmetro, faz uso de algum sub-tipo de X, etc), então esse método está no lugar certo.

Obviamente é difícil garantir que se um método apresentar alguma dessas características, ele está no DAO correto. Mas, dado que nossa experiência dizia que na maioria dos casos, isso era verdadeiro, decidimos avaliar se estávamos certos. E para isso olhamos para todos os DAOs de 3 projetos nossos. Criamos um protótipo de ferramenta que olha a assinatura de todos os métodos dos nossos DAOs e nos diz se o método está ou não dentro dessas regras.

Com o resultado em mãos, o apresentamos para um desenvolvedor do projeto dizer se concordava ou não com o resultado da heurística. E, para nossa surpresa, a heurística acerta na grande maioria das vezes!

Para um dos projetos, tínhamos 590 métodos dentro de DAOs, dentro dos quais a ferramenta disse que 79 deles possivelmente estavam em lugares errados. Isso quer dizer em torno de 14% dos métodos. Quando um desenvolvedor do projeto olhou todos eles, ele concordou com a ferramenta em 59 métodos, ou seja, em **75% dos casos**! Tivemos resultados parecidos para os outros 2 projetos avaliados: Em um deles, de 13 métodos marcados como possivelmente errados, o desenvolvedor concordou em 8 deles (60%). No outro, dos 33 errados, o desenvolvedor concordou em 16 deles (50%).

Ou seja, a heurística parece fazer sentido. **Veja que a ideia de uma métrica de código não é dizer precisamente se uma classe tem ou não determinado problema, mas sim dar um bom indicativo**. E veja, dos quase 600 métodos, a ferramenta selecionou apenas 79. E inspecionar manualmente 79 métodos é algo que sua equipe consegue fazer com um custo razoável (ainda mais sabendo que o acerto da heurística é superior a 50%); já inspecionar manualmente 600 métodos custa caro.

Por fim, lembre-se que escrever código de qualidade é difícil. Qualquer ferramenta que nos avise de possíveis problemas é de grande ajuda. Se você gostou do assunto, pode ler mais sobre o [paper que publicamos no MTD2014](http://www.aniche.com.br/wp-content/uploads/2013/04/mtd2014.pdf), o [Workshop Internacional sobre Dívida Técnica](http://www.sei.cmu.edu/community/td2014/), que aconteceu esse ano, em Victoria/Canadá. Você pode ver a simples implementação do nosso protótipo no [Github](https://github.com/mauricioaniche/calculadora-de-daos), e até executá-la em seus projetos.

E nos seus projetos? Você sofre muito para encontrar uma determinada SQL? Já repetiu código nos DAOs, sem querer?
