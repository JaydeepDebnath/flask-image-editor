pipeline {
    agent { label 'Jenkins-agent' }

    environment {
        APP_NAME = "flask-image-editor"
        RELEASE = "1.0.0"
        DOCKER_IMAGE = "jay0604/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        SONARQUBE_ENV = "sonarQube-server" 
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

        stage("SonarQube Code Analysis") {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    script {
                        def scannerHome = tool 'SonarScanner'
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=${APP_NAME} \
                                -Dsonar.sources=. \
                                -Dsonar.projectBaseDir=. \
                                -Dsonar.sourceEncoding=UTF-8 \
                                -Dsonar.language=py \
                                -Dsonar.exclusion=**/templates/**,**/static/**,**/uploads/**,**/venv/**,**/*.html
                        """
                    }
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                sh """
                    docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .
                """
            }
        }

        stage("Push Docker Image to DockerHub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                    """
                }
            }
        }

        stage("Run Flask App in Container") {
            steps {
                sh """
                    docker rm -f flask-app || true
                    docker run -d -p 5000:5000 --name flask-app ${DOCKER_IMAGE}:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline execution completed."
        }
    }
}
