# Angular - Tests

## Conceito

### Tipos de Testes

* Unit tests:
  * Testa unidade de código (pode ser função, pipe, service, class, componente).
  * Tipos de units tests no Angular:
    * Isolado: testamos uma única classe ou função onde instanciamos manualmente passando os argumentos necessários;
    * Integrado: testamos uma unidade através da criação de um module do Angular (para por exemplo testar o template de um componente), pode ser divido em:
      * Shallow: testamos apenas um componente (sem os filhos);
      * Deep: testamos o componente com os filhos.
* Integration tests:
  * Testa um conjunto de unidades de código que juntas entragam uma funcionalidade.
* End to End (E2E) test:
  * Aplicação live running;
  * Utiliza um browser com açÕes automatizadas (webdriver).

### Mocks

Mocks nos ajudam a garantir que estamos testando uma unidade de forma isolada.
Mock permite simular dependências que esta unidade precisa para funcionar de forma completa ou correta.

Tipos de mock:

* Dummies: objeto para ocupar uma dependência;
* Stubs: objeto que tem um comportamento controlável, definimos nele o retorno necessário para completar o cenário que
estamos testando;
* Spies: objeto que rastreia quais métodos deles foram chamados, com quais argumentos e quantas vezes, usamos ele para
garantir que o comportamento esperado da unidade está sendo executado;
* True mocks: objeto que usamos para saber se foi utilizado de uma forma bem específica (se determinado método foi chamado,
quais argumentos, quais não deveriam ser chamados, etc), são mais complexos de montar mas ajudam a garantir o comportamento
esperado.

## Ferramentas

* Karma: test runner no browser;
* Jasmine: test unitário e integrado permitindo mocks;
* Outras ferramentas: Jest, Mocha/Chai, Sion, TestDouble, Wallaby, Cypress.


### Jasmine

Tem um estrutura de BDD que podemos seguir para montar nossos testes.
Arquivo simple-test.spec.ts:

```js
describe('my unit', () => {
    let sut; // system under test

    beforeEach(() => {
        sut = {};
    });

    it('should be true if true', () => {
        // Montagem do cenário
        sut.a = false;
        // Ação
        sut.a = true;
        // Asserção
        expect(sut.a).toBe(true);
    });
});
```

É importante ter o sufixo `.spec.ts` porque o runner fará uma pesquisa por ele.

* `describe`: define um grupo de especificações que precisam ser testadas;
* `it`: define uma especificação única que será testada;
* `beforeEach`: função que roda antes de cada especificação.

Um teste deveria ser uma história completa contido na função `it`. Não deveria ser preciso ficar olhando ao
redor para entender o teste.

Técnicas:

* mova código menos interessante de setup para dentro do `beforeEach`;
* mantenha setup crítico dentro do `it`;
* `it` deveria conter as três partes do teste: arranjo da pré-condição; ação e a asserção.

## Angular Tests

No Angular colocamos os arquivos `.spec.ts` na mesma pasta da unidade que estamos testando, seja uma pipe, service,
componente ou simples classe.
Testes isolados são bem diretos vistos que são apenas classes. Normalmente pipes, services e componentes serão
similares.
As vezes mock será necessário para ajudar a isolar a unidade.

### Unit Tests

#### Pipes

Os testes de Pipes normalmente não tem segredo devido a simplicidade das Pipes.
Criamos um teste normalmente como se fosse uma simples classe.

Arquivo _strength.pipe.ts_:

```ts
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'strength' })
export class StrengthPipe implements PipeTransform {
  transform(value: number): string {
    if(value < 10) {
      return value + " (weak)";
    } else if(value >= 10 && value < 20) {
      return value + " (strong)";
    } else {
      return value + " (unbelievable)";
    }
  }
}
```

Arquivo de teste _strength.pipe.spec.ts_:

```ts
import { StrengthPipe } from './strength.pipe';

describe('StrengthPipe', () => {
  it('should display weak if strength is 5', () => {
    const pipe = new StrengthPipe();

    expect(pipe.transform(5)).toBe('5 (weak)');
  });

  it('should display strong if strength is 10', () => {
    const pipe = new StrengthPipe();

    expect(pipe.transform(10)).toBe('10 (strong)');
  });
});
```

#### Services

Os testes de services podem ser um pouco mais complicado pois podem ter atributos complexos, como uma store
ou um HTTP client.

Arquivo _message.service.ts_:

