# Kafka

Plataforma distribuída de mensagens com alto throughput.

Escrito em Scala (a versão baixa precisa ser correspondente).


#### Características:
* Alto throughput;
* Escalável horizontalmente (réplicas de brokers);
* Confiável e durável (através de réplica entre os followers e retation policy);
* Baixo acoplamento entre Produtor e Consumidor;
* Pub/sub em uma stream de registros;
* Armazena os registros em uma maneira tolerante à falhas (leader e followers);
* Processa os registros conforme eles ocorrente (fila);
* Armazena os dados em categorias chamadas Tópicos;
* Cada registro é uma tupla composta por ID, Payload e Timestamp;
* Utiliza protocolo binário sobre o protocolo TCP que é versionado e mantém retrocompatibilidade;
* Provê georeplicação do cluster com Kafka MirrorMaker;
* Provê suporte para multi-tenancy permitindo configurar quais tópicos podem produzir ou consumir dados e quotas de recursos das requisições;
* Pode atuar como um MQ, Storage e Stream Processing (comportamento é definido conforme configuração do Tópico X Partições);
* No geral, a escalabilidade do Kafka é determinado pela quantidade de partições sendo gerenciadas em multiplos brokers.

### Garantias
* Mensagens enviadas de um mesmo producer para um tópico serão persistidas na mesma ordem de envio;
* Um consumidor enxerga os registros na mesma ordem que são persistidos;
* Para um tópico com fator de replicação N, será tolerado até N-1 servidores falhandos sem que haja perda de registros commitados.


## Componentes

### Cluster e Broker
Um bloker é um processo/aplicação que executa o Kafka em si onde são salvos os detalhes da configuração e os tópicos com suas mensagens.
Um cluster é um conjunto formado por um ou mais brokers independentes onde podem está rodando em uma mesma máquina ou em diferentes máquinas.
Um cluster é gerenciado por um Zookeeper, um serviço centralizado que mantem metadados sobre o cluster dos nodes distribuídos:
* Informações de configurações;
* Health status;
* Group membership.
Quando escalamos o Kafka estamos escalando a quantidade de Brokers para atingir determinado throughput (Ex.: LinkedIn tem 1.400 brokers que atingem um throughput de 1,1 trilhão de mensagens por dia.).

### Producer
Aplicações que produz/publica as mensagens.

### Consumers
Aplicações que consomem as mensagens.

### Arquivos:
* server.properties
  * contem configurações do broker Kafka.



