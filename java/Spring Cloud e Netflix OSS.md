> Logs
- ELK ou GrayLog
- Async em outra maquina para não derrubar o serviço
- Logar mensagem e stacktrace
- Interessante ter canais para Tracing de calls e outro de erros

## Eureka
- Service Registry-Discovery
- Visualização das instâncias
- Load balancing e failover
- Add/Rem de nodes on the fly
- Visualização Health Check

> Projeto
- Deps: eureka-server
- Código:
  - @EnableEurekaServer (habilitar o Eureka Server)
  - Usar @EnableEurekaClient para registrar os clientes (setar key eureka.server.host no application.properties)
- application.properties
  - server.port=8761 # servidor Eureka
  - eureka.client.register-with-eureka=false # não registra nele mesmo


## Zuul
- API Gateway/Proxy
- Permite configurar autenticação
- Provê Load balancing (rouding-robin)
- Roteamento dinâmico (também provê manual)
- Logs de acesso
- Integrado com Eureka

> Projeto
- Deps: eureka-client, zuul
- Código:
  - Annotation @EnableZuulProxy
  - Annotation @EnableEurekaClient (para se registrar no Eureka Server)
  - Podemos criar um filtro estendendo ZuulFilter
- application.properties:
  - spring.application.name=zuul
  - eureka.server.host=127.0.0.1 # já é default
  
  
## Feign
- Facilitar a integração entre services
- Baseado em metadados (annotation)
- Utiliza o Ribbon
- Integração automática Eureka
  - Pesquisa no Eureka para carregar informações sobre URLs e qtde instâncias
- Basta criar uma interface do resource

> Projeto
- Deps: eureka-client, feign
- Código:
  - @EnableEurekaClient
  - @EnableFeignClients
  - Interface:
    - anotar interface com @FeignClient("NomeServico", fallback = ClassQueImplementaInterface.class)
    - anotar método com @RequestMapping(value = "/path", method = RequestMethod.GET)
- application.properties:
  - eureka.server.host=127.0.0.1

## Config server
- Centralizar configurações
- Podemos ter um ambiente que use um branch do Git com arquivos de configuração

## Sleuth
- Tracing da comunicação entre os serviços (app -> zuul -> node)
- Alimentar Elastic, Kibana

## Hystrix
- Framework de Fallback e failover



## Glossário
IDP - Servidor de Segurança Centralizado na arquitetura microserviços
