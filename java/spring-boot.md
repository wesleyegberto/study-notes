# Spring


### Spring Boot
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

* Os endpoints de gerencimento a partir da versão 2.0 estão desativados, para ativá-los basta adicionar no `.properties`:
`management.endpoints.web.exposure.include=*`
