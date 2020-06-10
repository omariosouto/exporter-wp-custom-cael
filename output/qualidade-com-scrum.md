---
title: "Qualidade com Scrum"
date: "2008-06-02"
author: "edmilson.miyasaki"
authorEmail: "edmilson.miyasaki@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

Tive a oportunidade de palestrar juntamente com o [Alexandre Magno](http://amagno.blogspot.com/) no [Falando em Java 2008](https://blog.caelum.com.br/falando-em-java-2008-eu-fui/). Um tema recorrente durante as discussões depois de palestras e workshops sobre Scrum é **Qualidade**.

Quando falamos sobre qualidade em software, surgem diversas dúvidas quanto ao que significa ter qualidade em um software: ter um código bem escrito? Testes unitários? Código que não apresente falhas? Boa performance no que se propõe a fazer? Documentação?

De acordo com a NBR 13596 (ISO/IEC 9126), existem algumas características que um software deve apresentar para ser considerado como um software de qualidade. Estas características são listadas na tabela a seguir:

Característica

Descrição

Funcionalidade

Satisfaz as necessidades?

Confiabilidade

É imune a falhas?

Usabilidade

É fácil de usar?

Eficiência

É rápido e "enxuto"?

Manutenibilidade

É fácil de modificar?

Portabilidade

É fácil de usar em outro ambiente?

A maioria das características que determinam um software com qualidade referem-se mais a boas práticas de engenharia de software ou eficiência da plataforma tecnológica. Entretanto, **Scrum**, como framework para gerenciamento de projetos, também é capaz de oferecer qualidade no processo de desenvolvimento.

Em Scrum, conseguimos uma melhora na qualidade através de diversos pontos. Obter esta melhora na qualidade depende muito se Scrum está sendo bem implementado ou não.

Dentre estes pontos, podemos destacar:

- Iterações
- Remoção de impedimentos
- Inspeção e adaptação
- Autonomia
- Times multifuncionais

### Iterações

Qualidade em software também significa entregar para o cliente algo que lhe seja _realmente útil_, de acordo com suas necessidades.

Por ser uma framework ágil, Scrum trabalha com **iterações**, onde a cada iteração entregamos software, ou incrementos de software, potencialmente usável e de acordo com a necessidade do cliente. E, a cada nova iteração, temos "feedback" do que foi entregue e que utilizamos para melhorar o produto (sempre de acordo com a prioridade do cliente).

O "**feedback**" do cliente existiria de qualquer forma, seja apresentando o produto ao final de uma iteração, seja ao final de todo o ciclo de desenvolvimento (o que normalmente acarreta em alterações no código). Entretanto, se estas alterações forem feitas no final do projeto, isto também pode causar efeitos colaterais indesejados, ao passo que, fazê-las de forma antecipada, impede este tipo de problemas.

Através das **Sprints**, times Scrum estão sempre desenvolvendo algo que realmente tenha valor para o cliente.

### Remoção de impedimentos

Remover qualquer tipo de _impedimento_ durante a execução de um projeto é essencial não importa qual metodologia seja utilizada. Em Scrum, é esperado que estes problemas apareçam. Mas, o que é feito após resolver este impedimento, determina se um time está utilizando Scrum corretamente ou não.

Durante a execução de uma Sprint, é recomendável que a execução das tarefas seja feita item a item ao invés de cada membro executar tarefas de itens diferentes. Isso tem duas razões: a primeira é relacionada ao valor para o cliente. Para um cliente, um item somente tem valor caso tenha sido entregue completamente -- algo que esteja funcionando 80% não lhe trará vantagem alguma. Além disso, executar um item completamente ajuda a manter o foco da equipe na meta e no item em específico. Desenvolvedores em geral tendem a ser mais orientados a tarefas ao invés de orientados a valor. Manter o foco na meta ajuda a aumentar a qualidade do item sendo desenvolvido.

A outra razão é em relação à forma como os problemas são resolvidos e como suas correspondentes soluções são utilizadas. A execução completa de um item representa um fluxo completo de execução e faz parte do processo utilizado no desenvolvimento. Neste caso, problemas que poderiam se tornar recorrentes podem ser solucionados imediatamente, permitindo que isso não se repita na execução dos próximos itens. Desta forma, estamos aprimorando o processo, o que também reflete na qualidade do produto.

### Inspeção e adaptação

Ao final da execução de uma Sprint, há a **Sprint Retrospective**, uma das cerimônias de Scrum. Nela, revisamos a Sprint (**inspeção**) e determinamos o que foi bom e o que precisa ser melhorado (**adaptação**). As adaptações podem ser individuais ou coletivas, mas, de qualquer forma, elas garantem a melhora do processo e consequente otimização, o que traz diversos benefícios.

Com um processo mais enxuto e mais eficiente, podemos ter um software com mais qualidade.

### Autonomia

Times em Scrum são **auto-gerenciados**, o que significa uma menor pressão sobre eles. Desta forma, cada um dos membros pode selecionar o que fará e terá o tempo necessário para fazê-lo com qualidade. Estudos mostram que, sob pressão de prazos exíguos, a primeira coisa a ser deixada de lado pelos desenvolvedores é a qualidade.

Além disso, através desta autonomia, os membros do time passam a ter uma melhor qualidade de vida, o que reflete em uma melhoria na qualidade como um todo. Isso porque passam a ter mais tempo e disposição para pesquisar uma melhor forma de abordar e executar uma tarefa. Em um ambiente onde Scrum tenha sido bem implantado, este aprimoramento pessoal é compartilhado com os outros membros, o que traz mais incremento na qualidade.

### Times multifuncionais

Quando montamos os times, procuramos sempre montá-los com membros que tenham diferentes características ou atribuições. Por exemplo, ao invés de um time formado só por desenvolvedores ou só de analistas de requisitos, procuramos misturá-los e formar diversos times Scrum.

Isto porque a experiência de cada um é extremamente útil no planejamento das tarefas a serem executadas na Sprint. Entretanto, existe um conceito maior escondido por trás disto: **qualidade desde o início**.

Pude presenciar em diversas ocasiões a seguinte situação: empresas utilizando o modelo em cascata, faziam o levantamento de requisitos no início do projeto. Em seguida, arquitetos de sistema e especialistas no negócio modelavam as classes para atender a todos os requisitos levantados na etapa anterior. Depois (bem depois, por sinal), estes modelos eram passados para a equipe de desenvolvimento e o resultado era testado pela equipe de Q&A e homologação. No final do processo, isto era entregue à equipe de implantação.

Invariavelmente, o contato com o cliente era feito no início do projeto, onde este apresentava todos os requisitos possíveis e imagináveis para o produto. Embora saibamos que o cliente sabe o que precisa mas tenha somente uma vaga idéia do que quer, ele era obrigado a informar o que desejava que fosse desenvolvido, e por isso a quantidade de requisitos, algumas vezes desnecessários, era imensa.

Durante a modelagem, os analistas modelavam o que era necessário para a aplicação, muitas vezes deixando de lado alguns detalhes que poderiam facilitar o desenvolvimento ou ignorando outros detalhes que pudessem melhorar o acesso aos dados.

Os desenvolvedores, por sua vez, simplesmente executavam o que foi determinado pelos arquitetos e no prazo determinado pelo gerente de projeto.

Depois de devidamente codificado, o resultado era passado para a equipe de Q&A, que testava o que tinha sido produzido e retornava o resultado dos testes à equipe de desenvolvimento. Infelizmente, isto era feito invariavelmente aos lotes -- os testadores eram obrigados a testarem diversos recursos de uma vez, muitas vezes impossibilitando testes com aspectos mais amplos.

Como tudo isso feito às pressas, em algumas situações, a equipe de implantação era informada com poucos dias de antecedência (e em uma situação, a equipe foi informada que tinha até o final da tarde para implantar um sistema). Com tão pouco prazo, muitas vezes a implantação era feita sem qualquer teste, simplesmente esperando que a sorte sorrisse para eles.

Note que os cinco parágrafos anteriores descreveram cada um dos estágios no desenvolvimento. E isto reflete como o desenvolvimento era feito -- sem qualquer comunicação adicional entre cada uma das etapas que não fosse a documentação do sistema. É fácil descobrir o resultado disso.

Através de **times multifuncionais**, a cada Sprint temos a opinião de especialistas em diferentes áreas definindo o que será feito naquela Sprint. Enquanto não sabemos o que o cliente realmente quer como produto, sabemos o que é mais importante para ele, com estes especialistas definindo a melhor abordagem possível, levando em consideração os aspectos nos quais cada um é melhor. Assim, arquitetos podem começar definindo as classes levando em consideração a opinião de um especialista em banco de dados, de domínio, etc.

Utilizando o princípio de qualidade desde o início, o código tende a ser mais enxuto, mais adaptável, a ter mais performance. Como a interação com o usuário é constante, o produto estará sempre de acordo com a necessidade do usuário. E com a presença de um especialista em testes, cada tarefa executada já pode ser testada e eventualmente corrigida rapidamente.

E finalmente, um especialista em implantação já sabe antecipadamente o que deve testar e providenciar como ambiente de produção.

### Conclusões

Existe uma beleza singular na simplicidade apresentada por Scrum. Entretanto, por trás desta simplicidade, existem conceitos que não devem ser ignorados, sob pena de obter somente parte dos benefícios de Scrum.

Um ScrumMaster deve estar sempre atento aos diversos sinais que o time apresenta, bem como motiva-los e desafia-los, sempre em busca constante do aprimoramento individual como seres humanos e o time como um todo. Além disso, buscar a melhoria contínua do processo permite que a qualidade passe a ser uma constante em futuros projetos de software.
