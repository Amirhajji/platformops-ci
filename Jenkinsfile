pipeline {
    agent any

    environment {
        GIT_SHA = ""
    }

    stages {

        stage('Source Checkout') {
            steps {
                checkout scm
                script {
                    def sha = sh(
                        script: 'git rev-parse --short HEAD',
                        returnStdout: true
                    ).trim()
                    env.GIT_SHA = sha
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

                sh '''
                    zip -r backend-${GIT_SHA}.zip backend \
                        -x "backend/.venv/*" \
                        -x "backend/__pycache__/*"
                '''
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'backend-*.zip', fingerprint: true
        }
    }
}
