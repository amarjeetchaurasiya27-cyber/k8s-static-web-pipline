pipeline {
    agent any

    environment {
        IMAGE_NAME = "amarjeet001/static-web:55"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/amarjeetchaurasiya27-cyber/k8s-static-web-pipline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%BUILD_NUMBER% ."
                bat "docker tag %IMAGE_NAME%:%BUILD_NUMBER% %IMAGE_NAME%:latest"
            }
        }

        stage('Push Image') {
            steps {
                bat "docker login -u YOUR_DOCKER_USERNAME -p YOUR_DOCKER_PASSWORD"
                bat "docker push %IMAGE_NAME%:%BUILD_NUMBER%"
                bat "docker push %IMAGE_NAME%:latest"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat "kubectl apply -f k8s/deployment.yml"
                bat "kubectl apply -f k8s/service.yml"
            }
        }
    }
}
