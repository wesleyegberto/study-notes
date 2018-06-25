# Microservices e Database com Zero Downtime


## Separação do Banco
* Identificar os dados que são lidos e escritos juntos;
* Migrar o código junto com o banco:
  * Ao extrair um microservice manter escrevendo na mesma base;
  * Posteriormente alterar para escrever no novo banco;


## Migração de Database
* Batch size;
* Utilizar shards nos updates (sem dar Lock na tabela inteira):
  * Ex:
	`
	ALTER TABLE my_table ADD COLUMN correct VARCHAR(20);
	// Batches
	UPDATE my_table SET correct = wrong WHERE id BETWEEN 1 AND 100;
	UPDATE my_table SET correct = wrong WHERE id BETWEEN 101 AND 200;
	`


## Cenários Comuns
As etapas de cada cenário visam manter compatibilidade entre a versão antiga e a nova.
Durante as etapas de migração de cada cenário é necessário ficar realizando consultas no banco para validar o comportamento da migração.
Caso a tabela tenha alguma constraint então removê-las antes da migração e adicionar somente no fim da migração (pode ocorrer depois de vários deploys - utilizar um novo ticket para criar).
Cenários comuns:
* Adicionar uma coluna
* Renomear uma coluna
* Alterar tipo/formato de uma coluna
* Remover uma coluna

### Adicionar uma coluna
Passos ideais:
* ADD COLUMN;
* Código calcula o valor de leitura e escreve na nova coluna;
* Atualize os dados utilizando shards;
* Código lê e escreve na nova coluna.

### Renomear uma coluna ou Alterar tipo/formato de uma coluna
Os passos para os dois cenários são iguais.
* ADD COLUMN;
* Código lê da coluna antiga e escreve em ambas;
* Copiar os dados usando shards;
* Código lê da nova coluna e escreve em ambas;
* Código lê e escreve na nova coluna;
* Remover a coluna (mais tarde - numa limpeza - pois é uma ação destrutiva).

Caso a coluna antiga utilize um algoritmo antigo e a nova coluna utiliza um novo algoritmo, então mantenha os dois em produção, cada um escrevendo nos seus respectivos campos. Assim não há perda de informação pois ainda temos a versão antiga escrevendo.

### Remover uma coluna
* NÃO REMOVER;
* Código primeiro para de ler a coluna mas continua escrevendo nela;
* Código para de escrever na coluna;
* Remover a coluna (mais tarde).



## Ferramentas de Migração de Banco
* Flyway
* LiquiBase



## Links
* [Palestra Edson Yanaga](https://www.youtube.com/watch?time_continue=1&v=glFEHRe3aMA)
* 
