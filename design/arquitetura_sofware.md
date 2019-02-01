# Arquitetura de Software


## Terminologia

Código: pedaço de código, método, classe ou grupo de classes.
Terminologia com base no livro Software Architecture in Practice por Len Bass, Paul Clements e Rick Kazman.

##### Funcional
* Qualquer pedaço de código que tenha papel puramente técnico na aplicação;
* Não está relacionada ao domínio;
* Simplesmente representa uma capacidade técnica na aplicação;
* Ex.: Layers, Factories, Repositories, Value Objects, Views, ViewModels.

##### Conceitual
* Qualquer código que reflita um conceito do domínio na aplicação;
* É diretamente relacionada ao domínio;
* Representa uma capacidade de negócio na aplicação;
* Ex.: User, Product, Stock Management, Product Variants, Checkout, Upsells.
* Um classe pode ser funcional e conceitual, ex.: Money (refere-se a dinheiro quando falando sobre o negócio e refere-se a Value Object quando falando sobre o código).

##### Módulo
* É um pacote (conjunto de classes) funcional que reflete uma capacidade técnica na aplicação;
* É desacoplada e pode ser substituída por outra implementação;
* Pode ter granularidade baixa (módulo de segurança ou ORM) ou alta (client, server).

##### Componente
* É um pacote conceitual que reflete uma capacidade de negócio;
* Idealmente, é desacoplada de outros módulos e componentes;
* Idealmente, reflete um bounded context.
* Ex.: User, Product, Checkout.

##### Aplicação
* "Código" que o usuário com determinada necessidade interage para suprir esta necessidade;
* É construída em cima dos componentes.

##### Sistema
* Conjunto de aplicações que, de alguma forma, trabalham juntos para prover funcionalidades para suprir as necessidades de uma empresa (Enterprise Application);
* Essas aplicações podem ou não ser construídas em cima dos mesmos componentes;
* Essas aplicações podem ser interna ou de terceiros (como gateway de pagamento).

##### Arquitetura
* "É o conjunto de estruturas necessárias para refletir sobre o sistema, que consiste elementos de softwares, relacionamento entre eles e propriedades de ambos." - *Clements et al, 2010*;
* Todas decisões técnicas que estão relacionadas a todos os recursos de desenvolvimento, como frameworks, padrões de códigos, documentação, processos, etc;
* Conjunto de decisões técnicas que são difíceis de mudar mais tarde no projeto;
* É visão geral do sistema com suas estruturas, componentes e suas relações;
* Ela prepara o projeto para mudanças (seja com código ou postergando decisões) e reuso dos componentes e módulos;
* Ela define padrões para consistência dos resultados e leveza dos processos, como padrões de código, estágios de desenvolvimento, entrega contínua.



### Sinais (Smells) de uma Arquitetura Ruim

* **Rigidez:** Software rigido é difícil de mudar porque uma mudança acaba gerando a necessidade de mudar outros lugares - rabbit hole.
* **Fragilidade:** Quando ocorre uma mudança outros lugares quebram de maneira inexperada mesmo sem ser relacionadas e geralmente não é previsível.
* **Imobilidade:** Quando contém partes que poderia ser útil para outros lugares mas o risco e o trabalho para separar é muito grande.
* **Viscosidade:** Quando é muito mais fácil fazer da forma errada do que da forma correta. Implica que é mais fácil implementar uma mudança através de um hack/gambiarra.
* **Repetições Necessárias:** Acontece quando abstrações necessárias não foram feitas, seja por falta de experiência ou tempo. O código não precisa ser necessariamente o mesmo mas existem regras de negócio que estão definidas em mais de uma lugar.
* **Opacidade:** Código que foi escrito de uma maneira opaca e confusa, e que precisa de dividido em vários métodos para entender o que fazem.
* **Complexidade Desnecessária:** Na tentativa ávida de evitar os smells acima, acaba-se criando todos os tipos de abstrações e preparações para possíveis mudanças. Um bom design de software é leve, flexível, fácil de ler e entender e, acima de tudo, fácil de alterar para não precisarmos prever todas as possíveis mudanças (YAGNI).



