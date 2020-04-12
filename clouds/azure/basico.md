# Azure - Basico

Azure é a plataforma de cloud computing da Microsoft.

Ela permite utilizarmos recursos pagando mensalmente no modelo pay-as-you-go.
Para utilizarmos os recursos é preciso criar uma subscription. Dentro desta subscription podemos estruturar profiles e subscriptions de acordo com a estrutura organizacional necessária.

## Azure Services

Categorias comuns:

* Compute
* Networking
* Storage
* Mobile
* Databases
* Web
* IoT
* Big Data
* AI
* DevOps

### Compute

Um dos principais motivos de uma empresa mudar para cloud.

* Virtual Machines: Windows ou Linux VM hosted na Azure.
* VM Scale Sets: Scaling para VMs hosted na Azure.
* Kubernetes Services: gerencimaneot de cluster de VMs que rodam serviços containerizado.
* Service Fabric: Plataforma distribuída de sistemas, pode rodar na Azure ou on-premise.
* Batch: Serviço gerenciado para aplicações de computação paralela e alta performance.
* Container Instances: Rodar containers no Azure sem provisionamento de servers ou VMs.
* Functions: Event-driven serverless compute service.

### Networking

Conectando recursos e provendo acesso para aplicações, também permite conectar serviços fora da Azure.

* Virtal Network: Conecta VMs em conexões de entrada de VPN.
* Load Balancer: Balanceia conexões inbound e outbound entre as aplicações ou service endpoints.
* Application Gateway: Otimiza a entrega de app server farm e aumenta a segurança.
* VPN Gateway: Acessa Azure Virutal Networks através de VPN gateways de alta performance.
* DNS: Prove respostas DNS de alta velocidade e disponibilidade.
* Content Delivery Network: Entrega de conteúdo de alta largura de banda globalmente.
* DDoS Protection: Protege aplicações hosted na Azure.
* Traffic Manager: Distribui tráfego de rede através das regiões da Azure.
* Express Route: Conecta na Azure através de conexões dedicadas, seguras e alta largura de banda.
* Network Watcher: Monitora e diagnostica problemas de rede utilizando análises baseadas em cenários.
* Firewall: Implementa firewall de alta disponibilidade e segurança com escalabilidade ilimitada.
* Virtual WAN: Cria um WAN unificada, conectando sites locais e remotos.

### Storage

Azure fornece 4 tipos principais de storage.

* Blob: Armazenamento de objetos muito grade como arquivos de videos ou bitmaps.
* File: Compartilhamento de arquivos que podemos acessar e gerenciar como um servidor de arquivos.
* Queue: Data store para enfileiramento e entrega confiável de mensagens entre aplicações.
* Table: NoSQL store.

Características comuns entre os serviços:

* Durável e alta disponibilidade com redundância e replicação;
* Sgurança através de encriptação automática e controle baseada em roles;
* Escalável com armazenamento virtualmente infinit;
* Gerenciado, cuidando de manutenção e problemas;
* Acessível de qualquer lugar através de HTTP ou HTTPS.

### Mobile

### Databases

### Web

### IoT

### Big Data

### AI

### DevOps


## Links

* [Docs](https://docs.microsoft.com/en-us/learn/modules/welcome-to-azure/3-tour-of-azure-services)