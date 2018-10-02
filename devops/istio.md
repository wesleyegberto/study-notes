# Istio

É um service smash open source para facilitar a conexão, segurança, controle e observação de serviços, é adicionado uma camada  de forma transparente  em uma arquitetura distribuída existente.
Também é uma plataforma que provê uma API que permite conectar em qualqur plataforma de logging, telemetria ou sistema de políticas.
Istio provê insights comportamentais e controle operacional sobre o service mash.

### Service Mash
Termo para descrever a rede de microserviços que compõe uma aplicação e as interações entre elas.
Algumas caracteristicas:
* Service discovery;
* Load balancing;
* Failure recovery;
* Metrics;
* Monitoring.

Também pode prover caracteristicas mais complexas:
* A/B testing;
* Canary releases;
* Rate limiting;
* Access control;
* E2E authentication.


## Como funciona
O suporte ao Istio é adicionado ao serviços através de um _sidecar proxy_ que é adicionado em todo o ambiente, este sidecar proxy intercepta todas as comunicações de rede entre os serviços.
A configuração e gerenciamento do Istio é feito através do seu _control plane_.
Algumas configurações:
* Load balancer automático para tráfego HTTP, gRPC, WebSocket e TCP;
* Controle granular do comportamento do trágico com regras de roteamento, retries, failovers e fault injection;
* Plugável camada de politicas e API configuração com suporte para access controls, rate limits e quotas;
* Métricas, logs e rastreamento de todo tráfego dentro do cluster, incluindo cluster ingress e egress;
* Segurança na comunicação entre os serviços no cluster com autorização e autenticação baseado em identidades.


### Características
Istio provê:
* Gerenciamento de tráfego:
  * Load balancing para tráfegos HTTP, gRPC, WebSocket e TCP;
  * Controle granular do comportamento do tráfego com regras de roteamento, retries, failovers e fault injection;
  * Camada de políticas e uma API configurável suportando controle de acesso, rate limits e quotas.
* Observabilidade:
  * Métricas automáticas;
  * Logs;
  * Rastreamento.
* Segurança:
  * Controla a comunicação entre serviços com uma forte autenticação e autorização baseada em identidades.

Istio consiste em duas partes: Data plane e Control plane.

[Ver imagem da arquitetura](http://www.eclipse.org/community/eclipse_newsletter/2018/september/images/control_plane.png)

