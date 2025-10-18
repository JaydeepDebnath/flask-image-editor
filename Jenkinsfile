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
        stage("RUN Flask app"){
            steps{
                sh '''
                docker run -d -p 5000:5000 $DOCKER_IMAGE
                '''
            }
        }
    }
}
