# Angular - Arquiteturas e Melhores Praticas

## Planjando a Arquitetura da Aplicação

### Considerações Iniciais da Arquitetura

Pontos para se discutir e documentar antes de iniciar uma aplicação:

* Overview: Para que servirá? Qual os objetivos? Como o cliente utilizará? Quais as vantagens de negócio que vai gerar?
* Features: Quais as possíveis features?
* Segurança do domínio: Qual a forma e o nível de segurança? Terá roles e claims? Como será a comunicação com a API? Utilizará JWT?
* Regras do domínio: Quais as regras que rodaram no app e na API? Validações ocorreram em anbos os lugares? Possível chamadas de API com regras no lugar do app?
* Logging: O que fazer quando um erro ocorre no app? Printar no console ou no local storage? Centralizar os erros para facilitar correção de bugs. Estatísticas de erro.
* Comunicação: HTTP? Web Sockets?
* Modelo de dados: Quais os modelos que serão utilizados para comunicação entre a API e o app? Entre os componentes? Usaremos classes ou só interfaces no TypeScript?
* Componentes features: Quais os componentes do Angular? Suas estruturas? Suas comunicações?
* Funcionalidades compartilhadas: Utilizaremos diretamente as libs de terceiros ou vahttp://codewithdan.me/angular-architecture-planningmos usar um wrapper para facilitar futura troca? Serão reutilizadas entre outros apps?

É interessante fazer um brainstorm com os stakeholders do projeto para detalhar essas considerações.
Template para documentar: http://codewithdan.me/angular-architecture-planning
Exemplo: http://codewithdan.me/angular-architecture-planning-example

É importante definir o style guide da aplicação, iniciando pelo Angular Style Guide e alterando onde for necessário.

Com isso é possível criar um template que pode ser reutilizado entre os projetos.

### Outras Considerações

Outros pontos que podem influenciar a arquitetura da aplicação e seus componentes:

* Acessibilidade: Suportará os padrões de acessibilidade?
* Internacionalização: Será disponibilizado em mais de uma linguage?
* Ambientes: Quais ambientes teremos?
* CI/CD: Terá esteira de build?
* Entrega: Será entregue via CDN? Container? Server?
* Testes: Quais ferramentas serão utilizadas para testes unitários e E2E?

## Organizando Features e Módulos

### Organizando Features

Para organizar as pastas e códigos podemos usar o *LIFT*:

* L - Locate code quickly: forma que permita encontrar as coisas rapidamente;
* I - Identify the code at a glance: permite identificar o componente através do nome (componente, service, guard, etc);
* F - Flattest structure: tentar manter a estrutura flateada o máximo que pudermos;
* T - Try to be DRY: tentar sempre ser DRY.

#### Opções para Organização

Temos duas opções: Convention-based e Feature-based.
O time do Angular recomenda feature-based (é utilizado no Angular CLI).

* Convention-based:
  * Segue estritamente convensão de nome;
  * Códigos relacionados podem ficar separados;
  * Pode resultar em uma pasta com um monte de arquivos.
* Feature-based:
  * São organizadas em suas próprias pastas;
  * São autocontidas;
  * Fácil de encontrar tudo que está relacionado a uma feature.

Organizando as features:

* Usar feature-based;
* Usar Angular CLI para gerar as pastas e componentes;
* No mínimo um module por feature (se apropriado);
* Tentar manter a hierarquia das features flateadas e sem muita profundidade nas pastas.

### Modules para Features

Formato recomendado:

```
/orders
    /routes.module.ts
    /orders.module.ts
```

Permite isolar o módulo de pedidos ao deixar que a feature cuide das suas próprias rotas e componentes.
Facilitar ter um time apenas para esta feature visto que a necssidade de conhecer tudo diminui.

### Modules Core e Shared

Esse dois módulos possuem as coisas que são compartilhadas através da aplicação.

* Core:
  * Coisas relacionadas à base e ao negócio da aplicação;
  * Deve conter singleton service;
  * Deve ser importado apenas no root da aplicação (adicionar código para validar multiplos carregamento);
  * Exemplos: LoggingService, ErrorService, DataService.
* Shared:
  * Coisas relacionadas ao Angular que são reutilizáveis;
  * Componentes, pipes, diretivas;
  * Será importado em todos os módules;
  * Poderá ser extraído para uma lib;
  * Exemplos: CalendarComponente, SelectComponente.

