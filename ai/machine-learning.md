# Machine Learning

Um software é dito que aprendeu a partir de uma experiência E em alguma tarefa T com uma medida de performance P, se sua performance na tarefa T, medida por P, aumentou sua experiêncie E (Tom Mitchel).

* Para definir se um algoritmo é bom ou ruim precisamos de uma base para comparação (seja um algoritmo burro que sempre dá a mesma resposta para todas as entradas ou outra forma qualquer);
* Avaliar se aquela taxa de acertos do algoritmo significa algo para o negócio, se vale a pena a taxa de erros.

Os algoritmos requerem número como input.

Passos ideais para podermos ter um modelo bem avaliado:
* Treinar com um grande conjunto para que crie uma regra de generalização;
* Testar contra um grande conjunto de dados conhecidos para validar o modelo;
* Após a decisão de qual algoritmo usar, rodar novamente contra um conjunto não visto antes pelo modelo para obter uma taxa de acerto mais real.




## Tipos de Aprendizado

### Aprendizado Supervisionado
Quando queremos prever uma variável _Y_ que depende de outras variáveis _X_.
Neste cenário mostramos à máquina o par _(X,Y)_ e então pedimos a ela que tente produza Y a partir de X. Com vários pares de (X,Y) esperamos que ela consiga generalizar uma regra que a partir das variáveis X nos retorne Y.
Queremos ```f(X) = y + E``` onde E é a nosso ruído aleatório.
Podemos identificar dois tipos de problemas dentro do regime de aprendizado supervisionado: *regressão* e *classificação*.
* Regressão: queremos prever um valor contínuo, como renda, peso, demanda de algo, ângulo da direção de um carro automático, etc;
* Classificação: queremos prever um valor discreto, ou seja, classificar segunda uma categoria, como identificar a presença de uma doença a partir dos sintomas, identificar rostos em uma imagem, classificação de livros, etc.

Alguns algoritmos que podem ser utilizados: regressão linear, regressão logística, árvores de decisão, florestas aleatórias, máquinas de suporte vetorial, k-vizinhos mais próximos, Bayes ingênuo e redes neurais artificiais.


### Aprendizado por reforço
Quando dado o estado de um certo ambiente queremos mapear que ações tomar. Queremos que a máquina desenvolva alguma política que mapeie os estados S e ações A. Cada par _(S, A)_ está associado a uma recompensa.
Algumas das aplicações: videogames, movimentação de robôs, simulação de ambientes complexos, aprender estratégias, etc.




### Variáveis Dummies
Para variáveis categoricas podemos usar variáveis Dummies, onde presentamos o valor da categoria por um conjunto de variáveis.



### K-Fold
Consiste em dividir a base de treino em algumas partes para que não haja influência da ordem dos dados.


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



## Libs
* Pandas: data structure and analisis, trata sozinho os dados dummies.
