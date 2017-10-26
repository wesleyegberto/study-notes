# Rede Neurais

### Perceptron
Primeiro modelo de neurônio artificial desenvolvido nas decadas de 50 e 60 por Frank Rosenblatt (inspirado no trabalho de Warren McCulloch e Walter Pitts).
Recebe um ou mais inputs e produz um output.
[Representação Perceptron](http://neuralnetworksanddeeplearning.com/images/tikz0.png)

Para calcular o output, o perceptron utiliza pesos (_weights_) associados a cada input (w1, w2, ..., wn) que nada mais são que números reais que expressão a importância do respectivo input para o output, ou seja, basicamente pesa as evidências para tomar uma decisão.
O output do perceptron é determinado se o valor do resultado da soma dos inputs multiplicados pelos seus pesos (denotado por _w*x_ onde _w_ e _x_ são vetores com wights e inputs respectivamente) é maior ou menor que algum valor limite (_threshold_).

Reformulando, temos: ```output = { 0 se w*x <= threshold, 1 se w*x > threshold }```
Podemos passar o threshold ara o outro lado da equação e substituí-lo pelo _bias_ do perceptron (b = -threshold).
```output = { 0 se w*x + b <= 0, 1 se w*x + b > 0 }```

O _bias_ é a medida do quão fácil é levar o perceptron para produzir como saída 1, ou seja, quão fácil atingir o threshold. Pensando em termos biológicos, o quão fácil é ativar o perceptron. Um perceptron com um valor alto no bias é muito fácil ativá-lo e um bias bem negativo torna bem difícil ativá-lo.

>Dado um perceptron que recebe 3 inputs (i1, i2 e i3) e gera um output, podemos:
Soma = i1 * w1 + i2 * w2 + i3 * w3 ou w*x
se Soma > threshold então output = 1 senão output = 0

Variando os weights e o threshold obtemos diferentes modelos de tomada de decisão para um perceptron e que, quandro agrupado, também varia o modelo da rede toda.

Numa rede neural temos vários perceptrons, alguns utilizam como entrada o input do usuário (primeira camada) e outros utilizam como entrada o output de outros perceptrons (camada interna - _hidden layer_).
[Representação Rede Neural com Perceptrons](http://neuralnetworksanddeeplearning.com/images/tikz1.png)

Perceptrons também são usados para calcular funções com lógicas elementares (como AND, OR e NAND).
Um perceptron com duas entradas com weights de -2 cada e um bias de 3, ao receberem entrada de 0 ou 1 conseguem efetuar o NAND das duas entradas.
> i1 = 1, i2 = 0; temos: i1 * w1 + i1 * w2 + 3 = 1 * -2 + 0 * -2 + 3 = 1
> i1 = 1, i2 = 1; temos: w*x = 1 * -2 + 1 * -2 + 3 = -1

Os perceptrons conseguem simular qualquer operação lógica ou circuitos delas. E por esses circuitos serem universais na computação, isso implica que os perceptrons também são universais.
Porém, por causas dos _weights e bias_ as soluções podem ser tunáveis, podemos alterar os valores para produzir os resultados aceitáveis. E com algoritmos de aprendizado é possível que essas variáveis sejam tunadas automaticamente conforme os treinos.







### Propostas para exercitar
* Desenvolver uma rede neural de perceptrons que efetuem soma, subtração e multiplicação de bits.


