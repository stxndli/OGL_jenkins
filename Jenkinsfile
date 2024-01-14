pipeline {
    agent any
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
        
        stage("Build"){
          steps{sh './gradlew generateMatrixAPI'}
        }
        stage("Deploy"){
           steps{sh './gradlew publish'}
        }
        stage("Notify"){
             steps{sh './gradlew postPublishedPluginToSlack'}
        }

    }
}
