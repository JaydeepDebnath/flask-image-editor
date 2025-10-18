pipeline {
    agent {label 'Jenkins-agent'}
    tools{
        jdk 'Java17'
        maven 'Maven3'
    }
    stages{
        stage("Cleanup Workspace"){
            steps(
                cleanWs()
            )
        }
        stage("Checkout from SCM"){
            steps {
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/JaydeepDebnath/flask-image-editor'
            }
        }
        stage("Build Application"){
            steps(
                sh "mvn clean package"
            )
        }
        // stage("Sonarqube Analysis"){
        //     steps(
        //         script{
        //             withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') { 
        //             sh "mvn sonar:sonar"
        //             }
        //         }
        //     )
        // }
        // stage("Quality Gate"){
        //     steps{
        //         script{
        //             waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
        //         }
        //     }
        // }
        // stage("Docker Build and Push Image"){
        //     steps{
        //         script{
        //             docker.withRegistry('',DOCKER_PASS){
        //                 docker_image = docker.build "${IMAGE_NAME}"
        //             }

        //             docker.withRegistry("",DOCKER_PASS){
        //                 docker_image.push("${IMAGE_TAG}")
        //                 docker_image.push('latest')
        //             }
        //         }
        //     }
        // }

        // stage("Trivy Scan"){
        //     steps{
        //         script{
        //             sh('docker run -v /var/run/docker.sock:/var/run/docker.sock ')
        //         }
        //     }
        // }

        stage("Test Appliction"){
            steps{
                sh 'mvn test'
            }
        }
    }
}