```ts
import { Injectable } from '@angular/core';

@Injectable()
export class MessageService {
  messages: string[] = [];

  add(message: string) {
    this.messages.push(message);
  }

  clear() {
    this.messages = [];
  }
}
```

Arquivo _message.service.spec.ts_:

```ts
import { MessageService } from './message.service';

describe('MessageService', () => {
  let cut: MessageService = null;

  beforeEach(() => {
    cut = new MessageService();
  });

  it('should have no message to start', () => {
    expect(cut.messages.length).toBe(0);
  });

  it('should add a message when add is called', () => {
    cut.add('message1');

    expect(cut.messages.length).toBe(1);
    const storedMessage = cut.messages[0];
    expect(storedMessage).toBe('message1');
  });

  it('should remove all messages when clear is called', () => {
    cut.add('message to remove');

    cut.clear();

    expect(cut.messages.length).toBe(0);
  });
});
```

#### Componentes

Arquivo _heros.component.ts_:

```ts
import { Component, OnInit } from '@angular/core';

import { Hero } from '../hero';
import { HeroService } from '../hero.service';

@Component({
  selector: 'app-heroes',
  templateUrl: './heroes.component.html',
  styleUrls: ['./heroes.component.css']
})
export class HeroesComponent implements OnInit {
  heroes: Hero[];

  constructor(private heroService: HeroService) { }

  ngOnInit() {
    this.getHeroes();
  }

  getHeroes(): void {
    this.heroService.getHeroes()
    .subscribe(heroes => this.heroes = heroes);
  }

  add(name: string): void {
    name = name.trim();
    const strength = 11;
    if (!name) { return; }
    this.heroService.addHero({ name, strength } as Hero)
      .subscribe(hero => {
        this.heroes.push(hero);
      });
  }

  delete(hero: Hero): void {
    this.heroes = this.heroes.filter(h => h !== hero);
    this.heroService.deleteHero(hero).subscribe();
  }
}
```

Exemplo de um teste unitário do componente (apenas o arquivo `.ts`).
Arquivo _heros.component.spec.ts_:

```ts
import { of } from 'rxjs/internal/observable/of';

import { HeroesComponent } from './heroes.component';

describe('HeroesComponent', () => {
  let HEROES: Array<any>;
  let mockHeroService: any;

  let component: HeroesComponent;

  beforeEach(() => {
    HEROES = [
      { id: 1, name: 'First Hero', strength: 9 },
      { id: 2, name: 'Second Hero', strength: 12 },
      { id: 3, name: 'Third Hero', strength: 34 }
    ];

    // mock the dependency from our component
    mockHeroService = jasmine.createSpyObj(['getHeroes', 'addHero', 'deleteHero']);

    component = new HeroesComponent(mockHeroService);
  });

  describe('delete', () => {
    it('should remove the indicated hero from the heroes list', () => {
      component.heroes = HEROES;
      mockHeroService.deleteHero.and.returnValue(of(true));
      const heroToRemove = HEROES[1];

      component.delete(heroToRemove);

      expect(component.heroes.length).toBe(2);
      expect(component.heroes.indexOf(heroToRemove)).toBe(-1);
    });
  });
});

```

### Integration Tests

Nos testes de integração temos interesse do comportamento da unidade interagindo com suas depedências, seja um service ou
outro componente utilizado no template, e, ainda, sendo mockado ou não.

Podemos mockar os services que ele depende e os componentes que ele utiliza no template, dependendo do nível do teste.

Nos testes de integração podemos usar a classe `TestBed` para criar o module de teste.

O `TestBed` disponibiliza um método para criar os componentes que estamos querendo testar:

`let fixture: ComponentFixture<MyComponent> = TestBed.createComponent(MyComponent)`

A partir do objeto retornado podemos acessa a instância do componente assim como o DOM, diretivas, models, disparar eventos
do Angular, etc.

Com o fixture podemos chamar o seu método `detectChanges()` que irá disparar alguns life cycles do Angular para
iniciar o data binding, executar os hooks do componentes, etc.

#### Shallow Tests

Nos testes de integração shallow normalmente queremos testar apenas um componente isolado, para isso
criamos mock para todos os services que ele depende e os componentes que ele utiliza no template.
Aqui podemos testar apenas a classe componente ou o template em si.

Exemplo de um teste de componente que não recebe dependências ou utilize outros componentes.
Arquivo _hero.component.spec.ts_:

