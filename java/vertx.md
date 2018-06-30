# Vert.x


## Core
* HTTP 1 e 2
* UDP
* EventBus
* Shared data
* TCP


## Modelo
Actor-Model
Event loop por Reactor

### Verticles
Building block que executam as operações.
Executa em um event loop.
* Escalável
* Non-blocking
* Simples


### Event Bus
Pode ser distribuído (as instâncias do verticles se conectam a um Hazelcast).
Os verticles se comunicam usando o event bus.
Modelos que podem se conectar:
* Publisher/Subscribe
* Point-2-Point

### Shared Data
Mapa de valores local ou distribuida usando Hazelcast (padrão).
