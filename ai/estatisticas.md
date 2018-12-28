# Estatística

## Conceitos

### Definições

* Tratamento: em um experimento, é a maneira que o pesquisador manipula/interage com o grupo em estudo (indivíduos). Dependendo do experimento e a forma do tratamento podemos ter resultados diferentes.

* Construto: é qualquer coisa que é difícil para medir pois ela pode ser medida de várias maneiras diferentes (felicidade, fome, gasolina [galão ou litros]).
* Definição operacional do construto: é a unidade de medida que está sendo usada para o construto, pois sem ela não seria possível medir.

* População: todos os indíviduos do grupo;
* Amostra: parte dos indíviduos do grupo.

* Parâmetro: Define uma característica da população;
* Estatística: Define uma característica da amostra.

* Mostrar relações: faça um estudo observacional ou pesquisa de campo (survey);
* Mostras causalidade: faça um experimento controlado.

* Estudo observacional: quando um pesquisador observa um grupo de indivíduos e não introduz um tratamento.

* Grupo de tratamento: é o grupo do estudo que recebe níveis variados das variáveis independentes, esses grupos são usados para medir o efeito do tratamento.

* Grupo de controle: é o grupo do estudo que não recebe tratamento, este grupo é usado como base de comparação com os grupos de tratamento.

* Placebo: algo que é dado ao grupo de controle para eles pensarem que estão recebendo tratamento.



#### Blinding e Double Blinding
É uma técnica usada para reduzir o viés/tendência em um experimento.

Blinding é quando apenas os individuos não sabem se receberam o tratamento ou o placebo.

Double blinding é quando nem os indivíduos e nem os pesquisadores sabem quem recebeu o tratamento e quem recebeu o placebo.



### Tipos de variáveis:
* Independente: é a variável do estudo que o pesquisador escolheu para manipular;
* Dependente: é a variável do estudo que o pesquisador escolheu para medir/verificar durante o experimento.

### Tipos de dados
Para aplicar um teste estatístico é necessário conhecer os seus tipos de dados e curva normal para determinar o melhor teste.

* Dados categóricos
  * Um diferente do outro;
  * Sem peso ou relação.

* Dados ordinais
  * Tem uma ordem;
  * Não é possível definir a diferença exata entre dois valores pois não é mensurável (ex.: nota).

* Dados intervalar
  * Tem uma ordem;
  * É possível definir a diferença entre dois valores é mensurável (ex.: temperatura).

* Dados racionais
  * Parecido com intervalar porém o valor 0 significa ausência de valor.



#### Média
* Sensível aos outliers (pontos fora da curva, exceções):
  * Exemplo numa avalição com notas de 1 a 10, tendo uma distribuição baixa próxima ao 1 mas por causa de um 10  lá sua média aumenta.
* Ideal quanto a distribuição é estável;
* Ideal para dados Intervalares, salvo aqueles que possuam muitos outliers;
* Não é ideal para dados ordinais, salvo os ordinais com uma distribuição normal;

* R: função `mean(list)`

#### Mediana
* Elemento que está no meio quando os dados estão ordenados.
* Se a quantidade for impar, pega o elemento central. Se for par, podemos pegar o meio -1 ou +1, ou ainda fazer a média aritméticas dos elementos centrais.
Ex: 1 1 2 3 4, mediana é 2.
* Ideal para dados Ordinais.

* R: função `median(list)`

#### Moda
* Elemento que mais se repete;
* Ideal para dados Ordinais de uma distribuição bastante dispersa e cheia de outliers.

* R: não existe uma função nativa
* Função customizada:
```
mode <- function(x) {
     ux <- unique(x)
     ux[which.max(tabulate(match(x, ux)))]
}
```


### Distribuição por Quartis
Antes de calcularmos a distribuição é ideal fazermos a limpeza dos dados para que os outliers não nos engane.

Uma forma de fazer isso é com Quartis. O que queremos é eliminar os primeiros 25% e os últimos 25%, trabalhar apenas com os 50% do meio.
Pegamos a quantidade total e dividimos por 4 para obter a posição que divide os primeiros 25%.
Depois fazermos _3 * n / 4_, onde n é total de elementos, para obter a posição que divide os últimos 25%.

No R, podemos usar o Boxplot para termos uma visão geral da nossa distribuição, nele podemos visualizar o 1 e 3 quartis, mediana e a posição dos nossos 50% que iremos trabalhar.

* R: função `summary(list)` nos mostra informações dos quartis e outros usados no Boxplot.


### Desvio Padrão
Nos informa qual a média da dispersão dos dados. Um desvio padrão grande mostra que os dados estão bem dispersos enquanto um desvio padrão pequeno mostra que os dados estão distribuídos mais próximos à média.

Para calculá-lo primeiro precisamos a variância média dos nossos dados, usamos a média (usando media, mediana ou moda) e então para cada elemento calcular a diferença desta média.

> Variância = ((elemento_1 - media) ^ 2 + (elemento_2 - media) ^ 2 + ...) / n

Nota: elevamos ao quadrado para eliminar os negativos que poderiam nos deixar com um 0.
A partir da variância média, basta extrairmos a raiz quadrada.
> Desvio padrão = Variância ^ 1/2

* R: função `var(list)` calcula a variância e `sd(list)` calcula o desvio padrão.



## Gráficos

#### Histograma
* Frequência que as coisas acontecem numa distribuição;
* Possui uma curva normal que mostra a distribuição esperada para determinada amostra de dados.

* Função do R: `hist()`


# Linguagem R

### Funções
* `png(file="C:\filename", width=x, height=y)`:
  * inicia a escrita de imagens em buffer, o proximo gráfico gerado será salvo neste arquivo e não será exibido;
  * para efetuar a escrita do buffer usamos: `dev.off()`.
* `read.csv(file="caminho/arquivo.csv")`
  * lê o arquivo csv e retorna o dataset
  * usamos `variavel$coluna` para acessar os dados de uma coluna.
* `summary(list)`:
  * exibe informações sobre os dados como primeiro e ultimo elemento, 1 e 3 quartis, media e mediana.
* `boxplot(list)`:
  * exibe o Boxplot dos dados.
* `var(list)`:
  * calcula a variância dos dados.
* `sd(list)`:
  * calcula o desvio padrão.
