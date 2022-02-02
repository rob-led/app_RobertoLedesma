pipeline {
    agent any

    environment {
        scannerHome = tool 'SonarQubeScanner'
        username = 'rob-led'
        PROJECT_NAME = "sonar-robertoledesma"
    }

    stages {
        stage("Checkout") {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/develop']], 
                    userRemoteConfigs: [[credentialsId: 'nag-devops', url: 'https://github.com/rob-led/app_RobertoLedesma.git']]
                ])
            }
        }

        stage('Build') { 
            steps {
                sh 'mvn -B -f devops-helloworld/pom.xml clean install' 
            }
        }

        stage('Scan') {
            steps {
                withSonarQubeEnv('Test_Sonar') {
                    sh '''${scannerHome}/bin/sonar-scanner \
                    -Dsonar.java.binaries=devops-helloworld/target/classes \
                    -Dsonar.sources=devops-helloworld/src/main/java \
                    -Dsonar.projectKey=$PROJECT_NAME'''
                }
            }
        }
        
        stage('Kubernetes Deployment') {
            steps {
                withCredentials(bindings: [
                      string(credentialsId: 'kubernete-jenkis-server-account', variable: 'api_token')
                      ]) {
                    sh 'kubectl --token $api_token --server https://192.168.49.2:8443 --insecure-skip-tls-verify=true apply -f i-robertoledesma-develop.yaml '
                }
            }
        }
    }
}

