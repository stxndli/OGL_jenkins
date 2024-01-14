pipeline {
    agent any
    stages{
        stage('Test') {
            steps{
            sh './gradlew test'
            }
        }
        stage('Archive and generate reports') {
          steps{archiveArtifacts 'build/test-results/'
          cucumber reportTitle: 'Cucumber report',
          fileIncludePattern: 'target/report.json',
          trendsLimit: 10,
          classifications: [
              [
                'key': 'Browser',
                  'value': 'Chrome'
              ]
          ]

          junit 'build/test-results/test/TEST-Matrix.xml'}
        }
        stage('SonarQube analysis') {
            steps{
              withSonarQubeEnv("sonar") {
              sh './gradlew sonar'
            }}
        }
        stage("Quality Gate"){
          options{
            timeout(time: 1, unit: 'HOURS') }
          steps{script{
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
