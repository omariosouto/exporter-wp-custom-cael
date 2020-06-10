---
title: "Seu repositório sabe tudo sobre seu projeto!"
date: "2016-02-04"
author: "maniche"
authorEmail: "mauricioaniche@gmail.com"
main_guide: "main_guide"
main_category: "geral"
---

Até então, eu já falei bastante sobre códigos fedidos e métricas de código aqui no blog, como por exemplo nos posts sobre [complexidade ciclomática](https://blog.caelum.com.br/medindo-a-complexidade-do-seu-codigo/), [acoplamento](https://blog.caelum.com.br/orientacao-a-objetos-uma-outra-perspectiva-sobre-o-acoplamento/), [coesão](https://blog.caelum.com.br/como-medir-a-coesao-lcom/), [asserts em testes](https://blog.caelum.com.br/o-que-a-quantidade-de-asserts-em-um-teste-nos-diz-sobre-o-codigo/), e [outros maus cheiros](https://blog.caelum.com.br/encontrando-smells-no-seu-codigo/). Todos eles até agora, se baseiam no código. E faz todo sentido, claro, afinal, **como achar um arquivo Java problemático, sem olhar o código Java desse arquivo?**

Mas temos um outro lugar bastante interessante, onde podemos procurar por coisas estranhas em nosso projeto. **E esse lugar é o repositório de código que guardamos nosso projeto!** Por exemplo, o Git. Afinal, tudo que você faz em seus arquivos, todas as pequenas modificações que acontecem ao longo do tempo, são salvas lá.

Pensa comigo: um arquivo que foi modificado em 25% de todos os commits do projeto parece estranho, não!? E o arquivo que o desenvolvedor escreve "corrigi um bug" na mensagem, toda vez que é committado? E o arquivo que já teve muitas linhas adicionadas e removidas, o que significa?

E por aí vai! E o mais legal é que basta minerar um pouco do repositório para extrair tudo isso. Essas métricas são conhecidas por _change metrics_, ou _social metrics_. Exemplos de informações que podemos extrair de repositórios:

- Contar o número de vezes que o arquivo foi modificado
- A quantidade de linhas já alteradas ao longo do tempo
- O número de desenvolvedores diferentes que já tocaram nele
- Quantos arquivos em média são modificados sempre junto com ele
- Quantas vezes o desenvolvedor disse que fez uma "refatoração" nele
- Quantas vezes o desenvolvedor disse que "corrigiu um bug" nele

Essas informações podem nos ajudar a descobrir arquivos que merecem mais atenção que outros. Veja, por exemplo, as informações que conseguimos extrair do [VRaptor 4](http://www.github.com/caelum/vraptor4) nessa [planilha no Google Docs](https://docs.google.com/spreadsheets/d/1BZDHEkNeFBJr-IbdGZbWjpoakIMk07KPcWTp9edNoVU/edit?usp=sharing). Se ordenarmos por "revisions", ou seja, número de revisões que um arquivo já teve, vemos que o _VRaptor.java_ está em primeiro lugar, seguido do _GenericContainerTest.java_, _AspectStyleInterceptorHandlerTest.java_ e _GsonJSONSerializationTest.java_. Ou seja, a classe principal do VRaptor, bem como seus testes são frequentemente modificados.

Se ordenarmos por "bugfixes", e olharmos as primeiras posições, vemos que o _VRaptor.java_ também aparece lá. Ou seja, sempre que há uma correção de bug, alguém mexe lá também. Se pularmos as próximas que são testes, encontramos a _LinkToHandler.java_. Será que devemos nos preocupar com ela? Se ordenarmos por "churn", ou seja, a quantidade de linhas adicionadas e removidas na história do arquivo, temos _IogiParametersProviderTest.java_ com 2188 linhas. Por que essa classe muda tanto?

**Esses dados não nos indicam classes problemáticas, mas nos ajudam a mostrar classes ... digamos, diferentes. E é sempre legal descobrir o que foge do padrão, não é mesmo?**

Esses dados também devem ser interpretados com contexto. Por exemplo, para descobrir "bugfixes", procurei por commits com a palavra "fix" no meio. Pelo baixo número de bugs (já passei por sistemas com valores muito maiores), os desenvolvedores podem não fazer uso dessa nomenclatura, e por consequência, esse número pode ser longe da realidade. Mas nada impede você de modificar o algoritmo para o seu projeto em específico.

Se você quer calcular isso em seu projeto Git, você pode usar um projeto meu, de código aberto para isso. Ele se chama [change-metrics](http://github.com/mauricioaniche/change-metrics), e tem uma [release pronta com o jar](https://github.com/mauricioaniche/change-metrics/releases/tag/0.0.1). Ele gera um CSV idêntico ao que mostrei do VRaptor. As instruções de uso estão no próprio [README do projeto](https://github.com/mauricioaniche/change-metrics/blob/master/README.md). Se você quer fazer outros tipos de análises mais complicadas em seu repositório, pense em usar o [MetricMiner](http://www.metricminer.org.br), um framework que criei para minerar repositórios Git.

Se gostou da ideia e quer ler mais sobre estudos malucos que as pessoas fazem com isso, veja conferências como a MSR, ICSME e SANER, e descubra da onde saiu o [meu tweet](https://twitter.com/mauricioaniche/status/674567153660497920) sobre o [quanto cada framework de persistência, como Hibernate, JPA, etc, sobrevivem em projetos Java](https://www.researchgate.net/profile/Mathieu_Goeminne/publication/281204249_Towards_a_Survival_Analysis_of_Database_Framework_Usage_in_Java_Projects/links/55db053508aeb38e8a8a4164.pdf).

No Alura, temos muitos cursos que ajudam você a combater esses maus cheiros de código, como é o caso do nosso [curso de SOLID](https://www.alura.com.br/curso-online-orientacao-a-objetos-avancada-e-principios-solid) ou [Design Patterns](https://www.alura.com.br/curso-online-design-patterns).
