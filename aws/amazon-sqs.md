# Amazon SQS


### Características
* Redundância das mensagens por padrão
* Delay da entrega da mensagem a nível de fila e mensagem (0s à 15min)
* Timeout de processamento (visibility timeout) de 30s (pode configurar entre 0s e 12h ou usar headbeat para estender o tempo)
* Mensagem pode ter tamanho máximo de 256 KB (pode enviar mais usando S3)
* Retenção de mensagem padrão de 4 dias (pode configurar entre 1min e 14 dias)
* Mensagem pode ter até 10 atributos (contam no tamanho máximo da mensagem)
* Suporta DLQ

### Tipos
#### Standard
* Throughput ilimitado
* No mínimo uma entrega (pode haver entragas duplicadas)
* Pode haver desordem das mensagens
* Limite de mensagens inflight: 120 K (pode solicitar aumento pelo suporte)

#### FIFO
* Nome da fila precisa ter o sufixo `.fifo`
* Throughput alto mas limitado (3000/s com batch; 300/s por ação; pode solicitar aumento pelo suporte)
* Exatamente uma entrega
* Garantia da ordem das mensagens que tenham o mesmo group id
* Limite de mensagens inflight: 20 K
* Não suporta delay de entrega por mensagem apenas por fila (preservar a ordem)
* Alguns termos:
  * Message Deduplication ID: após uma mensagem ser escrita, subsequentes mensagem com o mesmo ID serão aceitas mas não entregues dentro do intervalo (mínimo de 5m) de deduplicação (mesmo após o consumo da mensagem original). Pode ser gerada a partir do hash do conteúdo da mensagem ou atribuindo um identificador
  * Message Group ID: tag para agrupar mensagens para que sejam processadas uma por uma (sem paralelismo) na ordem exata de recebimento. Outras mensagems com outra tag serão processadas em paralelo
  * Receive Request Attempt ID: token utilizado para deduplicar as chamadas de ReceiveMessage
  * Sequence Number: número não-sequencial que o SQS atributi nas mensagens

### DLQ
* Precisa estar na mesma região e conta
* Precisa ter o mesmo tipo da fila de origem
* O TTL da fila de origem conta no TTL da DLQ
* Pode configurar alarme para mensagens que chegam na fila

### Uso
A mensagem tem uma janela de processamento (visibility timeout), caso não seja processada a tempo será entregue para outro consumer.
Esse visibility timeout tem valor padrão de 30s, pode ser configurado de 0 a 12 horas.

Passos:
* Receber a mensagem
* Consumir
* Deletar a mensagem (ACK deles)
