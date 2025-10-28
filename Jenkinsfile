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
                echo "Cleaning up workspace before starting..."
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                echo "Checking out source code from GitHub..."
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/JaydeepDebnath/flask-image-editor'
            }
        }

        stage("SonarQube Code Analysis") {
            steps {
                echo "Running SonarQube static code analysis..."
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
                            -Dsonar.exclusions=**/templates/**,**/static/**,**/uploads/**,**/venv/**,**/*.html,**/*.js,**/*.css \
                            -Dsonar.javascript.enabled=false \
                            -Dsonar.css.enabled=false
                        """ 
                    }
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                echo " Building Docker image..."
                sh """
                    docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .
                """
            }
        }

        stage("Trivy Security Scan") {
            steps {
                echo "Scanning Docker image for vulnerabilities using Trivy..."
                sh """
                    trivy image --exit-code 0 --severity HIGH,CRITICAL ${DOCKER_IMAGE}:${IMAGE_TAG} > trivy-report.txt
                    cat trivy-report.txt
                """
            }
            post {
                always {
                    archiveArtifacts artifacts: 'trivy-report.txt', fingerprint: true
                }
            }
        }

        stage("Push Docker Image to DockerHub") {
            steps {
                echo " Pushing Docker image to DockerHub..."
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
                echo "🏃 Running Flask app container..."
                sh """
                    docker rm -f flask-app || true
                    docker run -d -p 5000:5000 --name flask-app ${DOCKER_IMAGE}:${IMAGE_TAG}
                """
            }
        }

        stage("Cleanup Artifacts") {
            steps {
                echo "Cleaning up Docker artifacts..."
                sh """
                    docker image prune -af || true
                    docker container prune -f || true
                """
                cleanWs()
            }
        }
    }

    post {
        always {
            echo "Pipeline execution completed."
        }
        failure {
            echo "Pipeline failed. Check logs above."
        }
    }
}
