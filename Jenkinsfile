pipeline {
    agent { label 'Jenkins-agent' }
    environment {
        DOCKER_IMAGE = 'flask-image-editor:latest'
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
        stage("Build Docker Image") {
            steps {
                sh '''
                docker build -t $DOCKER_IMAGE .
                '''
            }
        }
        stage("RUN Test in Docker"){
            steps{
                sh '''
                docker run --rm \
                -e FLASK_APP=main.py \
                -e FLASK_RUN_HOST=0.0.0.0 \
                -e FLASK_ENV=production \
                -e FLASK_DEBUG=0 \
                $DOCKER_IMAGE pytest
                '''
            }
        }
    }
}
