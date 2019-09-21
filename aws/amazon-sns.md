# Amazon SNS

Pub/Sub que coordena e gerencia a entrega de mensagens para os clients/endpoints inscritos.

Ver: https://docs.aws.amazon.com/sns/latest/dg/images/sns-how-works.png

### Características
* Persistência de todas as mensagens recebidas
* Mensagem pode ter até 10 atributos (contam no tamanho máximo da mensagem)
* Suporta filtragem das mensagens que o consumer deseja receber utilizando os atributos
* Mensagem pode ter tamanho máximo de 256 KB (pode enviar mais usando nova versão)
* Throughput ilimitado
* No mínimo uma entrega (pode haver entragas duplicadas)
* Pode haver desordem das mensagens
* Pode ter URL, e-mail, push notification e SMS como inscrito
* Existe políticas de retry caso não consiga entregar a mensagem para um inscrito, a política vária conforme a forma de inscrição, após todas as tentativas a mensagem é descartada
* Não há garantia de entregas das mensagens para os inscritos - caso seja necessário que todas as mensagens sejam entregues é recomendado usar SQS