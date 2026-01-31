pipeline {
    agent any

    environment {
        GIT_SHA = ''
    }

    stages {

        stage('Source Checkout') {
            steps {
                checkout scm
                script {
                    GIT_SHA = sh(
                        script: 'git rev-parse --short HEAD',
                        returnStdout: true
                    ).trim()
                    echo "Building commit ${GIT_SHA}"
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
                        python -c "import app.main"
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
