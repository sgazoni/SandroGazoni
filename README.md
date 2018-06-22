# SandroGazoni
Arquivos

O site https://www.datascienceacademy.com.br foi consultado para algumas questões.

-------------------------------------------
Qual o objetivo do comando cache em Spark?
-------------------------------------------

A maior parte das operações em um RDD são Lazy Evaluation, ou seja, nenhum resultado é realmente obtido
quando se processa uma função de transformação nós apenas avisamos ao spark que nos queremos realizar
aquela transformação. Quando vc executa uma operação de ação, os RDDs são recomputados, mesmo eles estando
em memória eles serão sempre recomputados tornando o processo um pouco mais lento.
Para evitar esse comportamento podemos persistir os RDDs em cache de modo que as operações de ação sejam 
mais rápidas.
A função cache persiste os dados em memória que é o padrão do Spark ou seja os dados não serão recomputados
quando aplicarmos a função de ação.

-------------------------------------------------------------------------------------------------------------
O mesmo código implementado em Spark é normalmente mais rápido que a implementação equivalente em MapReduce. Por quê?
-------------------------------------------------------------------------------------------------------------

No Spark os resultados intermediários do processamento é feito em memória, ao contrário do Mapreduce onde
esses mesmo resultados são gravados em disco.

Entretando o spark pode não ser a melhor opção em todos os casos. Por exemplo quando se tem uma quantidade muito
grande de arquivos, na casa dos Petabytes ou acima de 1 Tera, pode ser que o spark não seja a melhor opção.

--------------------------------------
Qual é a função do SparkContext ?
--------------------------------------
No momento em que voce inicializa a aplicação pyspark seja via console ou jupyter notebook 
automaticamente é criado o SparkContext como se fosse um ambiente de desenvolvimento, onde
através dele, passam-se as configurações que vão ser utilizadas na alocação de recursos, 
como memória e processadores, pelos executors.
O SparkContext é uma espécie de API que faz o gerenciamento do Driver Program que é uma espécie de interface
entre a aplicação cliente e todo o ambiente de execução do spark.

------------------------------------------------------------------------
Explique com suas palavras o que é Resilient Distributed Datasets (RDD)
------------------------------------------------------------------------

Os RDDs são uma coleção de objetos distribuida e imutável(Coleção de objetos Read Only) 
onde cada conjunto de dados no RDD e dividido em partições lógicas que podem ser computadas em 
diferentes nodes do cluster.

Imagine o RDD como uma tabela de um banco de dados que pode guardar quando tipo de dado. 
É Resilient por que é tolerante a falhas. (Estrutura armazenada e processada em memória)

-----------------------------------------------------------------------------
GroupByKey é menos eficiente que reduceByKey em grandes dataset. Por quê?
-----------------------------------------------------------------------------

reduceByKey (function) - Quando chamado em um dataset de pares (K, V), 
retorna um dataset de pares (K, V) onde os valores para cada chave são agregados usando a função de redução dada.
 
A função deve ser capaz de receber argumentos de algum tipo e retornar o mesmo tipo de dados de resultado. 

Como em groupByKey, o número de tarefas de redução é configurável por meio de um segundo argumento opcional.
Ao contrário de groupByKey, reduceByKey não mistura dados no início. 
Como sabe que a operação de redução pode ser aplicada na mesma partição primeiro,somente o resultado da 
função de redução é transferido na rede. 
Isso causa uma redução significativa no tráfego pela rede. 

Quando fazemos uma agregação utilizando reduceByKey, o Spark sabe que pode realizar a operação passada como parâmetro
em todos os elementos de mesma chave em cada partição para obter um resultado parcial antes de passar esses dados 
para os executores que vão calcular o resultado final, resultando em um conjunto menor de dados sendo transferido. 

Ao chamar o RDD e usar groupByKey para aplicar a agregação, o cálculo de resultados parciais 
não é realizado, dessa forma um volume muito maior de dados é desnecessariamente transferido 
através dos executores podendo, inclusive, ser maior que a quantidade de memória disponível para o mesmo, 
o que cria a necessidade de escrita dos dados em disco e resulta em perda de performance.

Esta é uma operação dispendiosa, particularmente quando se trabalha em um grande conjunto de dados. 
Isso também pode causar problemas quando a lista de valores combinados é enorme para ocupar em uma partição. 
Nesse caso, cria a necessidade de escrita dos dados em disco.

groupByKey () opera em Pair RDDs e é usado para agrupar todos os valores relacionados a uma determinada chave.


------------------------------------------------
** Explique o que o código Scala abaixo faz **
------------------------------------------------

1. val textFile = sc . textFile ( "hdfs://..." )
2. val counts = textFile . flatMap ( line => line . split ( " " ))
3.           . map ( word => ( word , 1 ))
4.           . reduceByKey ( _ + _ )
5. counts . saveAsTextFile ( "hdfs://..." )

Nesse código, um arquivo-texto é lido (linha 1) no hdfs e transferido para a variável textFile. 
Em seguida, cada linha é "dividida" em palavras onde cada palavra é então transformada em um 
mapeamento de chave-valor, com chave igual à própria palavra e valor 1 (3). 
Esses valores são agregados por chave, através da operação de soma (4) (ReduceByKey). 
Por fim, o RDD com a contagem de cada palavra é salvo em um arquivo texto (5) no HDFS.
