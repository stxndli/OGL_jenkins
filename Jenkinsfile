pipeline {
    agent any

    environment {
        JAVA_HOME = sh '$(/usr/libexec/java_home -v 11.0.20)'
    }

    stages{
        stage('Test') {
            steps{
            sh './gradlew test'
            }
        }
        stage('SonarQube analysis') {
            steps{withSonarQubeEnv("sonar") {
              sh './gradlew sonar'
            }}
        }
        stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
        stage("Build"){
          steps{sh './gradlew generateMatrixAPI'}
        }
        stage("Deploy"){
           steps{sh './gradlew publish'}
        }
        stage("Notify"){
             steps{sh './gradlew slack'}
        }
    }
}
