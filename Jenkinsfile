pipeline {
    agent any

    environment {
        // Aapka DockerHub username aur image ka naam
        IMAGE_NAME = "amarjeet001/static-web"
        // Jenkins har baar naya Build Number (55, 56, etc.) automatic tag kar dega
        IMAGE_TAG  = "build-${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Aapka GitHub repository link
                git branch: 'main', url: 'https://github.com/amarjeetchaurasiya27-cyber/k8s-static-web-pipline.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Windows par %IMAGE_NAME% format use hota hai variable ke liye
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
                bat "docker tag %IMAGE_NAME%:%IMAGE_TAG% %IMAGE_NAME%:latest"
            }
        }

        stage('Docker Login & Push Image') {
            steps {
                // Isme 'dockerhub-creds' aapka DockerHub wala ID hona chahiye
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
                /* Yahan 'docker-desktop-k8s' ki jagah wahi ID likhein jo aapne 
                   Secret File upload karte waqt Jenkins mein di thi.
                */
                withKubeConfig([credentialsId: 'k8s-config']) {

                    // Cluster se connection check karein
                    bat "kubectl get nodes"

                    // Deployment aur Service apply karein
                    bat "kubectl apply -f k8s/deployment.yml"
                    bat "kubectl apply -f k8s/service.yml"

                    // Naya image (build-55, build-56...) Kubernetes mein update karein
                    bat "kubectl set image deployment/static-web static-web=%IMAGE_NAME%:%IMAGE_TAG%"

                    // Rollout check karein ki deployment sahi se hui ya nahi
                    bat "kubectl rollout status deployment/static-web"
                }
            }
        }
    }

    post {
        success {
            echo "Badhai ho! Deployment Successful 🚀 (Tag: ${IMAGE_TAG})"
        }
        failure {
            echo "Galti ho gayi! Deployment Failed ❌"
        }
    }
}
