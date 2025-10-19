pipeline {
    agent { label 'Jenkins-agent' }
    environment {
        DOCKER_IMAGE = 'flask-image-editor'
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
        stage("SonarQube code Analysis"){
            steps{
                withSonarQubeEnv('SonarQube'){
                    script {
                        def scannerHome = tool 'SonarScanner'
                        sh "${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=$DOCKER_IMAGE \
                            -Dsonar.sources=. \
                            -Dsonar.sourceEncoding=UTF-8"
                    }
                }
            }
        }
        stage("Build Docker Image") {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }
        stage("Run Flask App"){
            steps{
                sh "docker run -d -p 5000:5000 --name flask-app $DOCKER_IMAGE"
            }
        }
    }
}
