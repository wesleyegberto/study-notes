# Mongodb

Grande flexibilidade e liberdade na estrutura;

Desvantagens:
Cenários onde precisamos fazer muitas operações de agregação em uma única query, isso tem muito custo para o MongoDB;


Por default o diretório DB usado pelo MongoDB: /data/db

Os documentos não precisam ter aspas no atributo, mas para evitar problemas de encode é recomendável manter.

Rodar o MongoDB passando um diretório diferente para o banco:
`./mongod --dbpath <dir>`

Rodar o MongoDB-cli:
`./mongo`

Para importar um arquivo de dados:
`./mongoimport -c minhaColecao --jsonArray <meuArquivoJson.json`


## Storage
* Storage engine: componente primário responsável pelo gerenciamento dos dados;
* Journal: logs das alterações entre checkpoints que podem ser usados para restauração após um hard shutdown;
* GridFS: ideal para o armazenamento de documentos que ultrapassam o limite de 16 mB.


## Storage Engines
Componente responsável por determinar como os dados são armazenados em memória e em disco.

MongoDB suporta diferentes engines, cada uma ideal para um workload diferente.
A partir da versão 3.2 o WiredTiger é a engine padrão. Antes dessa versão é usado a engine MMAPv1.

Na versão Enterprise é disponibilizado a engine In-Memory.
Podemos alterar a engine atravé do parâmetro: --storageEngine

### WiredTiger
Provê:

* Document-level concurrency model:
  * permite que vários clientes alterem diferentes documentos sem uma coleção;
  * utiliza lock otimista na maioria das operações;
  * quando dois clientes tenta alterar o mesmo documento ao mesmo tempo, um deles gerará um conflito de escrita e o próprio MongoDB irá tentar novamente a operação de forma transparente;
  * algumas operações como deleção de coleção exige lock do banco.
* Snapshots (documento consistente em memória) e Checkpoint (snapshot escrito em disco):
  * utiliza MultiVersion Concurrency Controle (MVCC);
  * no início de uma operação a engine disponibiliza um snapshot (point-in-time) dos dados que são consistente na memória;
  * quando vai escrever no disco, a engine armazena todos os dados de um snapshot de uma maneira consistente em todos os arquivos;
  * os dados que foram persistidos (durable - consegue ser restaurado após um shutdown) funciona como um checkpoint nos arquivos de dados;
  * MongoDB configura a engine para gerar os checkpoints (escrever em disco) no intervalo de 60 segundos ou a cada 2 gB de logs (journal);
  * O novo checkpoint se torna acessível e permanente após a modificação da tabela de metadados da engine de forma atômica, e após isso a engine libera páginas ocupadas por checkpoints antigos;
  * Sem o journaling, o MongoDB consegue recuperar os dados apenas do último checkpoint, as alterações feitas depois do último checkpoint (os snapshots que não foram persistidos) não serão recuperados.
* Compressão:
  * suporta compressão em todos os dados e indexes;
  * diminui o custo de armazenamento mas aumenta gasto de CPU;
  * por padrão a engine utiliza o Snappy para compressão dos dados e compressão prefixa dos indexes (economiza memória e disco armazenando os prefixos das indexes-key somente uma vez);
  * é possível configurar compressão a nível de coleção e index;
* Memória:
  * a engine utiliza cache interno e filesystem cache;
  * a partir da versão 3.4 o cache interno utiliza por padrão o maior valor entre:
    * 50% RAM - 1 GB
    * 256 MB
  * através do filesystem cache o MongoDB utiliza automaticamente toda a memória disponível que não esteja sendo usada pelo cache da engine ou por qualquer outro processo, os dados armazenados nesse cache é compressado;
  * Podemos alterar o tamanho máximo do cache interno.
* e outros.

A engine usa write-head transaction junto com checkpoints para garantir a durabilidade dos dados;
Caso tenha ocorrido um hard shutdown, o Mongodb utiliza o journal para refazer todas as alterações desdes o último checkpoint.
A engine usa a lib de compressão Snappy para comprimir os logs, é possível alterar a lib ou desativar a compressão.
Se o registro de log for menor que 128 KB então não é feito compressão.


## Journal
Log de todas as alterações feitas entre os checkpoints.
O journaling pode ser desativado para diminuir o overhead porém após um hard shutdown apenas o último checkpoint será carregado, alterações após a última escrita serão perdidas.


## Coleções
São as "tabelas", não possuem estrutura fixa;


## Comandos CLI
Comandos de estrutura:

