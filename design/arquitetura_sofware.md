# Arquitetura de Software


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

### Vantagens
* Maioria dos desenvolvedores estão familiarizado
* Provê um jeito fácil de escrever código bem organizado, visibilidade da estrutura e testável
* Consistência entre diferentes projetos visto que a estrutura geral são basicamente iguais

### Desvantagens
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

### Vantagens
* Fácil de aprender
* Centralização no negócio
* Provê grande flexibilidade e testabilidade

### Desvantagens
* Adiciona muitas interfaces extras (principalmente quando suportamos apenas uma UI e database)
* Indireção - precisamos buscar os adpters das ports para sabermos o fluxo da aplicação
* Pode gerar confusão quando aplicado com frameworks (se devemos permitir ou não anotações no core)
* Sem guia de organização do código



# Arquitetura em Cebola (Onion)

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

### Vantagens
* Combina bem com DDD
* Acoplamento direcionado
* Provê flexibilidade e testabilidade

### Desvantagens
* Curva de aprendizado
* Interface em todo canto - indireção entre interfaces e implementações



## Microkernel

Util quando a aplicação tem um conjunto principal de responsábilidades e uma coleção de plugins (partes trocáveis - strategy pattern).
Provê um ponto de entrada e o fluxo geral da aplicação, sem realmente saber o que cada plugin faz.

A aplicação provê uma API que os plugins devem implementar. Então a aplicação pode comunicar-se com os plugins através desta interface.

Ideal para aplicações que lêem dados de uma origem, transformam e escrevem em diferentes destinatários. Aplicações de workflow. Aplicações que agendam e executaram tarefas.

### Vantagens
* Provê grande flexibilidade e extensibilidade
* Permite trocar o plugin enquanto a aplicação roda

### Desvantagens
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

### Vantagens
* Os modelos de escritas podem focar na lógica de negócio e validação enquanto os modelos de leitura podem focar nos diferentes cenários de exibição de dados
* Evita consultas complexas (quando leitura e escrita são armazenadas separadamente) e ajuda na performance

### Desvantagens
* Pode ser complexo manter os modelos de escrita e leitura em sincronização/atualizados



## Event Sourcing

Padrão onde são armazenados todos os eventos/ações que definem o estado atual da aplicação. Ao invés de salvar, por exemplo, o saldo da conta é salvo todas as transações de débito e crédito. Mas também podemos salvar separadamente o estado atual para facilitar na leitura.

A partir de todas os eventos salvos deve ser possível gerar no estado atual. Então sempre podemos efetuar uma validação do estado atual ao ler todos os eventos na ordem cronológica em que foram gerados.

Um evento nunca é apagado, ao invés disso é feito um novo evento com a correção do erro (ex. um estorno).

Ideal em aplicações que precisam publicar eventos em sistemas externos, construídas em CQS, possuem domínio complexo, precisam de auditoria de mudança nos dados.

### Vantagens
* Automaticamente disponibiliza um log para audição

### Desvantagens
* Requer disciplina pois não pode apenas corrigir o registro
* Não trivial alterar a estrutura do evento (eventos antigos não terão novos campos) - é preciso tratar no código



## Microservices

Aplicação é divida em um conjunto de aplicações menores (serviços) onde cada uma tela tem seu domínio de atuação e responsabilidade.
Os serviços se comunicam utilizando um protocolo comum e esta interface de comunicação precisa ter retrocompatibilidade, o que requer coordenação entre os serviços e seus times.

Não existe uma regra do quão grande um serviço pode ser mas é o ideal que cada um esteja centrado no seu contexto de negócio.

Ideal para aplicações onde certas partes são usadas intensivamente e precisam ser escaladas. Serviços que provê funcionalidades para várias outras aplicações. Aplicações que seriam bastante complexas quando reunidas em um monolito. Aplicações onde é possível definir os contextos claramente.

### Vantagens
* Cada serviço pode ser escrito, mantido e publicado separadamente
* Fácil de escalar visto que podemos escalar apenas os serviços mais utilizados
* Fácil refatorar as partes pois são pequenas e menos dependente de outras partes

### Desvantagens
* Preocupações extras: comunicação, coordenaçaõ, retrocompatibilidade, loggin, monitoramento e debugging
* Uma simples ação acaba passando em vários serviços o que pode gerar vários pontos de falhas
* Quando não estiver bem estruturado e monitorado pode ser difícil encontrar o ponto de falha



## Links
* https://dzone.com/articles/software-architecture-the-5-patterns-you-need-to-k
* https://dzone.com/articles/layered-architecture-is-good
