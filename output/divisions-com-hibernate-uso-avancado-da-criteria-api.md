---
title: "Divisions com Hibernate: uso avançado da Criteria API"
date: "2008-09-11"
author: "lucas"
authorEmail: "lucas.cavalcanti@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Existe uma operação, não muito conhecida, mas muitas vezes necessária, em bancos de dados chamada **divisão** (_division_). Essa operação representa o seguinte tipo de consulta: Selecione os alunos que fizeram todos os cursos. Selecione os autores em que **todos** os seus livros têm mais de 200 páginas. E assim por diante.

Esse tipo de consulta precisa de alguns recursos avançados do SQL, então antes de mostrar como implementá-la vamos ver como implementar consultas um pouco mais simples, usando a [Subqueries](http://www.hibernate.org/hib_docs/v3/api/org/hibernate/criterion/Subqueries.html) e a [DetachedCriteria](http://www.hibernate.org/hib_docs/v3/api/org/hibernate/criterion/DetachedCriteria.html), que nos possibilitam consultas bastante poderosas usando a api da [Criteria](http://www.hibernate.org/hib_docs/v3/api/org/hibernate/criterion/package-frame.html).

Bom, vamos começar com três entidades: `Aluno`, `Curso`, e um relacionamento de muitos pra muitos entre eles representado pela entidade `Matrícula`.

Vamos pensar um pouquinho como fazer a seguinte consulta: "_Selecionar todos os alunos que estejam cursando **Matemática** ou **Português**_". Pensando em banco de dados, podemos fazer um _join_ entre Alunos e Matrículas, e selecionar as linhas em que o curso é **matemática** ou é **português**. Precisamos também evitar que a busca retorne alunos repetidos. Vamos fazer isso com `Criteria`, recebendo a lista dos cursos que eu quero que o aluno esteja cursando algum deles:

\[java\] public List<Aluno> alunosCursandoAlgumDessesCursos(List<Curso> cursos) { Criteria criteria = session.createCriteria(Aluno.class); //join com as matrículas criteria.createCriteria("matriculas", "m"); //usando a disjunction para fazer um 'ou' entre vários elementos Disjunction ou = Restrictions.disjunction(); for (Curso curso : cursos) { ou.add(Restrictions.eq("m.curso", curso); } criteria.add(ou);

//eliminando resultados repetidos criteria.setResultTransformer(Criteria.DISTINCT\_ROOT\_ENTITY); return criteria.list(); } \[/java\]

Ou podemos fazer algo bem mais interessante, que é usar a restrição [in](http://www.hibernate.org/hib_docs/v3/api/org/hibernate/criterion/Restrictions.html#in(java.lang.String,%20java.util.Collection)), que retorna verdadeiro se a propriedade dada é igual a algum dos elementos da coleção que passarmos pra ela. Nesse caso trocaríamos o `Disjunction` por simplesmente:

\[java\] criteria.add(Restrictions.in("m.curso", cursos)); \[/java\]

Bem fácil! Agora vamos mudar só um pouquinho a consulta para: "_Selecione todos os alunos que estiverem cursando Português **E** Matemática_". Poderíamos inocentemente mudar a `Disjunction` para `Conjunction` no método anterior. Mas isso não funciona! Por quê? Porque se fizermos isso, estaríamos mudando a consulta para algo do tipo: "_Selecione os alunos que tenham uma matrícula que é em Português e em Matemática ao mesmo tempo_". E isso não é possível. Temos que mudar essa consulta para algo do tipo: "_Selecione todos os alunos para os quais exista uma matricula no curso Português e exista uma matrícula no curso Matemática_".

Existe uma operação em SQL que faz exatamente isso: o **exists**. Ela retorna verdadeiro se a subconsulta que estiver depois dela retornar algum resultado. Para fazer isso precisamos então criar subconsultas em `Criteria`, e o jeito de fazer isso é usando a classe [Subqueries](http://www.hibernate.org/hib_docs/v3/api/org/hibernate/criterion/Subqueries.html), que fabrica `Criterions` que envolvem a criação de subconsultas.

Para usar qualquer método da Subqueries precisamos de uma [DetachedCriteria](http://www.hibernate.org/hib_docs/v3/api/org/hibernate/criterion/DetachedCriteria.html). Essa `DetachedCriteria` é um tipo especial de `Criteria` que não precisa da session do hibernate para ser criada. Dentro dela temos acesso a todos os alias e propriedades da `Criteria` principal, e o uso é o mesmo que faríamos para Criterias normais.

Já que temos a Subqueries na mão, vamos implementar a consulta, recebendo a lista dos cursos que queremos que o aluno esteja matriculado em todos eles:

\[java\] public List<Aluno> alunosCursandoTodosEssesCursos(List<Curso> cursos) { Criteria criteria = session.createCriteria(Aluno.class, "a"); Conjunction e = Restrictions.conjunction(); for (Curso c : cursos) { e.add(Subqueries.exists( DetachedCriteria.forClass(Matricula.class, "m") .setProjection(Projections.id()) .add(Restrictions.eqProperty("a.id", "m.aluno.id")) .add(Restrictions.eq("m.curso",c)))); } criteria.add(e); return criteria.list(); } \[/java\]

Ou seja, queremos que exista uma matrícula do aluno da `Criteria` principal para cada curso da lista passada.

Mas vamos pensar no seguinte: Essa lista de cursos provavelmente veio de outra consulta no banco, por que não usar essa consulta, ao invés da lista de cursos?! O jeito de fazer isso é usando o operador division que falamos no começo do post. Ele é meio complicado de implementar, pois você tem que pensar meio ao contrário do normal. Por exemplo, para implementar a consulta "_Selecione os alunos que estão matriculados em todos os cursos_" precisamos transformá-la para: "_Selecione os alunos para os quais não exista nenhum curso para o qual não exista matrícula desse aluno para esse curso_", ou seja: um aluno que não exista nenhum curso em que ele não esteja matriculado. É estranho mas é assim mesmo que é feito. A Subqueries também possui o método `notExists`, então podemos fazer a seguinte consulta, que traz os alunos que fazem todos os cursos:

\[java\] public List<Aluno> alunosCursandoTodosOsCursos() { Criteria criteria = session.createCriteria(Aluno.class, "a"); criteria.add(Subqueries.notExists( DetachedCriteria.forClass(Curso.class, "c") .setProjection(Projections.id()) .add(Subqueries.notExists( DetachedCriteria.forClass(Matricula.class, "m") .setProjection(Projections.id()) .add(Restrictions.eqProperty("m.curso.id", "c.id")) .add(Restrictions.eqProperty("m.aluno.id", "a.id") )) )); return criteria.list(); } \[/java\]

Não é um bicho de sete cabeças, mas também não é nada trivial. O código fica meio poluído por causa das chamadas estáticas, mas se você fizer o `import static` dos métodos a coisa melhora um pouquinho.

As restrições que você tinha colocado para buscar a lista de cursos dos métodos anteriores, você pode colocar na `DetachedCriteria` de `Cursos`, que vai funcionar do jeito que é esperado. Por exemplo: "_Selecione os alunos que estejam matriculados em um curso noturno_" vira "_Selecione os alunos para os quais não exista algum curso noturno em que ele não esteja matriculado_". Mais ainda: você pode colocar restrições pertinentes na `DetachedCriteria` da matrícula, que também vai funcionar da forma esperada. Por exemplo: "_Selecione os alunos que estejam com a matricula paga em todos os cursos_" vira "_Selecione os alunos para os quais não existe algum curso em que não exista matrícula paga nesse curso_".

Existem muitos casos em que o operador division salva sua vida então, mesmo que ele seja meio complicadinho, é bom saber que ele existe e ter uma boa referência de como implementá-lo =).

Além da Subqueries, existe outra classe muito útil que fabrica `Criterion`s e `Projection`s relacionados a uma propriedade fixa: a [Property](http://docs.jboss.org/hibernate/core/3.3/api/org/hibernate/criterion/Property.html). Vale a pena olhar o javadoc do hibernate e ver a quantidade de opções de consultas que temos disponíveis. Existe [um bug](http://opensource.atlassian.com/projects/hibernate/browse/HHH-993) no hibernate que te obriga a setar uma `Projection` nas `DetachedCriterias` quando usadas dentro das Subqueries, se isso não é feito o hibernate nos presenteia com uma `NullPointerException`.
