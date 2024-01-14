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
        stage("Quality Gate"){
          steps{
            timeout(time: 1, unit: 'HOURS') {
            def qg = waitForQualityGate() 
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
             steps{sh './gradlew postPublishedPluginToSlack'}
        }
    }
    
    post { 
        failure { 
            notifyEvents message: 'Pipeline failed', token: 'w0jpwz0koupqiamfrh1set3em96x4lms'
        }
    }
}
