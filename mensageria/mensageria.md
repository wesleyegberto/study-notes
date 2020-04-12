# Mensageria

Publish/Subscribe é um paradigma de interação distribuída bem adaptado à publicação de sistemas escaláveis e desacoplados.

O desacoplamento pode acontecer em 3 dimensões:
* Entidade: produtores e consumidores não tem conhecimento um do outro;
* Tempo: produtores e consumidores não precisam estarem interagindo ativamente ou ao mesmo tempo;
* Sincronização: o produtor e consumidor não precisam esperar um pelo outro durante a interação.

## Características

### Modelo de Inscrição
Lógica de roteamento que decide se e onde a mensagem vinda de um produto irá parar.
Os dois modelos principais são: *tópic-based* e *content-based*.

### Garantias (QoS)

#### 1 - Corretude
A garantia de corretude dos dados pode ser definido em 3 premissas:
* sem perdas (no-loss);
* sem duplicidade (no-duplication);
* sem desordem (no-disorder).

##### 1.1 - Garantias de Entrega
* **No máximo uma (at most once)**:
  * Melhor esforço;
  * Garante a premissa *sem duplicidade*;
  * Em condições normais de operação, os pacotes serão entregues mas em caso de falha pode ocorrer perda de dados (visto que tenta garantir que uma mensagem não seja enviada mais de uma vez);
  * Provê o melhor throughput;
  * Tentar melhor que isso custará recursos.
* **No mínimo uma (at least once):**
  * Garante a premissa *sem perdas*;
  * Tentará garantir que não haja perda de pacotes;
  * Duplicatas e desordem pode ocorrer durante uma recuperação de falha.
* **Exatamento uma (exactly once):**
  * Garante as premissas *sem perda* e *sem duplicidade*;
  * Requerer um transacional caro com commit em duas fases.

##### 1.2 - Garantias de Ordenação

* **Sem ordem:**
  * Sem garantia de ordem;
  * Provê a melhor performance.
* **Ordenação particionada:**
  * Uma particão única pode ser definida para cada fluxo de mensagens que precisam ser consumida em ordem;
  * Provê uma boa performance dependendo da implementação.
* **Ordem global:**
  * Devido à sobrecarga de sincronização para garantir uma ordem global acaba custando muito recurso e penalizando a performance.

#### 2 - Disponibilidade

