pipeline {
    agent { label 'jenkins-agent' }

    tools { 
        jdk 'java17'
        maven 'maven3'
    }

    environment {
        APPNAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "nrshub"
        DOCKER_TOKEN = credentials('dockerhub-token')  // ⚠️ For security, store this in Jenkins credentials instead
        IMAGE_NAME = "${DOCKER_USER}/${APPNAME}"
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
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/porandlanaresh/registration-app.git'
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

        stage("Build & Push Docker Image") {
            steps {
                script {
                    // Build Docker image
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."

                    // Login to Docker Hub using username and token
                    sh "echo ${DOCKER_TOKEN} | docker login -u ${DOCKER_USER} --password-stdin"

                    // Push the image
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
                    sh "docker push ${IMAGE_NAME}:latest"

                    // Optional: Logout
                    sh "docker logout"
                }
            }
        }
    }
}
