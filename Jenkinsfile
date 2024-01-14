pipeline {
    agent any

    environment {
        JAVA_HOME = tool 'jdk11'
    }

    node {
        stages{
        stage('Prepare') {
                        sh 'git clone https://github.com/stxndli/OGL_jenkins && cd OGL_jenkins'
                    }
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
                stage("Notify"){
                     sh './gradlew slack'
                }
        }

    }
}
