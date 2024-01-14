pipeline {
    agent any

    environment {
        JAVA_HOME = tool 'jdk11'
    }

    node {
        stage('Test') {
            sh './gradlew test'
        }
        stage('SonarQube analysis') {
            withSonarQubeEnv() {
              sh './gradlew sonar'
            }
        }
        stage("Quality Gate"){
              timeout(time: 1, unit: 'HOURS') { // Just in case something goes wrong, pipeline will be killed after a timeout
                def qg = waitForQualityGate() // Reuse taskId previously collected by withSonarQubeEnv
                if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
                }
              }
            }
        stage("Build"){
          sh './gradlew generateMatrixAPI'
        }
        stage("Deploy"){
           sh './gradlew publish'
        }
        
    }

    post {
        success {
            script {
                sh './gradlew slack'
            }
        }

        failure {
            script {
                // Additional actions to perform on failure
            }
        }

        always {
            script {
                // Additional actions to perform regardless of success or failure
            }
        }
    }
}
