pipeline {
    agent any

    environment {
        IMAGE_NAME = "amarjeet001/static-web"
        IMAGE_TAG = "55"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/amarjeetchaurasiya27-cyber/k8s-static-web-pipline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
                bat "docker tag %IMAGE_NAME%:%IMAGE_TAG% %IMAGE_NAME%:latest"
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
                bat "docker push %IMAGE_NAME%:latest"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
