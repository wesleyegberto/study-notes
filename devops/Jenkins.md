# Jenkins

## Conceitos
É um servidor de integração contínua.
A Integração Contínua permite antecipar problemas no código através da automatização da
compilação, execução de testes, empacotamento da aplicação e publicação.
Assim se houver algum erro que foi commitado que faça com que o código não compile ou
algum teste falhe, o Jenkis irá nos informar.
Para executar o Jenkis usamos:
`java -jar jenkins.war --httpPort=8180`



### Job
* É uma tarefa que o Jenkins tem que executar;
* Pode ter parâmetros, ter procedimentos para executar antes ou depois da tarefa;
* Pode ter um ou mais builds.

#### Abas
* Geral
  * Permite definir informações e configurações gerais sobre o projeto e sobre o build;
  * Informações como nome, descrição, apelido para exibir no Jenkins, se é projeto do GitHub;
  * Configurações como número máximo de tentativas do checkout do SCM, desabilitar os builds temporariamente, definir se o build é parametrizado (se o usuário vai poder colocar inputs como massa de testes, release notes, etc), se deve descartar informações antigas para economizar disco (descarte por tempo ou quantidade), se deve manter log dos builds mesmo com a limpeza, permitir builds em paralelos, bloquear build se houver algum build de uma dependência ou job filho em execução, etc;
* Gerenciamento do Código Fonte
  * Permite definir o SCM (Git, SVN, etc) do projeto ou superprojeto (com módulos, .gitmodules);
  * Também podemos definir se o Job atuará apenas em uma branch ou se atuará em todas as branches (criando um subjob para cada uma);
  * Também podemos fazer com que o Job trabalhe em cima do merge de todas as branches (usando a opção Comportamento Adicional) e neste caso é ideal que exista um branch separado para o Jenkins usar como destino do merge e só ele deve efetuarcommit nesse branch. Assim sempre que o job finalizar com sucesso ele pode dar um push para garantir que esta branch esteja atualizado no repositório;
  * Também permite configurar vários Comportamentos Adicionais como clean antes ou depois do checkout, checkout para um diretório ou uma branch específica, fazer um merge, ignorar/pular os commits de determinados usuários ou determinado path ou que tenha determinada mensagem, etc;
* Triggers de Builds
  * Permite configurar quando serão feito os builds;
  * Podemos usar intervalo de tempo ou horário específico (notação cron), disparo de build remotamente (usando URL para ser chamada externamente), build após a construção de outro projeto, Git hooks, etc;
* Ambiente de Build
* Build
  * Permite configurar as ações que serão executadas pelo job;
  * Essas ações podem ser fases do Maven, execução de Shell scripts, scripts Ant, script Gradle, argumentos para as ferramentas e etc;
  * Com plugins podemos configurar ações mais específicas como Docker, Vagrant, etc.
* Ação Pós-Build
  * Podemos configurar ações após todo o build do sistema (compilação, execução de testes e empacotamento);
  * Essas ações podem ser publicação remota, notificação por e-mail, limpar workspace, disparar build de outro projeto, publicar relatórios de testes, efetuar push de um merge pré-build, etc;

### Workspace
* É a área de trabalho existente em cada job.
  
### Build
* É uma construção/execução de um job (procedimento de montar um pacote onde ocorre download, compilação e empacotamento);
* Pode ser considerado como uma instância de um job;
* Possui diferentes status:
  * Falha no build;
  * Build instável;
  * Build feito com sucesso;
  * Build pendente;
  * Build cancelado;
  * Build desligado.

### Pipeline
* Encadeamento de um ou mais jobs;
* Os jobs são executados automaticamente quando seu predecessor é finalizado.

### Artefator
* São pacotes resultante de um build;
* Pode ser qualquer arquivo (XML, zip, jar, log, etc).

### Executor
* São threads que executam os jobs;
* Jenkins permite configurar a qtde disponível;
* Por padrão Jenkins tem 2.



## Configuração
Na primeira inicialização, durante o boot, o Jenkins gerará uma chave para ser usada durante a
configuração do usuário administrador.

