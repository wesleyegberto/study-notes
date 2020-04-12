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

