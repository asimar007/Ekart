pipeline {
    agent any
    tools{
        jdk  'jdk11'
        maven  'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: 'Your credentialsId', poll: false, url: 'https://github.com/asimsk757/Ekart.git'
                // credentialsId you can Generate through Pipeline Syntax
            }
        }
        
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        
        stage('OWASP Scan') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ ', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar-server'){
                   sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url='your Sonarqube url' -Dsonar.log='your sonarqube Token' -Dsonar.projectName=Ekart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Ekart '''
               }
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'your docker cred', toolName: 'docker') {
                        
                        sh "docker build -t shopping-cart -f docker/Dockerfile ."
                        sh "docker tag  shopping-cart asimsk757/shopping:latest"
                        sh "docker push asimsk757/shopping:latest"
                    }
                }
            }
        }
        
        
    }
}