* show dbs: listar os databases;
* use database_name: usa um database, se ele não existir então será criado um quando houver operação (criação de collection ou insert);
* show collections: listar as coleções;
* Criar coleção: `db.createCollection("<nome_colecao>")`:
	* Ex.: db.createCollection("alunos").
* Inserção: `db.<colecao>.insert(<documento>)`:
  * Se a colecao não existir então será criado uma;
  * Ex.: db.alunos.insert({ "nome": "wesley", "data_nascimento": new Date(1993, 12, 30) }).
* Removendo documento: `db.< colecao>.remove(<documento_filtro>)`:
  * Ex.: db.alunos.remove({"cep": "05454100"}).


### Pesquisa
Comandos para pesquisa:
* `db.<colecao>.find(<documento_filtro>, <documento_opcoes>)`
* `db.<colecao>.findOne(<documento_filtro>)`

Para formatar a saída basta chamar .pretty()
Ex.:
* db.alunos.find() -> lista todos
* db.alunos.find({"nome": "Wesley"}) -> filtra por nome
* db.alunos.find({"habilidade.nome": "java"}) -> filtra por um atributo aninhado
* db.alunos.find().pretty()
* db.alunos.find({ "notas": "10" }) -> filtra os alunos que tem um 10 (já pesquisa dentro do array)

Quando pesquisamos um atributo que é um documento temos que passar exatamente o mesmo documento (mesmos campos, valores e ordem dos campos):
* db.alunos.find({ "curso": { "nome": "Física", "area": "Exatas" } })