```ts
import { NO_ERRORS_SCHEMA, DebugElement } from '@angular/core';
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';

import { HeroComponent } from './hero.component';

describe('HeroComponent (shallow tests)', () => {
  let fixture: ComponentFixture<HeroComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({
      declarations: [HeroComponent],
      // using NO_ERRORS_SCHEMA ignore unknown elements (not recommended when testing the template)
      schemas: [NO_ERRORS_SCHEMA]
    });
    fixture = TestBed.createComponent(HeroComponent);
  });

  const createHero = () => {
    return { id: 1337, name: 'Duke', strength: 3 };
  };

  it('should have the correct hero', () => {
    fixture.componentInstance.hero = createHero();

    expect(fixture.componentInstance.hero.name).toBe('Duke');
  });

  it('should render the hero name inside an anchor tag', () => {
    fixture.componentInstance.hero = createHero();
    // trigger initial data binding
    fixture.detectChanges();

    // `nativeElement` points to the JS DOM, its depends on runtime
    // maybe the test in running on a non-browser platform that doesn't provide all DOM API
    const heroEl: HTMLElement = fixture.nativeElement;
    expect(heroEl.querySelector('a').textContent).toContain('Duke');

    // `debugElment` is wrapper around the native elements to work safely accross platforms
    const debugElement: DebugElement = fixture.debugElement;
    expect(debugElement.query(By.css('a')).nativeElement.textContent)
      .toContain('Duke');
  });
});
```

Exemplo de um teste de componente que tem dependência de um service e utiliza outro componente.
Para isso instanciamos um mock para o service e criamos uma classe para mockar o componente que é utilizado na tela.

```ts
import { By } from '@angular/platform-browser';
import { Component, Input } from '@angular/core';
import { ComponentFixture, TestBed } from '@angular/core/testing';

import { HeroesComponent } from './heroes.component';
import { HeroService } from '../hero.service';
import { of } from 'rxjs/internal/observable/of';

describe('HeroComponent (shallow tests)', () => {
  let fixture: ComponentFixture<HeroesComponent>;
  let mockHeroService;

  let HEROES: Array<any> = [];

  beforeEach(() => {
    HEROES = [
      { id: 1337, name: 'Duke', strength: 10 },
      { id: 42, name: 'Tom', strength: 12 }
    ];

    mockHeroService = jasmine.createSpyObj(['getHeroes', 'addHero', 'deleteHero']);

    @Component({
      selector: 'app-hero',
      template: '<div></div>'
    })
    class MockHeroComponent {
      @Input() hero: any;
    }

    TestBed.configureTestingModule({
      declarations: [
        HeroesComponent,
        MockHeroComponent
      ],
      providers: [
        // here we use provide to inject our mock when a component needs a HeroService
        { provide: HeroService, useValue: mockHeroService }
      ],
      // we don't need this because we're providing a fake component
      // schemas: [NO_ERRORS_SCHEMA]
    });
    fixture = TestBed.createComponent(HeroesComponent);
  });

  it('should set heroes correctly from the service', () => {
    mockHeroService.getHeroes.and.returnValue(of(HEROES));
    fixture.detectChanges();

    expect(fixture.componentInstance.heroes.length).toBe(HEROES.length);
  });

  it('should create one li for each hero', () => {
    mockHeroService.getHeroes.and.returnValue(of(HEROES));
    fixture.detectChanges();

    const listLiDe = fixture.debugElement.queryAll(By.css('li'));
    expect(listLiDe.length).toBe(2);
  });
});
```

#### Deep Tests

Nos testes de integração deep fazemos o teste do componente interagindo com todos os outros componentes que
ele utiliza.
Para fazer isso podemos mockar todas services que ele depende e adicionar todos os componentes que queremos
testar no `TestBed`.

Os testes de componentes podem ser divididos em _shallow_ e _deep_ tests.

Arquivo _heroes.component.deep.spec.ts_:

