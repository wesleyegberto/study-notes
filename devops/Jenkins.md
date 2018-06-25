Plugins do Jenkins
####


# Docker
> CloudBees Jenkins Docker Build and Publish
  * Permite fazer build e publish de imagens docker para um registry durante o pipeline


# Pipeline Script
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
