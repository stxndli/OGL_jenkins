pipeline {
    agent any

    environment {
        JAVA_HOME = tool 'jdk11'
    }

    stages{
        stage('Test') {
            steps{
            sh './gradlew test'
            }
        }
        stage('SonarQube analysis') {
            steps{withSonarQubeEnv() {
              sh './gradlew sonar'
            }}
        }
        stage("Quality Gate"){
              steps{timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
                def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
              }}
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
