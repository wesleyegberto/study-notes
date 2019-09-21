# Microserviços

### Pontos de Balas
* Promete mais agilidade, velocidade nas entregas e maior escala quando comparado com monolítos;
* Prove uma maneira de desenvolver rapidamente e de forma ágil aplicações que pode resultar em um menor custo;
* Arquiteturas modernas são esperadas para maximizar a habilidade de substituir suas partes e minimizar o custo dessas substituições;
* Microserviços são alinhados em sentido de capacidades de negócios;
* Cada microserviço tem sua própria camada de apresentação, negócio e banco de dados;


## Definições

Microserviços são um estilo arquitetural ou método para desenvolver sistemas como um conjunto de capacidades de negócios que são autonomos, autocontidos e com baixo acoplamento.


## Princípios

### Responsabilidade Única no Serviço
O microserviço deve ter apenas uma responsabilidade, seguindo SOLID.

### Autônomo
São autocontidos, publicável independentemente e autônomos que têm completa responsabilidade de capacidade de negócio e sua execução.

Uma das maiores diferenças entre microserviços e SOA é o nível de autonomia. Enquanto que a implementação de SOA provê abtração a nível de serviço, microserviço vai além e abstrai a realização e execução do ambiente.

## Características

##### Serviços são cidadãos de primeira classe
Microserviços expõe endpoints de serviços como APIs e abstrai todos os detalhes da execução.

##### Contrato de serviço
Microserviços são descritos através de um conjunto bem definido de contratos de serviços.

No caso de comunicação utilizando REST com JSON é possível utilizar algumas técnicas para especificar esses contratos: JSON Schema, WADL, Swagger ou RAML.

##### Baixo acoplamento
Microserviços são independente e de baixo acoplamento. Na maioria das vezes aceitam um evento de entrada e respondem com outro evento.

Mensageria, HTTP e REST são normalmente utilizados para interação entre os microserviços, onde mensageria provê maior nível de desacoplamento.

##### Abstração do serviço
Além da abstração da execução do serviço, também provê completa abstração dos detalhes de suas dependências e ambiente de execução.

##### Sem estado
Microserviços bem desenhados não possuem ou compartilham nenhum estado. Caso precisem manter estado é utilizado um banco de dados.

##### Autodescobertos
Microseviços são autodescobertos. Quando iniciam e estão pronto para atender as solicitações ele notifica sua existência e quando morrem eles automaticamente notificam sua retirada.

##### Interoperabilidade
Com o uso de protocolos padrões e troca de mensagens, microserviços se tornam interoperáveis com a maioria das tecnologias. Em alguns casos essa interoperabilidade pode ser limitada caso precisem de maior otimização ao utilizar protocolos e mensagens mais específicas.

##### Composição de serviços
Um serviço pode ser compostos de vários microserviços. Esta composição é feita através de orquestração ou coreografia de serviços.

