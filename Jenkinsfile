pipeline {
    agent any
    stages {
        stage ('Build Backend') {
            steps {
               bat 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests') {
            steps {
               bat 'mvn test'
            }
        }
        stage ('Sonar Analysis') {
           environment {
                scannerHome = tool 'SONAR_SCANNER'
           }
           steps {
                WithSonarQubeEnv('SONAR_LOCAL') {
                     bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=ac31017d86a4a36250d8cea6d11c01688adfa666 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java "
                }
           }
        }
    }
}