### Estilo Arquitetural x Padrão Arquitetural x Design Patterns

#### Estilos Arquiteturais
* É o design da aplicação no mais alto nível de abstração;
* Estilo arquitetural mostra, de uma maneira bem geral, como organizar nosso código;
* É o nível mais alto de granularidade, especifica as camadas, módulos gerais, quais e como são as interações entre eles acontecem;
* Podem ser implementados de diferentes maneiras com ambiente técnico específico, políticas específicas, frameworks e práticas;
* Ex.:
	* Component-based
	* Monolithic application
	* Layered
	* Pipes e filters
	* Event-driven
	* Publish-subscribe
	* Plug-ins
	* Client-server
	* Service-oriented

#### Padrões Arquiteturais
* Padrão arquitetural é uma maneira de implementar um estilo arquitetural;
* Um padrão é uma solução recorrente para um problema recorrente;
* Padrões arquiteturais solucionam problemas relacionados ao estilo arquitetural;
* Tem impacto extensivo sobre o code base, frequentemente impactando na aplicação de horizontalmente (estrutra do código na camada) ou verticalmente (comunicação entre as camadas);
* Ex.:
	* Three-tier
	* Microkernel
	* Model-View-Controller
	* Model-View-ViewModel

#### Design Patterns
* Diferenciam dos padrões arquiteturais em questão de escopo, são mais locais;
* Tem menor impacto no code base pois seu impacto é em uma seção específica do código.
* Ex.:
	* Factory class
	* Factory method
	* Strategy pattern



## Monolíto (Monolithic)
Todo o código é publicado e roda como um processo único em um único node.
Pode ter módulos e componentes separados porém após a publicação eles rodam juntos em um único processo.
Cenários onde não é ideal usar monolíto:
* Escalabilidade independente de diferentes componentes (de negócio);
* Diferentes componentes ou módulos precisam ser escritos em diferentes linguagens;
* Publicação independente, seja por causa de um componente que tem uma taxa de atualização maior que os outros componentes, neste caso toda a aplicação acaba tendo que ser republicada causando atraso e lentidão no processo.

#### Big Ball of Mud (Spaghetti Architecture)
Anti-pattern para este estilo arquitetural onde a estrutura dos pacotes e seus relacionamentos não são explícito, coesão estrutural e o encapsulamento não existe ou é mínimo, dependências não seguem nenhum regra e é muito difícil visualizar, alterar e refatorar os subsistemas.



## Arquitetura em Camadas (Layered)

A ideia basicamente consiste em dividir o código em camadas onde cada uma tem sua responsabilidade e provê um serviço para uma camada acima.
Geralmente em cada camada também teremos algum outro tipo de organização pois a divisão em camadas não nos diz nada sobre o design ou implementação do projeto, apens nos diz sobre sua estrutura geral.

Não há um número definido de camadas, as mais comuns são:
* Presentation layer
* Application layer
* Domain layer
* Persistence layer
* Infrascructure layer

A ideia é que o usuário inicia um código na presentation layer a partir de alguma ação (ex. click). A presentation layer então chama a camada abaixo dela.

Com isso, as camadas mais altas sempre dependem das camadas abaixo. E um código deve sempre está em sua respectiva camada e acordo com sua funcionalidade, ou seja, o código de uma camada não pode residir em outra camada.

Ideal para aplicações de negócio que façam mais que apenas CRUD.

#### Vantagens
* Maioria dos desenvolvedores estão familiarizado
* Provê um jeito fácil de escrever código bem organizado, visibilidade da estrutura e testável
* Consistência entre diferentes projetos visto que a estrutura geral são basicamente iguais

#### Desvantagens
* Tende a desenvolver uma aplicação monolita que é difícil de dividir
* Esconde os casos de uso da aplicação, é preciso olhar as classes e implementação para ver claramente
* Geralmente acabamos escrevendo muito código para passar entre as camadas sem adicionar nenhum valor
* Baixa coesão visto que as classes que contribuem em um cenário estão separadas por conceitos técnicos



