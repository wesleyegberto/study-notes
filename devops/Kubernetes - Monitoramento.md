# Kubernetes Monitoring

## Tools

### cAdvisor
- Opera a nivel de node
- autodescoberta dos containers rodando
- coleta informações de CPU, memória, filesystem e rede

Cons:
- recursos básicos
- visão de uso do container e não como a aplicação está usando


### Prometheus
Toolkit para monitoramento e alerta open source.
Os componentes do Kubernetes por padrão já são instrumentados com Prometheus.
Existe o Prometheus Operator que já integra facilmente ao Kubernetes e consome suas configurações nativas.
- Tem suporte para instrumentação em várias linguages
- Tem exporters para conectar algumas aplicações

#### Prometheus Operator
Um operador é uma aplicação controller específica que estende a API do Kubernetes para criar, configurar e gerenciar instâncias de aplicações stateful complexas no lugar do usuário.

É baseado nos conceitos básicos de resource e controller do kubernetes mas pode incluir conhecimento específico da aplicação para automatizar algumas tarefas.

Com aplicações stateful geralmente precisa de conhecimentos específicos para poder gerenciar corretamente (ex.: um deployment do Elasticsearch para rodar em cluster é preciso fazer configurações de cada node), a função desso operator é codificar esses conhecimentos e configurações especificas de modo que fique fácil gerenciá-la (criar, atualizar e configurar).

O deployment do prometheus operator cria novos resources customizados no Kubernetes que definem o estado desejado do prometheus, o servicemonitor que define quais aplicações serão lidas (scraped).



## Links
* https://coreos.com/operators/prometheus/docs/latest/user-guides/getting-started.html
