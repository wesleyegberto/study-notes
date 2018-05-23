# Kafka

Plataforma distribuída de streaming.
* Pub/sub em uma stream de registros;
* Armazena os registros em uma maneira tolerante à falhas;
* Processa os registros conforme eles ocorrente (fila).


#### Caracteristicas:
* Roda como um cluster em um ou mais servidores.
* Armazena os dados em categorias chamadas Tópicos.
* Cada registro é uma tupla composta por Chave, Valor e Timestamp.
* Utiliza protocolo binário sobre o protocolo TCP que é versionado e mantém retrocompatibilidade.
* Provê georeplicação do cluster com Kafka MirrorMaker.
* Provê suporte para multi-tenancy permitindo configurar quais tópicos podem produzir ou consumir dados e quotas de recursos das requisições.
* Pode atuar como um MQ, Storage e Stream Processing.


### Garantias
* Mensagens enviadas de um mesmo producer para um tópico serão persistidas na mesma ordem de envio.
* Um consumidor enxerga os registros na mesma ordem que são persistidos.
* Para um tópico com fator de replicação N, será tolerado até N-1 servidores falhandos sem que haja perda de registros commitados.


### API principais:
* Producer API: permite um app publicar mensagens em um ou mais tópicos.
* Consumer API: permite um app inscrever-se em um ou mais tópicos para processar suas mensagens.
* Stream API: permite um app atuar como transformador entre tópicos, consumindo registros de um ou mais tópicos (input) e publicar seus resultados em um ou mais tópicos (output).
* Connector API: permite construir e rodar reusáveis producers e consumers que conectam os tópicos do Kafka em app ou bancos de dados existentes.



## Topicos
[Imagem detalhada](https://kafka.apache.org/11/images/log_anatomy.png)
* Uma categoria onde os registros são publicados.
* Todos os tópicos são multi-subscriber, podendo ter zero, um ou mais subscribers.
* Para cada tópico, o cluster Kafka mantém um log particionado. Cada partição é um sequência ordenada (pelo timestamp) e imutável de registros que é continuadamente escrita (estrutura de log commit). Cada registro nas partições recebe um ID sequencial chamado *offset* que é unico dentro de uma partição.
* O Kafka persiste todos os registros (consumidos ou não) durante um período de tempo configurado (período de retenção). Após esse tempo o registro é removido para liberar espaço. Isso não afeta a performance do Kafka pois esta é efetivamente constante dependendo apenas do tamanho do registro.
* O consumer que é responsável por controlar quais registros já leu. Isso é feito usando o *offset* do registro. Assim, normalmente, um consumer avança seu offset conforme processa os registros. Mas também é possível voltar e avançar a vontade ao alterar esse offset. Isso torna o consumo de registros bem leve, sem muito impacto no cluster Kafka.

### Partições
* As partições de um tópico permite este escalar seu tamanho (quantidade de registros) além do permitido no node do cluster (visto que um partição pode estar em um node e outra em outro) e também atua como uma unidade de paralelismo (visto que controla o commit dos registros por partição, logo podemos ter vários locks em um mesmo tópico permitindo assim escrita paralela).
* Kafka somente controla os registros por partição (offset único dentro da partição) e não entre as partições de um tópico. Caso a aplicação precise de controle de todos os registros por tópico (offset único dentro de um tópico) então será necessário criar um tópico que tenha apenas uma partição, mas isso significa também que terá apenas um consumidor por grupo.
* Cada partição pode ser armazenada em diferentes nodes do cluster e cada partição também pode ser replicadas entre um número configurado de nodes para tolerância à falhas.
* Cada partição tem um leader e zero ou mais followers. O leader é responsável por lidar com todas as requições de leituras e escritas na partição enquanto os followers apenas passivamente replicam os registros.
* Se um lider falha, então um de seus followers será elegido a lider. Os followers que são elegíveis são do ISR (in-sync replicas), grupo de followers que estão em sincronia com o lider (receberam o último commit).
* Quando o ACK do registro está a nível de replica entre os followers, essa mensagem só será comitada quando todos os followers receberem, se o lider morrer nesse meio tempo o follower elegido a lider terá apenas as mensagens commitadas nestes (replicadas), então pode ocorrer perda das mensagens não commitadas no lider.
* Cada node do cluster atua como lider em alguns tópicos e follower em outros, logo, a carga de requisições é bem balanceada no cluster.


### Comandos
* Listar: `./kafka-topics.sh --list --zookeeper localhost:2181`
* Criar: `./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic my-topic`
* Detalhar os topicos: `./kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-topic`




## Producers
* Todos os nodes do cluster fornecem metadata para informar qual node está vivo e qual node contém os liders das partições de um tópico. Com isso após a leitura desse metadata, o producer pode enviar mensagens diretamente para o lider.
* O producer é responsável por escolher qual registro será enviado para um determinada partição. Essa escolha pode ser feita via round-robin para balancer o load ou pode ser feita de fora seletiva.
* Também é possível enviar mensagens em modo batch, pode ser configurado para enviar quando atingir N mensagens e/ou após um determinado tempo.

### Comandos
Kafka possui um command-line producer, cada linha é uma mensagem que será enviada.
* Comando: `./kafka-console-producer.sh --broker-list localhost:9092 --topic my-topic`




## Consumers
* Cada consumidor se tem um label que define seu consumer group, ou seja, os consumidores sempre são organizados em conjuntos de um ou mais consumidores.
* Cada registro publicado em uma partição do tópico é enviado para um consumidor de cada grupo, ou seja, somente um consumidor do grupo irá ler esse registro.
* A maneira de consumo implementada no protocolo do Kafka faz com as partições do tópico sejam divididas entre os consumers de mandeira que cada consumer seja responsável pela mesma quantidade de partições para que não sejam sobrecarregados. Se um novo consumer é adicionado então ele toma uma ou mais partições de outro consumer do mesmo grupo. Se um consumer é removido então seus tópicos serão distribuídos entre os outros consumers do mesmo grupo [Exemplo](https://kafka.apache.org/11/images/consumer-groups.png).



### Comandos
Kafka também possui um command-line consumer que irá printar cada mensagem lida.
* Comando: `./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning`