## Arquitetura Hexagonal

Move o foco do desenvolvedor das camadas conceituais para uma distinção entre as partes internas e externas da aplicação.
As partes internas consistem nas regras de negócio e seus casos de uso enquanto as partes externas consiste no restante: UI, banco de dados, mensagens, etc.

A comunicação entre as partes internas e externas é feita através de interface de abstrações que são chamadas _Ports_ e sua implementação é chamada _Adapter_.

Possui 3 princípios a serem seguidos:
* A parte interna não sabe nada da parte externa
* Deve ser possível usar qualquer adapter que cabe numa determinada port
* Nenhuma regra de negócio ou caso de uso vai na parte externa

Com a separação do core da aplicação na parte interna estamos definindo o comportamento e regras de negócio da aplicação em termos de ports abstratas, fazendo nossa aplicação independente do modelo da entrega.
A lógica principal da aplicação  está pura e limpa de qualquer tecnologia que usamos, logo pode ser bem entendida e testada sem nos atermos àquelas tecnologias em uso.

Separando o core e as outras partes usando _ports_ e _adapters_ podemos simplemente trocar qualquer parte externa facilmente (ex. UI ou banco de dados).

Assim como na arquitetura em camdas, também não é feito nenhuma premissa sobre como o código deve ser organizado.

Ideal em aplicações que precisam centralizar toda a regra principal, que precise de grande flexibilidade e testabilidade.

#### Vantagens
* Fácil de aprender
* Centralização no negócio
* Provê grande flexibilidade e testabilidade

#### Desvantagens
* Adiciona muitas interfaces extras (principalmente quando suportamos apenas uma UI e database)
* Indireção - precisamos buscar os adpters das ports para sabermos o fluxo da aplicação
* Pode gerar confusão quando aplicado com frameworks (se devemos permitir ou não anotações no core)
* Sem guia de organização do código


## Arquitetura em Cebola (Onion)

Mistura da arquitetura em camadas e hexagonal. Utiliza a divisão em camadas mas com uma visão diferente da arquitetura em camadas.

As camadas são bem estabelecidas:
* Domain model: residem as entidades e classes intimamente relacionadas (ex. value objects)
* Domain services: residem as classes de processo de negócio
* Application services: residem as classes de caso de uso
* Outer layer: residem classes externas como UI, database e testes

O núcleo da aplicação é formado pelas camadas Domain model, domain services e application services. Este núcleo contem toda lógica necessária para rodar e testar todas aplicação conquanto que suas dependências sejam fornecidas.

Classes das camadas mais externas podem depender de classes das camadas mais internas mas o inverso não é permitido.

A maior diferença entre Arquitetura em camadas e em cebola é a direção das dependências e, assim, acoplamento.

Na arquitetura em camadas, todas as camadas acima da camada infraestrutura podem depender dela enquanto que na arquitetura em cebolas não visto que está está na parte mais externa.

#### Vantagens
* Combina bem com DDD
* Acoplamento direcionado
* Provê flexibilidade e testabilidade

#### Desvantagens
* Curva de aprendizado
* Interface em todo canto - indireção entre interfaces e implementações



## Microkernel

Util quando a aplicação tem um conjunto principal de responsábilidades e uma coleção de plugins (partes trocáveis - strategy pattern).
Provê um ponto de entrada e o fluxo geral da aplicação, sem realmente saber o que cada plugin faz.

A aplicação provê uma API que os plugins devem implementar. Então a aplicação pode comunicar-se com os plugins através desta interface.

Ideal para aplicações que lêem dados de uma origem, transformam e escrevem em diferentes destinatários. Aplicações de workflow. Aplicações que agendam e executaram tarefas.

#### Vantagens
* Provê grande flexibilidade e extensibilidade
* Permite trocar o plugin enquanto a aplicação roda

#### Desvantagens
* Pode ser difícil definir o que percence ao núcleo e o que pertence ao plugin
* Uma API pode não ser perfeito para os plugins futuros (exigindo refatoração cuidadosa)



