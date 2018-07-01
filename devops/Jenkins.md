# Jenkins

## Conceitos

### Job
* É uma tarefa que o Jenkins tem que executar;
* Pode ter parâmetros, ter procedimentos para executar antes ou depois da tarefa;
* Pode ter um ou mais builds.

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

## Groovy
* Permite rodas as tasks do Groovy;
* Podemos fazer scripts para controlar a execução (falhando ou não o build) e exibindo mensagens no log ou no resumo do job.

## Gradle
* Permite rodar rotinas em Groovy;
* Interessante para usar com Flyway.

## Backup
* Faz backup e restaura arquivos de configuração.

## Build Timeout
* Define um timeout para os builds;
* No job podemos marcar a opção "Abort the build if it's stuck" e selecionar uma estratégia e uma ação de timeout.

## Job Configuration History
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
