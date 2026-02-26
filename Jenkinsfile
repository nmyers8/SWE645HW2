pipeline {
    agent any
    environment {
        DOCKERHUB_USER = 'nmyers8'
        IMAGE_NAME = 'static-web-app'
        IMAGE_TAG = 'latest'
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1', 'dockerhub-creds') {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Update Kubernetes Deployment') {
            steps {
                script {
                    sh """
                    sed -i 's|nmyers8/static-web-app:latest|${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}|' k8s/deployment.yaml
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                    """
                }
            }
        }
    }
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}