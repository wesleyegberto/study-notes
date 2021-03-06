# Spring


## Spring Boot

* Utiliza conversão sobre configuração.
* Para cada dependência `spring-boot-starter-*` no POM.xml é executado uma classe `AutoConfiguration` padrão (ex.: `spring-boot-starter-jpa` executa JpaRepositoriesAutoConfiguration`).
* Ao executar o JAR com o parâmetro `--debug` é apresentado um relatório de todos os AutoConfiguration que foram executados:
`java -jar target/api.jar --debug`.
* É possível remover a autoconfiguração de uma dependência com:
`@EnableAutoConfiguration(exclude = { JpaRepositoriesAutoConfiguration.class })`.
* Para mudar o diretório do arquivo `.properties`:
`java -jar target/api.jar --spring.config.name=myconfig.properties`
Ou usando o arquivo `application.properties` que aponta para outro:
```
spring.config.name=myconfig.properties
spring.config.location=/path/to/the/file
```

* Os endpoints de gerenciamento a partir da versão 2.0 estão desativados, para ativá-los basta adicionar no `.properties`:
`management.endpoints.web.exposure.include=*`

### Web Async e Non-Blocking

#### Fluxo da Requisição
* Poucas threads (1-4 dependendo do # de cores) fazendo polling no seletor que olha os canais nas conexões em busca de atividade de IO;
* Quando encontra atividade IO, é invocado o método `handle` da conexão e uma thread do pool é alocada para processar;
* Thread tenta ler e converter o conteúdo da conexão para um HTTP Connection. Se os headers da conexão estão completos, a thread segue chamando o handler do request (que eventualmente chega no Servlet) sem esperar o resultado;
* No Servlet, mesmo API de IO sendo blocking, através de callbacks, as operações do I/O no HttpInputStream e HttpOutputStream são async e non-blocking.

## Spring Cloud

### Gateway

Utiliza a stack reactor.
Event loop que serve as requisições.

#### Fluxo
- Handler Mapping
	- utiliza predicados a partir de paths, headers, cookies, etc
- Web Handler
  - Pré-filters
    - Headers, path, rate limiting, cookies, hystrix, etc
  - Global filters
    - Netty router, web socket, LB, métricas
  - Post-filters
    - Set status, headers, cookies, etc

#### Formas de Deploy

* Embedded
  * própria aplicação
  * add dependência no pom.xml
* Facade
  * aplicação separada

#### Configuração

##### Rotas
Podemos definir rotas através de beans no java ou no arquivo properties.

```java
@Bean
public RouteLocator routeLocator(RouteLocatorBuilder builder) {
	return builder.routes()
		.route(p -> p.path("/external-path").uri("http://my-service:8080/internal-path"))
		.route(p -> p.path("/lb").uri("lb://eureka-registered-service/path"))
		.build();
}
```

Podemos ativar a configuração das rotas usando os serviços registrados no Eureka.
```java
@Bean
DiscoveryClientRouteDefinitionLocator discoveryRoutes(DiscoveryClient dc) {
	return new DiscoveryClientRouteDefinitionLocator(dc);
}
```