Podemos criar um projeto de lib. Para criar:

```js
ng g lib acme-shared

ng build acme-shared
```

O Angular CLI irá vincular a lib no projeto principal, então podemos apenas importar o módulo onde formos utilizar.

```js
import { AcmeSharedModule } from 'acme-shared';
```

Estrutura final do projeto:

```
/orders
    /routes.module.ts
    /orders.module.ts
/core
/shared
/projects/acme-shared
```

## Estrutura dos Componentes

### Padrão Container Presentation Componentes

Esse padrão nos orienta a dividir um componente complexo em pedaços menores onde basicamente teremos dois tipos: Container e Presentation.

* Container: responsável por gerenciar o estado, faz a busca e envio dos dados;
* Presentation: responsável por renderizar e receber interação do usuário;

Neste padrão teremos um componente pai que será o container que será o responsável por carregar ou enviar os dados utilizando um `Service` e
passará para um componente filho exibir.
Essa passagem de dados pode ser feita via input properties.

Orientações:

* Passar o estado do container -> presentation usando input properties;
* Comunicação do presentation -> container usando output properties;
* Utilizar Change Detection Strategy no modo `OnPush` nos componentes presentation;
* Considere a clonagem de tipos complexos;
* Utilizar child routes apropriadamente.

#### Exemplo

No componente container faria o request dos dados do cliente e passaria para o componente presentation renderizar.
O componente presentation poderia ter um form que ao clicar no botão submit efetuaria a emissão do evento usando um `EventEmitter`,
e o componente container fica escutando esse evento para então poder enviar os dados para a API.

```html
<app-customer-details [customer]="customer" (changed)="onCustomerChange($event)"></app-customer-details>
```

#### Change Detection Strategies

Ajuda a impedir que o componente presentation altere os dados diretamente.

Mudanças somente serão disparadas quando:

* Referência da input property mudar;
* Eventos de output property ou DOM são disparados;
* Async pipe recebe um evento;
* Change detection é manualmente invocado através do ChangeDetectorRef.

Benefícios:

* Performance: componente não é checado até que condições do OnPush são atendidas;
* Evita que o componente presentation altere o estado que deveria vir do componente container.

#### ngOnChanges: reference vs value

Quando usamos a estratégia OnPush os componentes somente serão atualizando caso o valor ou referência sejam atualizados, por 
isso é preciso lembrar que ao passar um array para o componente presentation é preciso atualizar a referência do componente
container.

```js
this.customers = [...this.customers, newCustomer];
```

Também é interessante trabalhar com clonagem de objeto (usando lib clone ou immutable.js) para facilitar a resolução de erros
de componentes não sendo atualizados.

## Comunicação entre Componentes

Existem algumas formas de permitir a comunicação entre diferentes componentes de qualquer lugar do site.

* Event bus service:
  * Padrão Mediator;
  * Angular service atua como um intermediador entre os componentes;
  * Componentes que se inscrevem nos eventos não sabem de onde os dados vem;
  * Baixo acoplamento;
  * Dependem de subject/observable.
* Observable service:
  * Padrão Observer;
  * Angular service que expõe um observable;
  * Componentes sabem de onde vem os dados;
  * Baixo acoplamento mas pior que o event bus;
  * Dependem de subject/observable.

### RxJS Subjects

Um subject permite enviarmos valores para um ou mais inscritos.

Alguns dos principais tipos de subjects:

* Subject
* BehaviorSubject
* ReplaySubject
* AsyncSubject

```ts
new Subject<string>().asObservable().subscribe(it => console.log(it));
new BehaviorSubject<string>().asObservable().subscribe(it => console.log(it));
new ReplaySubject<string>().asObservable().subscribe(it => console.log(it));
new AsyncSubject<string>().asObservable().subscribe(it => console.log(it));
```

Precisamos sempre desinscrever dos observables para evitar memory leaks.
Podemos fazer manualmente através do ngOnDestroy, após se inscrever no observable precisamos salvar em um atributo para
poder de desinscrever no final do componente.
Existe alguns pacotes que podem nos ajudar nisso: `ngx-auto-unsubscribe` e `subsink`.

#### Subject

Somente os inscritos no momento do envio da mensagem que receberão o valor, futuros inscritos não receberão valores enviados
anteriormente

