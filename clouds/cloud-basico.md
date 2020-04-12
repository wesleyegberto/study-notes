# Cloud - Conceitos

## Cloud Deployment Models

Um cloud deployment model define onde os dados serão armazenados e como os clientes irão interagir com ele (como ele chegam nos dados e onde a aplicação irá rodar). Também depende do quanto queremos ou precisamos gerenciar a nossa própria infraestrutura.

### Public

Modelo mais comum.
Neste caso, a empresa não tem infraestrutura local para gerenciar ou atualizar. Toda a infraestrutura roda nos recursos do cloud provider.
Em alguns casos, podemos reduzir custo ao compartilhar recursos com outros usuários desta cloud.

#### Vantagens

* Alta escalabilidade e agilidade: não precisa comprar novos servidor para poder escalar;
* Cobrança pay-as-you-go: paga somente o que usar;
* Não é responsável por manter e atualizar o hardware;
* Conhecimento mínimo para configurar e usar: podemos deixar as skiils e expertise do cloud provider para garantir que os workloads estão seguros e com alta disponibilidade.

#### Desvantagens

* Pode haver específicos requisitos de seguranças que não possam ser satisfeitos;
* Pode haver políticas governamentais, padrões de indústria ou requisitos legais que podem não ser satisfeitos;
* Não podemos gerenciar os hardwares ou serviços da forma que quisermos;
* Requisitos de negócios muito específicos podem não ser satisfeitos.

### Private

A empresa cria o ambiente de cloud no próprio datacenter e provê acesso aos recursos para os usuários.
Simula a public cloud para os usuários mas a empresa tem completa responsabilidade de comprar e manter o hardware e software.

#### Vantagens

* Podemos garantir que a configuração pode suportar qualquer cenário ou aplicação legada;
* Temos controle (e responsabilidade) na segurança;
* Pode satisfazer qualquer restrição de segurança, compliance ou requisito legal.

#### Desvantagens

* Gasto do capital inical para comprar e manter o hardware (CapEx);
* Limitação na agilidade: para escalar primeiro precisamos comprar e configurar o hardware;
* Requer pessoas com alto nível de conhecimento.

### Hybrid

Combina a public e private cloud, permitindo rodar as aplicações nos locais apropriados.

#### Vantagens

* Podemos manter qualquer sistema rodando e acessível que precise de hardware ou SO desatualizado;
* Flexibilidade com o que precisamos rodar local e na cloud;
* Ter vatangem de usar recursos da public cloud quando este for mais barato ou mais fácil de gerenciar e vice-versa;
* Podemos usar nossos próprios recursos para atender cenários de segurança, compliance ou legados.

#### Desvantagens

* Pode ser mais caro que selecionar apenas um modelo de deployment visto que envolve o CapEx;
* Mais complicado de configurar e gerenciar.

## Categorias de Cloud Computing

### Infrastructure as a Service (IaaS)

Categoria mais flexível.
Nos dá  controle completo sobre o hardware que a aplicação roda (infraestrutura de servidores e VM, storage, network e SO).
Ao invés de comprar o hardware, alugamos.

**Shared Responsability Model:** a empresa e o cloud provider são responsáveis por garantir que a aplicação está de pé e rodando.

Cenários:

* **Migração de workloads:** IaaS é gerenciado de uma maneira similiar ao ambiente on-premise.
* **Teste e desenvolvimento:** times podem configurar e desmontar ambientes de teste e desenvolvimento rapidamente.
* **Storage, backup e recovery:** IaaS é útil para gerenciar demanda imprevisível e crescimento constante de armazenamento. Também simplifica o planejamento e gerenciamento de backup e sistema de recuperação.

### Platform as a Service (PaaS)

Provê um ambiente para construção, teste e publicação de aplicações de software.
O objetivo do PaaS é ajudar a criar uma aplicação de forma rápida sem gerenciar a infraestrutura subjacente.
PaaS é o ambiente completo de desenvolvimento e produção na cloud, com recursos que permite a organização entregar todo tipo de aplicação.
Os recursos que são utilizados podem ser cobrados conforme o uso e podem ser acessados pela internet.

Cenários:

* **Framework de desenvolvimento:** provê um framework para desenvolvermos ou customizar aplicações cloud-based. Features da cloud como escalabilidade, alta disponibilidade e capacidade multi-tenant são incluídas, reduzindo a quantidade de código que os desenvolvedores precisam fazer.
* **Analytics ou business intelligence:** ferramentas disponbilizadas permitem a organização analisar e minerar os dados.

### Software as a Servie (SaaS)

Provê software que é centralmente mantida e gerenciada para o cliente final.
Geralmente é baseada em uma arquitetura onde uma versão da aplicação é usada para todos os clientes e licenciada através de uma inscrição mensal ou anual.