Para projetar os campos que queremos trazer basta passar no documento de opções:
* Trazer os campos especificados e o _id:
  * db.alunos.find({ "nome": "wesley" }, { "nome": 1, "habilidade": 1 }
* Para não trazer o _id basta setar 0 nele:
  * db.alunos.find({ "nome": "wesley" }, { "nome": 1, "habilidade": 1, "_id": 0 }

Ordenando (1 para asc e -1 para desc) e paginando:
* db.alunos.find().sort({ "nome": 1 }).skip(10).limit(10)

#### Operadores:
https://docs.mongodb.com/manual/reference/operator/query/
* $or, $and:
  * db.alunos.find({ $or: [ { "ano":  "2016" }, { "ano": "2017" } ] })
* $in, $nin:
  * db.alunos.find({ "ano": { $in: [ "2016", "2017" ] }})
  * db.alunos.find({ "ano": { $nin: [ "2013", "2014", "2015" ] } })
* $lt, $gt, $lte, $gte, $eq, $ne
  * db.alunos.find({ "media": { $gte: "7.0" }})
* $exists: verifica se o atributo existe no documento
  * db.alunos.find({ "rg": { $exists: true } })
* $type: verifica o tipo do atributo no documento
  * db.alunos.find({ "idade": { $type: "int" } })

### Atualização
Formato padrão do comando:
* `db.<colecao>.update(<documento_filtro>, <documento> [, <documento_forma>])`

Por padrão o update() substitui apenas o primeiro documento que conseguir filtrar:
 * db.alunos.update({ "cidade": "barueri" }, { "cidade": "Barueri"}) -> após a execução teríamos apenas os atributos _id e cidade no documento encontrado;

Para atualizar apenas um atributo do documento, usamos o operador $set:
* db.alunos.update({ "cidade": "barueri" }, { $set: { "cidade": "Barueri" } }) -> irá atualizar apenas o atributo cidade de um documento

Para atualizar mais de um documento precisamos passar a forma multi:
* db.alunos.update({ "cidade": "barueri" }, { $set: { "cidade": "Barueri" }}, { "multi": true }) -> atualiza o campo cidade de todos os documentos que filtrar.

#### Operadores
[Documentação](https://docs.mongodb.com/manual/reference/operator/update/)

Tem vários operadores, abaixo são listados alguns:
* $set: atualiza apenas o campo passado
  * { $set: { "cidade": "Barueri" } }
* $push: adiciona um elemento numa lista (pode repetir)
  * { $push: { "notas": 10 } }
* $addToSet: adiciona um elemento num set (não repete)
  * { $push: { "codigos": 1922 } }
* $each: permite executar uma operação para cada elemento do array, devemos combinar com outra operação
  * { $push: { "notas": { $each: [ 10, 9, 7.5 ] } } }


## Tipos pré-definidos
Alguns tipos pré-definidos.
* Coordenada:
  *  Precisa ter um atributo coordinates do tipo array com os dois pontos de latitude e um atributo type com o tipo da coordenada
  * Ex. de documento aluno com coordenada: { "nome": "Wesley", "localizacao": { "rua": "Rua do Bairro", "type": "Point", "coordinates": [-66, -44] } }



## Index
[Documentação](https://docs.mongodb.com/manual/indexes/)

Comando:
`db.<colecao>.createIndex(<campo_e_tipo_index>, [<opcoes>])`

Características:
* Permite definir um index para efetuar pesquisas de forma bem mais eficiente. Sem ele é efetuado um scan;
* Um index armazena um ou mais campos da tabela em estruturas de dados ordenada que são eficientes para efetuar um scan;
* Esses campos podem ser toplevel ou sublevel no documento;
* Essas estruturas são determinadas de acordo com o tipo do index;
* MongoDB utiliza árvores B (O(log) para inserção, remoção e busca) para armazenar os campos do index;
* Uma index pode ter um TTL (time-to-live) e remover as keys automaticamente;
* MongoDB pode utilizar a intersecção de indexes para satifazer uma query;
* Um index requer no mínimo 8 KB disponível para dados.

Se a coleção é muito grande e a aplição precisa consultar os dados durante a criação do index, é possível efetuar a criação em background.
Por padrão o MongoDB cria um index _id quando a coleção é criada e esse index não pode ser deletado.

### Tipos de Índices
O MongoDB disponibiliza um conjunto de tipos de index para suportar tipos de dados e consultas específicas.
Os índices podem ter tipos:

* Single Field
  * Utiliza apenas um campo do documento que pode ser ordenado (asc/desc)
  * Para ordenar o MongoDB percorre em uma das direções
  * No momento da criação definimos a ordem padrão:
    * db.alunos.createIndex({ "media": 1 })
    * Usando: db.alunos.find({ "media": { $gt: 7 } })
  * Também podemos criar index utilizando um subdocumento inteiro ou algum atributo dele (o que nos permite utilizar apenas esse campo na pesquisa)
    * Subdocumento inteiro:
      * db.alunos.createIndex({ "curso": 1 })
      * Usando:
        * Temos que passar o documento inteiro corretamente e com os campos na ordem exata
        * db.alunos.find({ "curso": { "nome": "Ciência da Computação" } })
    * Atributo do subdocumento:
      * db.alunos.createIndex({ "curso.nome": 1 })
      * Usando: db.alunos.find({ "curso.nome": "Ciência da Computação" })
  * Para criar chave com campo único (CPF, CNPJ e etc) passando o parâmetro unique:
      * db.alunos.createIndex({ "cpf": 1}, { unique: true })

* Compound Index:
  * Utiliza mais de um atributo do documento onde cada um pode ter uma ordem, porém a ordem do resultado será a ordem da sequência dos campos
  * O limite de atributos é 31 e não é permitido usar atributos do tipo hashed
  * Criando:
    * db.alunos.createIndex({ "media": 1, "sexo": -1, "turma":1 })
  * Para utilizar o index precisamos seguir a ordem dos atributos declarado no index e sempre começar pelo primeiro atributo (prefixo), depois do primeiro podemos pular um ou outro desde que estejam em ordem:
    * db.alunos.find({ "media": { $gt: 7 }})
    * db.alunos.find({ "media": { $gt: 7 }, "sexo": "M" })
    * db.alunos.find({ "media": { $gt: 7 }, "turma": "INF3DM" })
  * A consulta abaixo não usa o index:
    * db.alunos.find({"sexo":"M":})
  * Quando ordenamos usando index precisamos seguir exatamente a sequência dos atributos e o inverso/reverso de TODOS os atribuso
    * Usa o index:
      * db.alunos.find().sort({ "media": -1, "sexo": 1, "turma": -1 })
    * Não usa o index:
      * db.alunos.find().sort({ "media": -1, "sexo": -1, "turma": 1 })
      * db.alunos.find().sort({ "media": -1, "sexo": -1, "turma": -1 })
      * db.alunos.find().sort({ "media": 1, "sexo": 1, "turma": 1 })

* Multikey Index:
   * Quando o atributo index é um array o MongoDB cria um index-key para cada item do array
   * É feito automaticamente pelo MongoDB quando o atributo é um array

* Geospatial Index:
   * MongoDB disponibiliza dois tipos de index: 2d e 2dsphere
   * Usado para efetuar consultas que utilizem localização geoespacial (locais próximos a uma coordenada)

* Text Index:
   * Usado para pesquisar em uma coleção de palavras
   * É possível definir pesos, tokens delimitadores, outros atributos que não sejam text e outras coisas

* Hash Index:
   * Podemos criar um index com um atributo que é armazenado em hash
   * Suporta apenas buscas exatas, não temos busca por range e nem efetua ordenação
   * MongoDB calcula automaticamente o hash do atributo quando esta index é utilizada
   * Se o atributo for float, é feito truncamento antes do calculo do hash, o que gera colisão
   * Criação:
      * db.alunos.createIndex({ "senha": "hashed" })


## Agregadores
Permite processar um conjunto de dados e retornar o resultado da computação.
Consegue agrupar um cojunto de valores de vários documentas e efetuar operações sobre esse conjunto.

É o jeito prefiro para fazer agregação de dados porque:
* Utiliza operações nativas dentro do MongoDB para tornar as agregações eficientes;
* Utiliza indexes em algumas fases e também tem uma fase de otimização;

MongoDB disponibiliza três tipos de agregações:

* Single Purpose Aggregation Methods:
   * Mais simples e limitada, permite apenas operações de agregações em uma coleção;
   * Operações: count, distinct, etc;
* Aggregation Pipeline:
   * Os documentos percorrem vários estágios de operações e produzem um resultado agregado;
   * Algumas operações disponíveis:
      * Filter: filtrar documentos com uma query;
      * Transformation: transforma um documento em outro;
      * Sorting: ordenação por um ou mais atributos;
      * Grouping: agrupamento por um ou mais atributos;
      * Arrays: agregação de elementos (simples ou subdocumentos) de array;
      * Operadores: relacionais, aritméticos, operação em textos, booleanos, conjuntos, etc;
 * Map-Reduce Functions:
   * Geralmente tem duas fases: Mapping e Reducing. Também pode ter uma fase de finalização para modificar o resultado final:
      * Fase Mapping: Efetua o processo de cada documento e gera um ou mais objetos como saída;
      * Fase Reducing: Efetua a combinação dos objetos de saída
   * Consegue filtrar os documentos de entrada e limitar e ordenar os resultados;
   * Por ser mais complexo e menos eficiente do que pipelines porém consegue utilizar funções javascript nas suas fases para ter mais flexibilidade.

Exemplo de busca agregada que pesquisa as pessoas próximas a uma coordenada:
```
db.alunos.aggregate([{
	"$geoNear": {
		"near": {
			"coordinates":[-23.588055, -46.632403], // input do filtro
			"type":"Point"
		},
		"distanceField": "distancia.calculada", /// novo campo no documento filtrado com o resultado do cálculo
		"spherical": true, // forma de cálculo
		"num": 4 // traz até o 4th
  },
  { $skip: 1 } // pula o primeiro
]);
```


## Padrões para Aplicações

* **Efetuar update somentes nos campos que foram alterados:** diminui o uso de rede e o overhead do banco.
*  **Evitar negação nas queries: ** Mongodb não indexa a falta do valor e condições de negação podem requerer scan em todos os documentos.
*   **Usar covered queries sempre que possível: ** são queries que retornam os dados diretos dos índices sem acessar os documentos, logo, são mais eficientes. Para que isso ocorra, todos os campos do filtro e todos os campos da projeção precisam estar no índice.
*   **Evitar queries entre shards: ** queries que são roteadas para multiplos shards não escalam tão bem conforme mais shards são adicionados.
*   **Escolha o nível de escrita apropriado: ** setar o writeConcern por conexão, banco de dados, coleção ou por operação de acordo com a necessidade para ter máxima performance e response time.
*   **Escolha o nível de leitura apropriado: ** setar o readConcern adequadamente pode ter um impacto considerável na latência, portanto o maxTimeMS também deveria ser setado para gerar timeount em operações longas.
*  **Use consistência casual quando precisa: ** consistência casual garante que toda operação de leitura enxergará os dados que foram escritos numa operação anterior independente do node que está servindo a requisição. Esse modo pode minimizar a latência.
*  **Garanta distribuição uniforme dos shard keys: ** quando não são uniformemente distribuídos para leitura e escrita, essas operações podem ser limitadas pela capacidade de um shard único.

*  **Use índice composto ao invés de interção de index: ** para melhor performance quando consultando por multiplos predicados use índices compostos.
*  **Use índices parciais: ** reduz o tamanho e aumente a performance dos índices ao incluir apenas os documentos que serão utilizados através daquele índice.
*  **Evite regex que não são rooted ou left anchored:** índices são ordenados por valor. Wildcards são ineficientes e podem causar scan no índice inteiro. Trailing wildcard pode ser eficiente se houver case-sentive caracteres na expressão.
*  **Use otimização de índice disponível no WiredTiger:** além da compressão padrão, é possível ter índices nos seus volumes separados, permitindo paginação mais rápidas e baixa contenção.
*  **Identifique e remova índices obsoletos:** para entender a efetividade dos índices existentes em uso podemos usar *$indixStats* aggregation stage para determinar a frequência de uso do índice.