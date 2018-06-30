# Alguns Conceitos sobre Testes

## Técnicas de Testes
* Partição de equivalência
* Análise de Valor Limite
  * Testar os limites e um passo além
* Tabela verdade
* Pairwise (análise combinatória)
* Análise de mutantes


## Quadrantes
Q1 e Q2: automatização foca no suporte do time.
Q2 e Q3: parte manual focam na visão do usuário e do negócio.
Q3 e Q4: focam usabilidade e comportamento da aplicação.
* Q1 (automatizados)
  * Teste unitários;
  * Testes de componentes.
* Q2 (automatizados e manuais)
  * Testes funcionais;
  * Story tests;
  * Protótipos.
* Q3 (manual - perspectiva do usuário)
  * Teste exploratório;
  * Cenários;
  * Testes de usabilidade;
  * Teste de aceitação do usuário;
  * Alpha/beta.
* Q4 (ferramentas)
  * Teste de performance e carga;
  * Teste de segurança;
  * Teste de "ilidade" (manutentabilidade, usabilidade, etc).


## Tipos de Testes
* Mock
  * Mockar o comportamento das APIs que consumimos;
  * Cobrir os casos críticos que nossa aplicação depende.
* Contract
  * Testar os resultados que você espera de uma API;
  * Verificar as operações e campos esperados ainda estão lá.
* Smoke
  * Testar se nossas principais APIs estão no ar;
  * Menor fluxo para saber que as funcionalidades estão funcionando.
* Acceptance
  * Testas funcionalidades que geram valor para o nosso negócio.


## Fluxo de Testes
* Integration Test
  * Mock
* Service Test
  * Smoke
  * Contract
  * Acceptance
* Acceptance Test
  * Smoke
* Funcional Test
  * Web, Mobile
* Não Funcional Test
  * Testar desempenho, segurança, logs, etc.



## Modelo de Abstração
* Page Object
  * Cada página tem uma classe com operações disponíveis na sua tela:
    * Preencher login
    * Preencher senha
    * Clicar no botão Entrar
  * Assim cada página tem sua classe representativa que acaba permitindo facilitar e focar o teste nas ações e compartilhar os objetos entre outras classes (como testes de popups).


## Gerar Logs
Gerar logs da execução de testes em qualquer nível para agilizar a resolução do problema.
* Em caso de falhas (asserts) ou erros (exceptions)
* Gerar arquivos .log
* Gerar screenshots
* Agregar arquivos de resultado em formato xUnit ou usar ferramenta de geração de relatórios


## Ferramentas para Relatórios de Testes
* ExtenteReport
* Allure Framework



## Links
* [Apresentação Elias Nogueira](https://www.slideshare.net/elias.nogueira/arquitetura-bsica-de-testes-para-seu-projeto-java)
