## Logs
- ELK ou GrayLog;
- Async em outra maquina para não derrubar o serviço;
- Logar mensagem e stacktrace;
- Interessante ter canais para Tracing de calls e outro de erros;


## Zuul

É um  API Gateway/Proxy.
Características:
- Permite configurar autenticação;
- Ignorar ou adicionar headers e cookies;
- Provê Load balancing (rouding-robin) através do ribbon;
- Roteamento dinâmico (também podemos fazer manualmente através do .properties);
- Logs de acesso;
- Monitoramento;
- Integrado com Eureka;
- Todos requests são executados dentro de um comando hystrix;
- Permite testes:
  - Stress testing
  - Canary testing
- Provê load shedding;
- Versões disponíveis:
  - 1: blocking
  - 2: non-blocking

Fluxo:
  - Request
  - Pre-Filters (e custom filters)
  - Routing Filters (envia e recebe das APIs)
  - Post-filters
  - Errors Filters (paralelo ao fluxo todo)

#### Projeto
- Deps: eureka-client, zuul
- Código:
  - Annotation @EnableZuulProxy
  - Annotation @EnableEurekaClient (para se registrar no Eureka Server)
  - Podemos criar um filtro estendendo ZuulFilter
- application.properties:
  - spring.application.name=zuul
  - eureka.server.host=127.0.0.1 # já é default

#### Links
- [Github](https://github.com/Netflix/zuul)
- [Zuul 1 - Post](https://medium.com/netflix-techblog/announcing-zuul-edge-service-in-the-cloud-ab3af5be08ee)
- [Zuul 2 - Post](https://medium.com/netflix-techblog/zuul-2-the-netflix-journey-to-asynchronous-non-blocking-systems-45947377fb5c)
- [Heroku Post](https://blog.heroku.com/using_netflix_zuul_to_proxy_your_microservices)
- [Baeldung](http://www.baeldung.com/zuul-load-balancing)
- [Presentation](https://www.youtube.com/watch?v=2oXqbLhMS_A&feature=youtu.be)


## Eureka
- Service Registry-Discovery
- Visualização das instâncias
- Load balancing e failover
- Add/Rem de nodes on the fly
- Visualização Health Check

#### Projeto
- Deps: eureka-server
- Código:
  - @EnableEurekaServer (habilitar o Eureka Server)
  - Usar @EnableEurekaClient para registrar os clientes (setar key eureka.server.host no application.properties)
- application.properties
  - server.port=8761 # servidor Eureka
  - eureka.client.register-with-eureka=false # não registra nele mesmo

#### Links
- [Netflix Blog](https://medium.com/netflix-techblog/netflix-shares-cloud-load-balancing-and-failover-tool-eureka-c10647ef95e5)
- [Heroku Post](https://blog.heroku.com/managing_your_microservices_on_heroku_with_netflix_s_eureka)

## Ribbon
Dynamic routing e Load Balancer pode ser usado para fazer lookup dos serviços em runtime.
Utiliza inforamções disponíveis  no Euraka para localizar instâncias apropriadas dos serviços.
Se mais de uma instância for encontrada, Ribbon aplicará load balancing para espalhar os requests através das instâncias disponíveis.
Não roda como um serviço separado mas sim como um componente embutido em cada consumidor de serviço.


## Feign
- Facilitar a integração entre services
- Baseado em metadados (annotation)
- Utiliza o Ribbon
- Integração automática Eureka
  - Pesquisa no Eureka para carregar informações sobre URLs e qtde instâncias
- Basta criar uma interface do resource

#### Projeto
- Deps: eureka-client, feign
- Código:
  - @EnableEurekaClient
  - @EnableFeignClients
  - Interface:
    - anotar interface com @FeignClient("NomeServico", fallback = ClassQueImplementaInterface.class)
    - anotar método com @RequestMapping(value = "/path", method = RequestMethod.GET)
- application.properties:
  - eureka.server.host=127.0.0.1


## Hystrix

Provê capacidade de circuit breaker para um consumidor de serviço:
- Resiliência
- Framework de Fallback e failover

O circuito fecha quando o serviço não response (por causa de timeout ou erro de comunicação), então Hystrix redireciona a chamada para o fallback.
Quando ocorre flhas repetidas o Hytrix abre o circuito e falha rápido - não chama o serviço e executa direto o fallback.

#### Projeto
- Deps:
  - spring-boot-starter-actuator
  - spring-cloud-starter-netflix-hystrix
- Deps para expor métrics p/ MQ:
  - spring-cloud-starter-stream-rabbit
  - spring-cloud-netflix-hystrix-amqp
- Deps para dashboard:
  - spring-cloud-starter-netflix-hystrix-dashboard
- Código:
  - @EnableHystrix
  - @EnableCircuitBreaker
  - @EnableHystrixDashboard // Habilitar dashboard
- application.properties:
  - feign.hystrix.enabled=true # Habilitar Hystrix no Feign client
  - management.endpoints.web.base-path=/actuator # default - apenas para lembrar
  - management.endpoints.web.exposure.include=hystrix.stream # habilitar endpoint do Hystrix

#### Links
- [Post](https://medium.com/netflix-techblog/introducing-hystrix-for-resilience-engineering-13531c1ab362)
- [Post Dashboard](https://medium.com/netflix-techblog/hystrix-dashboard-turbine-stream-aggregator-60985a2e51df)


## Turbine
- Agregador em real-time dos estados dos hystrix command das aplicações;
- Quando se tem várias aplicações expondo endpoint com stream do Hystrix, fica inviável utilizar o dashboard, neste caso é necessário utilizar o Turbine Stream para agregar as métricas de todas as aplicações em um MQ ou Kafka;
- Os dados estão em formato bruto, para melhor visualização é necessário a utilização do Dashboard do Hystrix;
- Para consumir basta abrir o dashboard do Hystrix e colocar a URL que tem o seguinte formato: `http://localhost:8989/turbine.stream`;

#### Projeto Server
- Deps:
  - spring-boot-starter-actuator
  - spring-cloud-starter-netflix-hystrix
  - spring-cloud-starter-netflix-turbine-stream
- Deps para MQ:
  - spring-cloud-starter-stream-rabbit
- Código:
  - @EnableTurbineStream
- application.properties:
  - configurar rabbitmq


## Config server
- Centralizar configurações
- Podemos ter um ambiente que use um branch do Git com arquivos de configuração


## Sleuth
- Tracing da comunicação entre os serviços (app -> zuul -> node);
- Alimentar Elastic, Kibana;


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
