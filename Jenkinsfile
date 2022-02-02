pipeline {
    agent any

    environment {
        scannerHome = tool 'SonarQubeScanner'
        username = 'robelepe'
        PROJECT_NAME = "sonar-robertoledesma"
    }

    stages {
        stage("Checkout") {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']], 
                    userRemoteConfigs: [[credentialsId: 'GitHub', url: 'https://github.com/robelepe/devops_ci.git']]
                ])
            }
        }

        stage('Build') { 
            steps {
                sh 'mvn -B -f billing/pom.xml clean install' 
            }
        }

        stage('Scan') {
            steps {
                withSonarQubeEnv('Test_Sonar') {
                    sh '''${scannerHome}/bin/sonar-scanner \
                    -Dsonar.java.binaries=billing/target/classes \
                    -Dsonar.sources=billing/src/main/java \
                    -Dsonar.projectKey=$PROJECT_NAME'''
                }
            }
        }
        
        stage('Kubernetes Deployment') {
            steps {
                script {
                    withKubeConfig ([credentialsId: 'kubeconfig', serverUrl: 'http://localhost:34559']) {
                        sh 'kubectl create -f $WORKSPACE/namespace-i-robertoledesma.yml'
                    }
                }
            }
        }
    }
}