## CQRS

Acrônimo para Command and Query Reponsability Segregation.
A ideia central é que as operações de leituras e escritas devem ser separadas totalmente.
Os modelos usados nas operações de escrita (commands) serão diferentes dos modelos usados nas operações de leitura (queries).
As operações de escrita podem escrever os dados em um lugar enquanto as operações de leitura podem ler de outro, porém precisamos de um intermediário para fazer essa transição (geralmente event sourcing).

Quando o usuário executa uma ação, a aplicação gera um comando que é enviado para o serviço que, por sua vez, pode gerar uma notificação para outro serviço fazer uma transformação de dados e salvar o resultado em um local separado para futura leitura.

Quando a aplicação precisa mostrar algo para o usuário ela consulta o serviço de leitura para carregar esses transformados.

Ideal para aplicações que uma grande quantidade de leituras e aplicações com domínio de negócio complexo.

#### Vantagens
* Os modelos de escritas podem focar na lógica de negócio e validação enquanto os modelos de leitura podem focar nos diferentes cenários de exibição de dados
* Evita consultas complexas (quando leitura e escrita são armazenadas separadamente) e ajuda na performance

#### Desvantagens
* Pode ser complexo manter os modelos de escrita e leitura em sincronização/atualizados



## Event Sourcing

Padrão onde são armazenados todos os eventos/ações que definem o estado atual da aplicação. Ao invés de salvar, por exemplo, o saldo da conta é salvo todas as transações de débito e crédito. Mas também podemos salvar separadamente o estado atual para facilitar na leitura.

A partir de todas os eventos salvos deve ser possível gerar no estado atual. Então sempre podemos efetuar uma validação do estado atual ao ler todos os eventos na ordem cronológica em que foram gerados.

Um evento nunca é apagado, ao invés disso é feito um novo evento com a correção do erro (ex. um estorno).

Ideal em aplicações que precisam publicar eventos em sistemas externos, construídas em CQS, possuem domínio complexo, precisam de auditoria de mudança nos dados.

#### Vantagens
* Automaticamente disponibiliza um log para audição

#### Desvantagens
* Requer disciplina pois não pode apenas corrigir o registro
* Não trivial alterar a estrutura do evento (eventos antigos não terão novos campos) - é preciso tratar no código



## Microservices

Aplicação é divida em um conjunto de aplicações menores (serviços) onde cada uma tela tem seu domínio de atuação e responsabilidade.
Os serviços se comunicam utilizando um protocolo comum e esta interface de comunicação precisa ter retrocompatibilidade, o que requer coordenação entre os serviços e seus times.

Não existe uma regra do quão grande um serviço pode ser mas é o ideal que cada um esteja centrado no seu contexto de negócio.

Ideal para aplicações onde certas partes são usadas intensivamente e precisam ser escaladas. Serviços que provê funcionalidades para várias outras aplicações. Aplicações que seriam bastante complexas quando reunidas em um monolito. Aplicações onde é possível definir os contextos claramente.

#### Vantagens
* Cada serviço pode ser escrito, mantido e publicado separadamente
* Fácil de escalar visto que podemos escalar apenas os serviços mais utilizados
* Fácil refatorar as partes pois são pequenas e menos dependente de outras partes

#### Desvantagens
* Preocupações extras: comunicação, coordenaçaõ, retrocompatibilidade, loggin, monitoramento e debugging
* Uma simples ação acaba passando em vários serviços o que pode gerar vários pontos de falhas
* Quando não estiver bem estruturado e monitorado pode ser difícil encontrar o ponto de falha



## Links
* https://dzone.com/articles/software-architecture-the-5-patterns-you-need-to-k
* https://dzone.com/articles/layered-architecture-is-good
* https://herbertograca.com/2017/07/03/the-software-architecture-chronicles/
* http://www.cvc.uab.es/shared/teach/a21291/temes/object_oriented_design/materials_adicionals/principles_and_patterns.pdf
* https://docs.microsoft.com/en-us/previous-versions/aa480021(v=msdn.10)