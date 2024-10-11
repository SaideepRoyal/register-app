pipeline {
    agent { label 'Jenkins-Agent' }
    
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "saideepg361"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/SaideepRoyal/register-app'
            }
        }
        
        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialId: 'Jenkins-sonarqube-token') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("Quality Gate") {
            steps {
                script {
                   waitForQualityGate abortpipeline: false, credentialId: 'Jenkins-sonarqube-token' 
                }
            }
        }
        stage("Build & Push Docker Image") {
            steps {
                script {
                    // Install buildx if necessary
                    sh 'docker buildx install || true'
                    
                    // Create a new builder instance
                    sh 'docker buildx create --use || true'

                    // Build the Docker image using Buildx
                    sh 'docker buildx build DOCKER_PASS--platform linux/amd64,linux/arm64 -t my-image:latest --push .'
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build("${IMAGE_NAME}")
                        
                    }
                     docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                         docker_image.push('latest')
                     }
                }
            }
        }
        
    }
}

