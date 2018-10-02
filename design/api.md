# APIs

### HATEOS
- Hypertext As The Engine of Application State
- Padrão para serviços REST em que os links de navegação são disponibilizados no metadata do payload.


## Twelve-Factor App
* Parâmetros de configurações precisam estar externalizadas do código fonte.


## Design

### Design dos Limites
* Bounded context é uma boa maneira de determinar os limites de um serviço;
* Estabelecer os limites é mais fácil no cenário onde temos uma migração de um monolíto para microserviços, visto que os limites dos serviços e dependências são conhecidas do sistema existente;
* Em um projeto novo, as dependências são difíceis de definir de início;
* A forma mais pragmática para definir os limites do serviço fazer um teste de mesa com os possíveis cenários do processos para levantar as opções (_service litmus test_);
* Se o serviço em análise é uma operação autonoma por natureza então ela pode ser tomada como seu próprio limite, geralmente esses serviços tem poucas dependências com serviços externos (geralmente aceitam um input, executam uma lógica e retornam o resultado - encriptação, notificação, etc);
* Uma decomposição funcional junto com a modelagem da árvore de dependências dessas funções pode ajudar a estabelecer os limites dos serviços:
	* Evite serviços que se comuniquem muito (vários requests);
	* Evite dependência ciclica e sincrona.
* Pense em cada serviço como um produto - proverá e manterá seus dados da forma mais isolada possível seguindo seu negócio;

### Contrato
* Faça apenas o necessário para satisfazer as necessidades atuais, e então mantenha mudando e refatorando o design para acomodar as novas funcionalidades;
* Consumer Driven Contracts é uma boa idea que suporta design evolucionário - CDC define que cada consumidor definam suas expectativas na forma de casos de testes para que o provedor use-as como testes de integração sempre que ocorra alterações.


### Versionamento
* Deve ser considerado desde o início do design;
* Ajuda nos releases sem que quebre os clientes existentes;
* É mais simples controlar a versão a nível de serviço do que a nível de operação;
* 3 formas:
	* URI: `/api/v3/products`
	* Media type: Accept: `application/vnd.company.products-v3`
	* Header: `Version=3`


### Dependências entre Serviços
* Como identificar o impacto de uma alteração? Como saber se todos os serviços dependentes estão rodando corretamente? Como o serviço vai ser comportar se um serviço dependente estiver fora?
* 4 aspectos de design importantes:
	* Reduza as dependências ao fazer um design correto dos limites do serviço;
	* Reduza o impacto ao definir as dependências da forma mais desacopladas possíveis, e também defina as interações usando comunicação assíncrona;
	* Aborde os problemas de dependências usando padrões como circuit breakers;
	* Monitore as dependências usando gráficos de dependência.

