> Logs
- ELK ou GrayLog
- Async em outra maquina para não derrubar o serviço
- Logar mensagem e stacktrace
- Interessante ter canais para Tracing de calls e outro de erros


## Zuul
- (Zuul 1 - Post)[https://medium.com/netflix-techblog/announcing-zuul-edge-service-in-the-cloud-ab3af5be08ee]
- (Zuul 2 - Post)[https://medium.com/netflix-techblog/zuul-2-the-netflix-journey-to-asynchronous-non-blocking-systems-45947377fb5c]
- API Gateway/Proxy
- Permite configurar autenticação
- Provê Load balancing (rouding-robin)
- Roteamento dinâmico (também provê manual)
- Logs de acesso
- Integrado com Eureka
- Fluxo:
  - Request
  - Pre-Filters (e custom filters)
  - Routing Filters (envia e recebe das APIs)
  - Post-filters
  - Errors Filters (paralelo ao fluxo todo)
- Versões
  - 1: blocking
  - 2: non-blocking

> Projeto
- Deps: eureka-client, zuul
- Código:
  - Annotation @EnableZuulProxy
  - Annotation @EnableEurekaClient (para se registrar no Eureka Server)
  - Podemos criar um filtro estendendo ZuulFilter
- application.properties:
  - spring.application.name=zuul
  - eureka.server.host=127.0.0.1 # já é default


## Eureka
- [Post](https://medium.com/netflix-techblog/netflix-shares-cloud-load-balancing-and-failover-tool-eureka-c10647ef95e5)
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
- [Post](https://medium.com/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362)
- [Post Dashboard](https://medium.com/netflix-techblog/hystrix-dashboard-turbine-stream-aggregator-60985a2e51df)
- Resiliência
- Framework de Fallback e failover


## Turbine
- Visualização de latência


## Vector
- Monitoramento de hosts


## Ice
- Visualização de custos


## Dynomite e Dyno
- Clusters de cache


## Prana
- Integração de coisas fora da JVM que precisem da Stack


## Aegisthus
- Big data


## Servo
- Métricas


## Archaius
- Configuração dinâmica


## Karyon
- Server para APIs
- Versões:
  - 1: blocking
  - 2: non-blocking (RxJava com Netty e outros)
- Gerenciamento do lifecycle com Governator
- Gerenciamento de properties com Archaius
- Service discovery com Eurkea
- Interface de Admin
- Healthcheck (e também pode disponibilizar para o Eureka)

## Glossário
IDP - Servidor de Segurança Centralizado na arquitetura microserviços
