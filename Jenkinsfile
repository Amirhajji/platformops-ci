pipeline {
    agent any

    environment {
        GIT_SHA = "unknown"
    }

    stages {

        stage('Source Checkout') {
            steps {
                checkout scm
                script {
                    env.GIT_SHA = sh(
                        script: 'git rev-parse --short HEAD',
                        returnStdout: true
                    ).trim()
                    echo "Building commit ${env.GIT_SHA}"
                }
            }
        }

        stage('Backend Build & Package') {
            steps {
                dir('backend') {
                    sh '''
                        python3 -m venv .venv
                        . .venv/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt
                        deactivate
                    '''
                }

                sh """
                    zip -r backend-${GIT_SHA}.zip backend \
                        -x "backend/.venv/*" \
                        -x "backend/__pycache__/*"
                """
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: "backend-${GIT_SHA}.zip", fingerprint: true
        }
    }
}
