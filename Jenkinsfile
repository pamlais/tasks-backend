pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Test') {
            steps {
                bat 'mvn test'
            }
        }
        stage ('Sonar Analysis') {
            environment{
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL'){
                bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://192.168.99.100:9000 -Dsonar.login=9f801f7b5f332319cd2f7a15c63eb3bd965b7fa3 -D sonar.java.binaries=target"
                }
            }
        }
        stage ('Quality Gate'){
            steps{
                sleep(5) 
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin2', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }

        }
        stage ('API Test'){
            steps{
                dir ('api-test'){
                    git credentialsId: 'github_login', url: 'https://github.com/pamlais/tasks-api-test'
                    bat 'mvn test'
                }
            }
        }
    }
}
