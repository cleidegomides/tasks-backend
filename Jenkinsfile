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
                withSonarQubeEnv('SONAR_LOCAL') {
                     bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=ac31017d86a4a36250d8cea6d11c01688adfa666 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**,**/model/**,**Application.java "
                }
           }
        }
        stage ('Quality Gate') {
            steps {
                sleep(5)
                timeout(time: 1, unit:'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test') {
           steps {
              dir('api-test') {
                  git 'https://github.com/cleidegomides/tasks-api-test'
                  bat 'mvn test'
              }
           }
        }
        stage ('Deploy Frontend') {
            steps {
                dir('frontend') {
                    git 'https://github.com/cleidegomides/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }
        stage ('Functional Test') {
            steps {
               dir('functional-test') {
                   git 'https://github.com/cleidegomides/tasks-funcional-test'
                   bat 'mvn test'
               }
            }
        }
        stage ('Deploy Prod') {
            steps {
                   bat 'docker-compose build'
                   bat 'docker-compose up -d'
               }
            }
        }
    }
}

