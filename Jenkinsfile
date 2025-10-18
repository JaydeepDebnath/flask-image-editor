pipeline {
    agent { label 'Jenkins-agent' }
    environment {
        VENV_DIR = 'venv'
    }   
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/JaydeepDebnath/flask-image-editor'
            }
        }
        stage("Setup Python Environment") {
            steps {
                sh '''
                python3 -m venv $VENV_DIR
                source $VENV_DIR/bin/activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }
        stage("Run Tests") {
            steps {
                sh '''
                source $VENV_DIR/bin/activate
                pytest
                '''
            }
        }
        stage("Build Docker Image") {
            steps {
                sh '''
                docker build -t flask-image-editor:latest .
                '''
            }
        }
    }
}
