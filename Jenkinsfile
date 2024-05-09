pipeline {
    agent {label 'worker'}
    tools {nodejs "nodejs18"}
    
    stages {
    	stage ('Checkout') {
    	    steps {
    	       checkout([$class: 'GitSCM', branches: [[name: '*/develop']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/dlchoi-91/juice-shop']]]) 
    	    }
    	}

    
        stage('Install') { 
            steps {
                sh 'npm install' 
            }
        }
    
        stage('Test') {
            steps {
                echo 'Testing...'
                snykSecurity(
                  snykInstallation: 'snyk@latest',
                  snykTokenId: 'dc-snyk-api-token',
                  failOnIssues: false,
                  // place other optional parameters here, for example:
                  additionalArguments: '--debug --prune-repeated-subdependencies'
                )
            }    
        }
    
    	stage ('Docker Build') {
    	    steps {
                sh 'docker build -t dlchoi91/juice-shop:dev .'
                }
    	}
    
        stage('Docker Push') {
          steps {
            withCredentials([usernamePassword(credentialsId: 'dc-dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
              sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
              sh 'docker push dlchoi91/juice-shop:dev'
            }
          }
        }
    
    }
}
//}