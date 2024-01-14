pipeline {
    agent any

    environment {
        JAVA_HOME = tool 'jdk11'
    }

    node {
        stage('Test') {
            sh './gradlew test'
        }
    }

    post {
        success {
            script {
                // Additional actions to perform on success
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
