# Machine Learning

* Para definir se um algoritmo é bom ou ruim precisamos de uma base para comparação (seja um algoritmo burro que sempre dá a mesma resposta para todas as entradas ou outra forma qualquer);
* Avaliar se aquela taxa de acertos do algoritmo significa algo para o negócio, se vale apena a taxa de errosç

Os algoritmos requerem número como input.

Passos ideais:
* Treinar com um grande conjuntoç
* Testar contra um grande conjuntoç
* Após a decisão de qual algoritmo usar, rodar novamente contra um conjunto não visto antes pelo algoritmo para obter uma taxa de acerto mais real.


## Algoritmos

### Naive Bayes
* Ideal para classificação de texto;
* Toma decisão com base na probabilidade das características dos dados utilizados no treinamento e utiliza uma das 3 regras de decisão: Maximum a Posteriori, menor probabilidade ou probabilidade.
* Por exemplo, uma distribuição de treino que 70% resulta em X e 30% resulta em Y, conforme a regra de recisão ele classificaria uma entrada qualquer:
  * Maximum a Posteriori: Seria X pois este tem maior probabilidade (70%);
  * Menor probabilidade: Seria Y pois este tem 30%;
  * Probabilidade: sorteia um número de 1 a 100, se for <= 70 então é X se for > 70 então é Y.
* Caso tenha mais de uma características, é utilizado a multiplicação dos resultados individuais de cada característica.
* Notação: P(comprar | brasil), probabilidade de comprar dado a característica brasil

### Variáveis Dummies
Para variáveis categoricas podemos usar variáveis Dummies, onde presentamos o valor da categoria por um conjunto de variáveis.


## Libs
* Pandas: data structure and analisis, trata sozinho os dados dummies.