Após criar o usuário e efetuar login, temos que configurar as variáveis de ambiente ou caminho
do Java e do Maven para que ele possa compilar, testar e empacotar a aplicação. Lá também
podemos configurar o Docker, Gradle, Ant e o Git.

Para configurar vai no menu Gerenciar Jenkins > Global Tools Configuration.
Também precisamos configurar a notifação de e-mail para o Jenkins. Em Gerenciar Jenkins >
Configurar Sistema temos a seção de Notificação de E-mail onde definimos configurações do
servidor SMTP e os dados de acesso. E na seção Jenkins Location, também precisamos
configurar o nome do remetente para fazer mais sentido.



## Plugins
Podemos instalar plugins ou criar uma (utilizando framework Jelly)

### CloudBees Jenkins Docker Build and Publish
* Permite fazer build e publish de imagens docker para um registry durante o pipeline

### Promoted Builds Plugin
* Permite restringir a execução de um job até que um usuário com certa autoridade aprove.

Após instalação, em qualquer build terá um checkbox "Promote builds when" onde podemos:
* Dar um nome e escolher um ícone;
* Restringir quando a promoção pode ocorrer;
* Se precisa de aprovação manual e quem pode aprovar;
* Também permite configurar a aprovação manual com base em alguns critérios.

Quando um usuário sem permissão entrar ele pode ver os status dos jobs em promoção e quem pode aprovar.
Quando um usuário com permissão entrar ele terá acesso a mesma tela mas com adição de um botão aprovar e ao clicar é iniciado a execução e o ícone escolhido é destacado.

### Xvfb
Fornece um virtual frame buffer do X11 Server para que possa executar jobs que requerem acesso X11 (como o selenium).
No job que precise do X11 precisamos:
* Marcar a opção Start Xvfb before build and shut it down after;
* Selecionar qual a instação do Xvfb;
* Marcar a opção Let Xvfb choose display name;
* E, opcionalmente, podemos marcar a opção para logar o output.
Também podemos dar um nome para o display, configurar a resolução e suporte de cores.

### Groovy
* Permite rodas as tasks do Groovy;
* Podemos fazer scripts para controlar a execução (falhando ou não o build) e exibindo mensagens no log ou no resumo do job.

### Gradle
* Permite rodar rotinas em Groovy;
* Interessante para usar com Flyway.

### Backup
* Faz backup e restaura arquivos de configuração.

### Build Timeout
* Define um timeout para os builds;
* No job podemos marcar a opção "Abort the build if it's stuck" e selecionar uma estratégia e uma ação de timeout.

### Job Configuration History
* Grava o histórico de configurações, permitindo comparar e restaurar versões antigas.





## Pipeline Script
* Os plugins fornecem uma maneira de serem chamados via script, basta consultar sua documentação;
* Algumas vars disponíveis para o script:
  * env.JOB_NAME
  * env.BUILD_TAG: tag do build (nome do job + build number)
  * env.BUILD_URL: URL para acessar o resultado do build executado
  * env.BUILD_NUMBER
  * currentBuild.durationString
  * currentBuild.currentResult: SUCCESS, UNSTABLE OU FAILURE
  
* Exemplo de pipeline que efetua um checkout, build e notifica o resultado no Slack:
```
pipeline {
	agent any
	environment {
		appName = 'my-project'
	}
	stages {
		stage('Checkout') {
			deleteDir() // limpar pasta para ter um clean build
			checkout([branches: [[name: '*/master']]], userRemoteConfigs: [[url: 'https://github.com/wesleyegberto/my-project.git']])
		}
		stage('Build') {
			sh 'mvn clean install' // executa um comando
		}
	}
	post { // post build
		always {
			slackSend 'Build finalizado' // usando o plugin de notificação no Slack (aqui podemos passar também outros params como token, cor, etc)
		}
		success {
			slackSend color: 'good', message: 'Build finalizado com sucesso'
		}
		unstable {
			slackSend color: 'bad', message: 'Build instável'
		}
		failure {
			slackSend color: 'good', message: 'Build finalizado com erros'
		}
		changed {
			slackSend color: 'good', message: 'Houve mudanças'
		}
	}
}
```
