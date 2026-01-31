pipeline {
    agent any

    stages {
        stage('Source Checkout') {
            steps {
                checkout scm
                sh 'git rev-parse --short HEAD'
            }
        }
    }
}