## Topicos
[Imagem detalhada](https://kafka.apache.org/11/images/log_anatomy.png)
* Uma entidade lógica que categoriza os registros foram publicados.
* Todos os tópicos são multi-subscriber, podendo ter 0-N subscribers.
* Para cada tópico, o cluster Kafka mantém um ou mais logs particionados. Cada partição é um sequência ordenada (pelo timestamp) e imutável de registros que é continuadamente escrita (estrutura de log commit).
* Cada registro de log nas partições tem:
  * ID sequencial chamado **Offset** que é unico dentro de uma partição;
  * Timestamp com o momento que foi criado (seja no broker ou no producer - depende da configuração do broker);
  * Body que é a mensagem;
  * Pode conter uma Key que permite salvar informações adicionais e, ainda, pode determinar em qual partição será salva (routing key);
* O Kafka persiste todos os registros (consumidos ou não) de um tópico durante um período de tempo configurado (**retention policy**, tem valor padrão de 168h ou 7d que é configurável por tópico). Após esse tempo o registro é removido para liberar espaço. Isso não afeta a performance do Kafka pois esta é efetivamente constante dependendo apenas do tamanho do registro.
* O consumer que é responsável por controlar quais registros já leu. Isso é feito usando o *offset* do registro. Assim, normalmente, um consumer avança seu offset conforme processa os registros. Mas também é possível voltar e avançar a vontade ao alterar esse offset. Isso torna o consumo de registros bem leve, sem muito impacto no cluster Kafka.
* Quando um offset do consumer é commitado o kafka salva esse estado num tópico interno chamado `__consumer_offsets`.

### Partições
* É a representação fisica do tópico. Na pasta do server terá uma pasta `<NOME_DO_TOPICO>-<NUMERO_DA_PARTICAO>`.  
* As partições de um tópico permite este escalar seu tamanho (quantidade de registros) além do permitido no node do cluster (visto que um partição pode estar em um node e outra em outro) e também atua como uma unidade de paralelismo (visto que controla o commit dos registros por partição, logo podemos ter vários locks em um mesmo tópico permitindo assim escrita paralela).
* Kafka somente controla os registros por partição (offset único dentro da partição) e não entre as partições de um tópico. Caso a aplicação precise de controle de todos os registros por tópico (offset único dentro de um tópico) então será necessário criar um tópico que tenha apenas uma partição, mas isso significa também que terá apenas um consumidor por grupo.
* Cada partição pode ser armazenada em diferentes nodes do cluster e cada partição também pode ser replicadas entre um número configurado de nodes para tolerância à falhas.
* Cada partição tem um *Leader* e zero ou mais *Followers*.
  * Leader: é responsável por lidar com todas as requições de leituras e escritas na partição
  * Followers: apenas passivamente replicam os registros.
* Se um lider falha, então um de seus followers será elegido a lider. Os followers que são elegíveis são do ISR (in-sync replicas), grupo de followers que estão em sincronia com o lider (receberam o último commit).
* Um tópico é considerado health se o valor da ISR é igual ao fator de réplica do momento da sua criação (ISR com lider + N followers = replication-factor).
* Quando o ACK do registro está a nível de replica entre os followers, essa mensagem só será comitada quando todos os followers receberem, se o lider morrer nesse meio tempo o follower elegido a lider terá apenas as mensagens commitadas nestes (replicadas), então pode ocorrer perda das mensagens não commitadas no lider. 
* Cada node do cluster atua como lider em alguns tópicos e follower em outros, logo, a carga de requisições é bem balanceada no cluster.
* Quanto mais partições maior é a sobrecarga no Zookeeper.



## Producers
- Todos os nodes do cluster fornecem metadata para informar qual node está vivo e qual node contém os liders das partições de um tópico. Com isso após a leitura desse metadata, o producer pode enviar mensagens diretamente para o lider.
- O producer é responsável por escolher qual registro será enviado para um determinada partição. Essa escolha pode ser feita via round-robin para balancer o load ou pode ser selecionada  de forma específica (com um routing key ou definindo diretamente a particão).
- O producer é configurado através de properties que é passado no seu construtor. Algumas propriedades:
  - `max.in.flight.request.per.connection`: define a quantidade máxima de requisições em uma conexão;
- Também é possível enviar mensagens em modo batch, pode ser configurado para enviar quando atingir N mensagens (configurado através do `batch.size` e `batch.memory`) e/ou após um determinado tempo (configurado através do `linger.ms`).
- Garantias de entrega é configurado através do `acks`:
  - 0: Fire and forget
  - 1: Leader acknowledged
  - 2: Replication quorum acknowledged (todos os brokers do ISR)
- Quando ocorre um erro o produtor pode tentar entregar novamente os registros. Esse retry pode ser configurado com:
  - `retries`: quandidade de tentantivas
  - `retry.backoff.ms`: tempo entre as tentativas
- Quando `retries` > 0 pode ocorrer que a mensagem esteja fora de ordem (pois uma mensagem em retry pode ser escrita depois que outra foi escrita de primeira - sem erro), para garantir ordem mesmo com retry é preciso configurar o `max.in.flight.request.per.connection` = 1 para que exista apenas uma mensagem em retry. Porém isso poderá diminuir o throughput do producer.
- É possível usar a key da mensagem para calcular a partição em que será escrita, mantendo assim um grupo de mensagem em uma mesma partição. É utilizado a quantidade de partições para calcular o hash, logo, quando alterado a quantidade de partições de um tópico é possível que passe a escrever em outro tópico. Caso o agrupamento das mensagens seja importante é necessário já criar o tópico com partições suficientes.



## Consumers
- Cada consumidor se tem um label que define seu consumer group, ou seja, os consumidores sempre são organizados em conjuntos de um ou mais consumidores.
- Cada registro publicado em uma partição do tópico é enviado para um consumidor de cada grupo, ou seja, somente um consumidor do grupo irá ler esse registro.
- A maneira de consumo implementada no protocolo do Kafka faz com as partições do tópico sejam divididas entre os consumers de mandeira que cada consumer seja responsável pela mesma quantidade de partições para que não sejam sobrecarregados.
  - Se um novo consumer é adicionado então ele toma uma ou mais partições de outro consumer do mesmo grupo.
  - Se um consumer é removido então seus tópicos serão distribuídos entre os outros consumers do mesmo grupo [Exemplo](https://kafka.apache.org/11/images/consumer-groups.png).
  - Caso este esteja num grupo de consumers, caso a partição que ele retome já tenha sido iniciada por outro consumidor anteriomente então ele continuará a processar a partir do último offset commitado (caso a propriedade `auto.offset.reset` seja lastest). 
- Quando o consumer faz um subscribe em um ou mais tópicos, caso seja adicionado uma nova partição algum desses tópicos esse consumir fará a verificação automaticamente e então passará a ler dessa nova partição também.
- Quando o consumer faz um assign em uma ou mais partições, caso seja adicionado uma nova partição em algum algum desses tópicos ele não fará nada visto que está interessado apenas nas partições definidas inicialmente.
- `poll()`
  - Recebe o timeout na chamada
  - É single-threaded
  - Retorna uma coleção de records



## Principais API
- Producer API: permite um app publicar mensagens em um ou mais tópicos.
- Consumer API: permite um app inscrever-se em um ou mais tópicos para processar suas mensagens.
- Stream API: permite um app atuar como transformador entre tópicos, consumindo registros de um ou mais tópicos (input) e publicar seus resultados em um ou mais tópicos (output).
- Connector API: permite construir e rodar reusáveis producers e consumers que conectam os tópicos do Kafka em app ou bancos de dados existentes.



## Propriedades do Broker
* `log.message.timestamp.type`: Define qual o timestamp será usado no registro de log.
  * CreateTime: o timestamp setado pelo produtor que será utilizado
  * LogAppendTime: o timestamp será setado pelo broker no momento que o registro é appendado no log.



### Propriedades do Client Producer

* `max.in.flight.request.per.connection`: define a quantidade máxima de requisições em uma conexão.
* `retry.backoff.ms`: intervalo entre as tentativas de salvar o registro.
* `batch.size`: tamanho máximo do batch antes que seja commitado.
* `batch.memory`: tamanho máximo do batch em bytes antes que seja commitado.
* `linger.ms`: tempo máximo até que haja um commit batch (estando cheio ou não).
* `acks`: configura o nível de acknowledge.

### Propriedades do Client Consumer

* `group.id`: define um ID para o grupo caso queira que o consumer faça parte de um grupo de consumidores específico.
* `heartbeat.interval.ms`: Intervalo dos sinais de health do consumer, default é 3000.
* `session.timeout.ms`: timeout dos sinais de health, default é 30000.
* `enable.auto.commit`: Define se o autocommit dos registros processados mas não commitados (registros entre a posicão atual do offset e a última posição commitada do offset):
  * true: default
  * false: o consumidor é responsável por efetuar o commit usando um dos métodos a seguir:
    * `commitSync`: o commit é efetuado de forma síncrona, o que nos dá grande nível de consistência, o consumidor é bloqueado até que receba uma resposta do broker, se ocorrer erro é feito novas tentativas.
      * `commitAsync`: o commit é feito de forma assíncrona, enquanto o consumidor pode estar processando outros registros, o que diminui o nível de consistência. Porém é possível passar um callback que será chamado quando for completado (é recomendado sempre usar esta opção). Não tem retry automático. 
* `auto.commit.interval`: Interval para commitar os registros que foram processados. Valor padrão é 5s.
* `auto.offset.reset`: Define o comportamento quando um consumer voltar a consumir uma nova partição.
  * lastest: inicia do último offset commitado, é o comportamento padrão.
  * earliest: 
  * none:
* `retry.backoff.ms`: intervalo entre as tentativas de commitar o offset dos registros processados.
* `fetch.min.bytes`: define a qtde mínima de bytes para que haja um processamento, para não desperdiçar ciclos.
* `max.fetch.wait.ms`: tempo máximo de espera do fetch.
* `max.partition.fetch.bytes`: tamanho máximo de bytes que pode ser carregado por partição durante um ciclo.
* `max.poll.records`: tamanho máximo de registros para processar.



## Comandos

### Zookeeper
* Iniciar: `./bin/zookeeper-server-start.sh config/zookeeper.properties`
* Testar: `telnet localhost 2181` e digitar `stat`

### Broker Kafka
* Iniciar: `./bin/kafka-server-start.sh config/server.properties`
* Tópicos:
  * Listar: `./bin/kafka-topics.sh --list --zookeeper localhost:2181`
  * Criar: `./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic my-topic`
  * Alterar: `./bin/kafka-topics.sh --alter --zookeeper localhost:2181 --replication-factor 2 --partitions 2 --topic my-topic`
  * Detalhar: `./bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-topic`
  * Pasta física na máquina (caso não tenha mudado o caminho padrão): `/tmp/kafka-logs/my-topic-0`
* Testes:
  * Stress: `./bin/kafka-producer-perf-test.sh --topic my-topic --num-records 1000 --record-size 100 --throughput 100 --producer-props bootstrap.servers=localhost:9092 key.serializer=... value.serializer=...`
    * num-records: qtde de registros
    * record-size: tamanho de cada registro em bytes
    * throughput: performance desejada (mensagens/segundo)
    * producer-props: propriedades do produtor (iguais do client java)

### Produtor
Kafka possui um command-line producer, cada linha é uma mensagem que será enviada.
* Run: `./kafka-console-producer.sh --broker-list localhost:9092 --topic my-topic`

### Consumidor
Kafka também possui um command-line consumer que irá printar cada mensagem lida.
* Run: `./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning`


## Resources
* Kafka Registry: conjunto de serializadores.
* Kafka Connector: conjunto de libs para conectar em outros sistemas e bancos.