pipeline {
    agent any

    environment {
        IMAGE_NAME = "amarjeet001/static-web"
        IMAGE_TAG  = "build-${BUILD_NUMBER}"
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

        stage('Docker Login & Push Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat "docker login -u %DOCKER_USER% -p %DOCKER_PASS%"
                    bat "docker push %IMAGE_NAME%:%IMAGE_TAG%"
                    bat "docker push %IMAGE_NAME%:latest"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-creds', variable: 'KUBECONFIG')]) {

                    // Verify cluster connection
                    bat "kubectl get nodes"

                    // Apply Deployment YAML
                    bat "kubectl apply -f k8s/deployment.yml"

                    // Apply Service YAML
                    bat "kubectl apply -f k8s/service.yml"

                    // Update image dynamically
                    bat "kubectl set image deployment/static-web static-web=%IMAGE_NAME%:%IMAGE_TAG%"

                    // Wait for rollout
                    bat "kubectl rollout status deployment/static-web"
                }
            }
        }
    }

    post {
        success {
            echo "Deployment Successful 🚀"
        }
        failure {
            echo "Deployment Failed ❌"
        }
    }
}