```ts
import { By } from '@angular/platform-browser';
import { NO_ERRORS_SCHEMA } from '@angular/core';
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { of } from 'rxjs/internal/observable/of';

import { HeroesComponent } from './heroes.component';
import { HeroComponent } from '../hero/hero.component';
import { HeroService } from '../hero.service';

describe('HeroComponent (shallow tests)', () => {
  let fixture: ComponentFixture<HeroesComponent>;
  let mockHeroService;

  let HEROES: Array<any> = [];

  beforeEach(() => {
    HEROES = [
      { id: 1337, name: 'Duke', strength: 10 },
      { id: 42, name: 'Tom', strength: 12 }
    ];

    mockHeroService = jasmine.createSpyObj(['getHeroes', 'addHero', 'deleteHero']);

    TestBed.configureTestingModule({
      declarations: [
        HeroesComponent,
        HeroComponent
      ],
      providers: [
        // here we use provide to inject our mock when a component needs a HeroService
        { provide: HeroService, useValue: mockHeroService }
      ],
      schemas: [NO_ERRORS_SCHEMA]
    });
    fixture = TestBed.createComponent(HeroesComponent);
  });

  it('should render each hero as a HeroComponent', () => {
    mockHeroService.getHeroes.and.returnValue(of(HEROES));

    // trigger hook ngOnInit
    fixture.detectChanges();

    const heroComponentsDEs = fixture.debugElement.queryAll(By.directive(HeroComponent));
    expect(heroComponentsDEs.length).toBe(2);

    for (let i = 0; i < heroComponentsDEs.length; i++) {
      expect(heroComponentsDEs[i].componentInstance.hero).toEqual(HEROES[i]);
    }
  });

  // example of how to test a button click
  it('should call heroService.delete when the HeroComponent\'s delete button is clicked', () => {
    mockHeroService.getHeroes.and.returnValue(of(HEROES));
    spyOn(fixture.componentInstance, 'delete');

    fixture.detectChanges();

    const heroComponents = fixture.debugElement.queryAll(By.directive(HeroComponent));

    // finds the button and trigger its event passing the $event objet used in the callback
    heroComponents[0].query(By.css('button'))
      .triggerEventHandler('click', { stopPropagation: () => {}});

    // triggering the events from the child
    (<HeroComponent>heroComponents[0].componentInstance).delete.emit(undefined);

    // triggering the event using only its name (won't be affected by refactoring and
    // we need to change this when the template is changed)
    heroComponents[0].triggerEventHandler('delete', null);

    expect(fixture.componentInstance.delete).toHaveBeenCalledWith(HEROES[0]);
  });

  // example of how to test a form input
  it('should add a new hero when the add button is clicked', () => {
    mockHeroService.getHeroes.and.returnValue(of(HEROES));
    fixture.detectChanges();
    const name = 'Uzumaki Naruto';
    mockHeroService.addHero.and.returnValue(of({ id: 203, name, strength: 100 }));

    const inputElement = fixture.debugElement.query(By.css('input')).nativeElement;
    const addButton = fixture.debugElement.queryAll(By.css('button'))[0];

    inputElement.value = name;
    addButton.triggerEventHandler('click', null);
    // we need to call detection to trigger the updates
    fixture.detectChanges();

    const heroesLiDEs = fixture.debugElement.queryAll(By.css('li'));
    expect(heroesLiDEs.length).toBe(3);
    expect(heroesLiDEs[2].nativeElement.textContent).toContain(name);
  });
});
```

#### Service

Para testar services que fazem requisições para API podemos mockar o `HttpClient` manualmente ou utilizar um module
disponibilizo pelo Angular para testes: [HttpClientTestingModule](https://angular.io/guide/http#testing-http-requests).

```ts
import { TestBed, inject } from '@angular/core/testing';
import { HttpClientTestingModule, HttpTestingController } from '@angular/common/http/testing';

import { HeroService } from './hero.service';
import { MessageService } from './message.service';

describe('HeroService', () => {
  let httpTestingController: HttpTestingController;
  let mockMessageService;

  let heroSvc: HeroService;

  beforeEach(() => {
    mockMessageService = jasmine.createSpyObj(['add']);

    TestBed.configureTestingModule({
      imports: [ HttpClientTestingModule ],
      providers: [
        HeroService,
        { provide: MessageService, useValue: mockMessageService }
      ]
    });

    // gets the controller from container
    httpTestingController = TestBed.get(HttpTestingController);
    heroSvc = TestBed.get(HeroService);
  });

  // example how to use `inject()` method
  it('should be able to create a service', inject([HeroService], (heroService: HeroService) => {
    expect(heroService).toBeDefined();
  }));

  describe('getHero', () => {
    it('should call get with the correct URL', () => {
      heroSvc.getHero(4).subscribe();

      // it expects one request to the given URL
      const req = httpTestingController.expectOne('api/heroes/4');
      // returns the value so the observable is returned
      req.flush({ id: 42, name: 'Adam', strength: 42 });
      // asserts that only the given URL was called (other calls will fail)
      httpTestingController.verify();
    });
  });
});
```

## Links

* [Docs do Angular Tests](https://angular.io/guide/testing)

