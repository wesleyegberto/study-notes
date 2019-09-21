# RabbitMQ


## Operação

### Possíveis limitações:
* RAM (hardware)
* Disk (hardware)
* número max de files processo pode manipular (arquivos, sockets)

### Quando limitado:
* Bloqueia conexões dos producers (se um consumer compartilha a mesma conexão logo também poderá ser bloqueado)
* Conexões dos consumers continua liberado para permitir o draining das mensagens
* Monitoramento de heartbeat das conexões é suspendido

### Memory Alarm
* É realizado gargalo dos producers quando consumers não acompanham;
* Por padrão, não aceita nenhuma mensagem quando detecta que que está usando mais de 40% da memória disponível (reportado pelo OS).
    * Este valor padrão é seguro, pois SO e filesystem pode usar memória para acelerar os processos em todo sistema, se não deixar espaço pode ocorrer problemas de performance devido ao swapping ou morte do processo do RabbitMQ.
    * Recomendações na alteração:
        * Node host precisa ter no sempre disponível no mínimo 128MB de RAM;
        * Valor para vm_memory_high_watermark entre 0.4 e 0.66 (caso seja maior pode ter problemas de performance devido a paging).
* Por padrão, antes do broker começar a bloquear os producers ele tentará liberar memória através do paging das filas
    * Valor padrão é 50% do valor do VM Memory High Watermark.
    * Para alterar:
        * vm_memory_high_watermark_paging_ratio = 0.75
* Alterar configuração:
    * {vm_memory_high_watermark, 0.4}
    * vm_memory_high_watermark.relative = 0.4
    * vm_memory_high_watermark.absolute = 2GB
* Pode ser alterado enquanto o broker está rodando
    * set_vm_memory_high_watermark 0.4
* Desabilitar as publicações de forma global:
    * rabbitmqctl set_vm_memory_high_watermark 0

### Disk Alarm
* Evitar que o disco seja lotado o que pode causar a morte do processo;
* Consequências:
    * bloqueia os producers
    * evita que mensagens memory-based sejam paginadas no disco
* Valor padrão do disk_free_limit é 50MB (ok para dev e testes);
* Produção requerer um valor maior para mais segurança pois disco insuficiente pode causar  falha do Node o que pode causa perda de dados e falha de escrita;
* O intervalo padrão de checagem é de 10s, mas conforme o disk alarm se aproxima do limite a frequência de checagem pode aumentar (até 10/s);
* Se o valor for muito baixo e as mensagens são paginadas rapidamente é possível lotar o disco e crashar o broker entre os disks checks (10 segundos);
* Em cluster, o disk alarm é cluster-wise, se um node disparar então todos os nodes irão recusar mensagens;
* Recomendações:
    * {disk_free_limit, {mem_relative, 1.0}}
        * Valor mínimo recomendado que será igual à RAM disponível (se tem 2GB de RAM ele requer 2GB livres no disco)
    * disk_free_limit, {mem_relative, 1.5}}
        * Valor mais seguro para produção (se tem 2GB de RAM ele requer 3GB livres no disco)
        * Nos caso de shutdown, o Rabbit irá escrever todas mensagens em memória pro disco (nesse caso, 2GB poderá ser escrito tranquilamente nos 3GB livres)
    * disk_free_limit, {mem_relative, 2}}
        * Valor mais conservador, garante que o RabbitMQ terá todo disco livre necessário o tempo todo.
* Pode alterar enquanto está rodando:
    * rabbitmqctl set_disk_free_limit disk_limit
    * rabbitmqctl set_disk_free_limit mem_relative fraction


### Files
* SO limita os files (arquivos físicos, sockets de conexões) que um processo pode manipular;
* Garantir que tenha o limite no mínimo 50K (até para dev);
* Regra para calcular:
    * 95th percentile do número de conexões paralelas * qtde total de filas


### Conexões
* Heartbeats
    * Valor muito baixo pode causa falso positivo (falha no ping devido rede congestionada);
* Connection Churn
    * Alto rate de conexões que são abertas e fechadas logo em seguida (para efetuar apenas uma operação);
    * Desperdício de recursos e ineficiência;
    * Caso esse seja o cenário exigido é preciso tunar para liberar conexões TCP mais rápido que o kernel o faz (caso contrário pode chegar no limite de files aberto ou memória);
    * 



Links
* https://www.cloudamqp.com/blog/2017-12-29-part1-rabbitmq-best-practice.html
* https://www.rabbitmq.com/production-checklist.html#resource-limits-ram
* https://www.rabbitmq.com/memory.html
* https://www.rabbitmq.com/production-checklist.html#resource-limits-ram
* https://www.rabbitmq.com/networking.html#dealing-with-high-connection-churn
* https://www.rabbitmq.com/ha.html
* https://www.rabbitmq.com/distributed.html
