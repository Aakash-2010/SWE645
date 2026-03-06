pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "skye20"
        IMAGE_NAME     = "swe645"
        IMAGE_TAG      = "${BUILD_NUMBER}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/desaimann37/SWE_645.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-pass', url: '') {
                        sh "docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh """
                    kubectl set image deployment/swe645-deployment \
                    swe645-container=${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                    
                    kubectl rollout status deployment/swe645-deployment
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful! Image tag: ${IMAGE_TAG}"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