#### BehaviorSubject

Permite que futuros inscritos recebam o último valor que foi enviado mesmo que não estivessem inscritos no momento do envio.

#### ReplaySubject

Permite que futuros inscritos recebam todos os valores que já foram enviados.

#### AsyncSubject

Somente enviará o último valor para os inscritos quando o observable completar.

### Formas de Comunicação

#### Event Bus Service

Centraliza toda a comunicação na aplicação.

```ts
@Injectable()
export class EventBusService {
    private subject = new Subject<any>();

    on(eventType: EventType, action: any): Subscription {
        return this.subject
            .pipe(filter((e: Event) => e.type === eventType))
            .map((e: Event) => e.value)
            .subscribe(action);
    }

    emit(event: Event) {
        this.subject.next(event);
    }
}

export enum EventType {
    EVENT_1, EVENT_2
}

export class Event {
    constructor(public type: EventType, value?: any) {}
}
```

#### Observable Service

Teremos algumas services especifica para cada tipo de evento que desejamos comunicar.

```ts
@Injectable()
export class UserService {
    private users: Array<any> = [];

    private subject: BehaviorSubject<Array<any>>;
    public users$: Subscription<Array<any>>;

    constructor() {
        this.subject = new BehaviorSubject<Array<any>>(this.users);
        this.users$ = this.subject.asObservable();
    }

    public addUser(user: any) {
        this.users.push(user);
        this.subject.next(this.users);
    }
}
```

## Gerenciamento de Estado

Conforme a aplicação cresce pode surgir a necessidade de gerenciar estado entre vários componentes.
Motivos para se gerenciar o estado:

* Origem única de verdade
* Previsiblidade
* Imutabilidade
* Rastrear mudanças

Estados que podemos gerenciar:

* Configurações e preferências da aplicação
* Sessão do usuário
* Entidades de negócio

Podemos fazer isso de algumas formas:

* Services
* NgRx
* ngrx-data
* Observable store
* Outros: Akita, Ngxs, Mobx

### Angular Services

Prós: forma mais simples; podemos injetar em qualquer lugar; podemos prover notificações utilizando Subject.
Cons: pode facilmente virar uma bagunça com repetição de funcionalidade caso não haja uma boa comunicação.

### NgRx

Combinação de Redux + RxJS.
Prós: origem de dados única para o estado; imutabilidade; consistências através dos times; ferramenta de diagnóstico
que permte observar a store.
Cons: aumento considerável da quantidade e/ou complexidade do código quando não é bem planejado.

Componentes:

* Store: armazena o estado;
* Actions: ação para manipular o estado;
* Reducers: executam a ação e atualizam o estado;
* Selectors$: carregam os estados.

Fluxo: componente -> action -> reducer -> state -> selector -> componente

### ngrx-data

Simplificação do NgRx.
Prós: simplificação do NgRx; sem boilerplate; pode ser customizado a nível de reducer e selectors.

### Observable Store

Provê uma maneira simples de gerenciar o estado no frontend que também possui algumas características chaves oferecidas
por outras opções de gerenciamento de estado mais complexas.

* Single source of truth
* Estado imutável
* Provê notificações de mudança de estado para inscritos
* Rastreia histórico de mudança de estado
* Quantidade mínima de código
* Funciona com qualquer framework

Fluxo: componente -> service (possui Observable Store com o estado) -> componente

## Considerações Adicionais

### Funções vs Pipes

Funções que são chamadas a partir do template são invocadas sempre que o evento onChange dispara.
Uma pipe pura só é chamada quando o valor de input muda.

Podemos usar memo decorator (pacote 'memo-decorator') para cachear as pipes, assim se o um mesmo input é passado para a mesma
pipe ele utilizará o valor cacheado.

### Segurança

* CORS:
  * permite um browser efetuar requisição para um domínio ou porta diferente;
  * habilitador no servidor;
  * limitar os domínios, headers e methods permitidos.
* CSRF:
  * hablitar CSRF no servidor se utiliza autenticação com cookie;
  * Angular lê o cookie retornado pelo servidor e adiciona automaticamente nos requests, será enviado somente para
  o mesmo domínio que setou.
* Route guards:
  * define route guards necssários para a aplicação de acordo com o usuário ou role;
  * sempre replicar a regra de segurança no servidor.

