# API Gateway

## Características

Responsabilidades:

* Transformação de protocolo e payload
* Rate limiting
* Load balancing
* Tracing
* Service discovery
* Autenticação e autorização
* Métricas
* Tratar respostas específicas para determinados clientes (GraphQL pode ser utilizado)
* Canary release
* Desmembramento de monolíto

## Prós

* Facilita o consumo das APIs
  * URL e esquemas unificados
  * Único token para acesso
* Tratamento de erro comum (idealmente)
* Formato e protocolos de comunicação compartilhados
* Provê as mesmas métricas que o cliente enxerga