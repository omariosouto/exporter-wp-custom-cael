---
title: "Trabalhando com coleções aninhadas: Maps de Maps de Lists de Lists"
date: "2011-06-02"
author: "peas"
authorEmail: "paulo.silveira@caelum.com.br"
main_guide: "main_guide"
main_category: "geral"
---

![](https://blog.caelum.com.br/wp-content/uploads/2011/06/java_generics-228x300.gif "java_generics-228x300") Os generics do Java possibilitam, além de uma certa garantia de tipo em tempo de compilação, visualizar rapidamente com que tipos as coleções estão trabalhando. Sabemos que `List<Aluno>` é uma lista de aluno e que `Map<Cidade, Estado>` parece mapear uma cidade para um determinado estado, logo um estado pode ter várias cidades associadas. Fácil.

Às vezes temos confiança demais nessa expressividade, e decidimos expor nossas coleções via getters ou mesmo receber argumentos onde há um encadeamento grande de definições. O usuário do código acaba ficando dependente da investigação da assinatura dessa tipificação, além de ficar bastante confuso ao ter uma coleção em mãos, parecendo que pode manipulá-la e utilizar todos seus métodos, o que nem sempre é verdade.

Vejamos um caso onde guardamos preferências. Tente desvendar o uso da seguinte coleção:

\[java\]

public Map<Cliente, Map<String, List<String>>> getPreferenciasDosClientes() { return preferenciasDosClientes; // ou uma copia defensiva } \[/java\]

A preferência de cada cliente parece devolver, para cada chave `String`, uma lista de valores. Funciona muito bem, porém quem vai trabalhar com esse mapa terá de utilizar métodos `put`, `clear`, `add`, etc, bem distantes da lógica de negócio. Para pegar o timeout de determinado cliente, teríamos:

\[java\] int timeout = Integer.parseInt(getPreferenciasDoCliente() .get(cliente).get("timeout").get(0)); \[/java\]

Mesmo que você não seja fã da [lei de Demeter](http://en.wikipedia.org/wiki/Law_of_Demeter), é fácil sentir que algo aí está muito exagerado. Precisamos desembrulhar coleção de dentro de coleção para pegar o que precisamos. Por mais que a tipagem via generics nos ajude a deduzir essa sequência, a linha de código acima deixa muito a desejar.

Não seria mais interessante, por se tratar de `Preferencias`, ter apenas disponíveis métodos para leitura, dada uma `String`? Ou até possibilitar que o usuário saiba dizer se deveria ter apenas um valor para determinada chave? Teríamos então [um tipo bem específico](https://blog.caelum.com.br/pequenos-objetos-imutaveis-e-tiny-types/) para essa tarefa:

\[java\] interface Preferencias { String valorPara(String chave); List<String> valoresPara(String chave); } \[/java\]

Internamente, ela pode ser implementado através de um mapa, e no caso da invocação de `valorPara` podemos fazer a verificação da unicidade de valores (`if(mapa.get(chave).size() != 1)`). Essas garantias só podem ser feitas de maneira elegante agora que não expomos mais nossas coleções. Elas são estruturas internas dos nossos objetos e devem ser tratadas com cuidado, permitindo acesso sempre ao mínimo necessário, através de uma interface bem definida.

Com essa nova proposta, teríamos:

\[java\] public Map<Cliente, Preferencias> getPreferenciasDosClientes() { return preferenciasDosClientes; } \[/java\]

E a utilizaríamos com:

\[java\] int timeout = Integer.parseInt(getPreferenciasDoCliente() .get(cliente).valorPara("timeout")); \[/java\]

Apesar de já estar mais curto, esse não é o objetivo. Importante aqui é que já ficou mais claro o que estamos fazendo nessa linha de código, além de que não expomos mais métodos de uma `List`, pois não há necessidade. Dependendo da necessidade, podemos chegar a ter `getPreferenciasDoCliente().get(cliente).valorIntPara("timeout"))` deixando não apenas a verificação de unicidade de valor para `Preferencias`, mas também o `Integer.parseInt`. Se você vai chegar a esse ponto? Depende de caso a caso: pode ser um exagero.

Um mapa que passa a sensação de um-para-muitos também pode ser um sinal de necessidade de inversão de lados. Aqui pode ser um bom momento de remover o mapa de clientes para preferências e colocar, dentro da classe `Cliente`, uma referência a `Preferencias`.

Pode haver casos em que até mesmo a `List` de `String` seja interessante de extrair em uma classe. Certamente há ocasiões em que faz sentido passar como argumento coleções com grandes encadeamentos de definições. O importante é ponderar e sempre considerar a estruturação de coleções tipadas em classes próprias, em especial se elas serão expostas.
