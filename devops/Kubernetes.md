# Kubernetes (K8s)

* Orientado a código (arquivo descritor em YML ou JSON) que provê portabilidade e centralização;
* Abstração dos recursos de hardware;
* Pool de recursos que agrega capacidade, automaticação eficiente e distribui as aplicações conforme as necessidades;
* Provisionamento automatizado;

* Container: abstração de processos dentro do kernel, permitindo definir limites de recursos (memória, rede, etc);

* Desire state: estado descrito nos PodSpecs.
* Running state: estado atual em execução.



## Componentes do K8s

### Componentes do Master
Provê o painel de controle do cluster, toma decisões globais sobre o cluster, detectando e respondendo os eventos do cluster (como iniciar um novo pod).
* API Server: expõe a API do K8s no master, é o front-end do painel de controle do K8s.
* etcd: consistente e alta-disponibilidade key-value store usado pelo K8s para armazenar todos o estado do cluster.
* Scheduler: componente no master que escuta eventos de pods recém criados (que ainda não estão em execução em um node) para poder selecionar um node que atenda seus requisitos para rodar.
* Controller Manager: componente no master que roda os controllers (Node Controller, Replication Controller, Endpoints Controller e Service Account & Token Controller).
  * Node Controller: responsável por monitorar e responder quando um node cai.
  * Replication Controller: responsávle por manter o # correto de pods para cada objeto replication controller do cluster (cada spec do deployment).
  * Endpoints Controller: popula o objeto endpoints (que juntam os Services e Pods).
  * Service Accunt & Token Controller: cria as contas default e tokens de acessos das API nos novos namespaces.

### Componentes dos Nodes
Componentes que rodam em cada node, mantendo os pods rodando e provendo ambiente de runtime do K8s.
* Kubelet: recebe um conjunto de PodSpecs e garante que os containers especificados neles estejam rodando e healthy.
* kube-proxy: habilita a abstração de serviõs do K8s ao manter as regras de rede no host e efetuando os forwardings de conexões.

### Addons
São pods e services que implementam features do cluster. Os objetos addon são criados no naespace kube-system.
* DNS: obrigátorio, DNS server que resolve os registros de Services do K8s, os containers nos PodSpecs automaticamente incluem esse DNS server no seus resolvedores de DNS.
* Container Resource Monitoring: registra métricas genéricas sobre os containers na base central e provê uma UI para consulta.
* Cluster-level Logging: mecanismo responsávle por salvar os logs dos containers numa base cnetral de logs.

### Pods
Permite o agrupamento lógico de containers iguais ou diferentes que podem compartilhar recursos de volumes, networking e infos.

#### Prioridade
O kubelet trata as prioridade dos pods conforme o QoS (Quality of Service - request e limit) que é definido no resource para o pod. Os pods de menor prioridade são evicted primeiro.

* Lowest  (Best effort): quando não são configurados o request e limit de resource do pod;
* Medium (Burstable): quando são configurados o request e limit de resource do pod com valores diferentes;
* High (guaranteed): quando são configurados o request e limite de resource do pod com valores iguais;

A partir da versão 1.11 do Kubernetes é possível setar uma prioridade nos pods.
```yaml
apiVersion: scheduling.k8s.io/v1
kind: PriorityClass
metadata:
 name: high-priority
value: 1000
---
apiVersion: v1
kind: Pod
metadata:
 name: mypod
spec:
 containers:
 - image: redis
   name: mycontainer
 priorityClassName: high-priority`
```

### Services
Permite o agrupamento de Pods com uma politica de acesso; tambem serve como load balancer entre pods selecionados via label.

### Deployments
Responsável por rodar e atualizar as configurações e instâncias dos Pods.




## Links
* https://www.magalix.com/blog/kubernetes-patterns-capacity-planning
* https://www.magalix.com/blog/kubernetes-patterns-declarative-deployments
