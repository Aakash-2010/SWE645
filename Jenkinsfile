pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "skye20"
        IMAGE_NAME     = "swe645"
        IMAGE_TAG      = "${BUILD_NUMBER}"
    }

    stages {
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

        stage('Update Kubernetes Deployment') {
            steps {
                sh """
                sed -i 's|skye20/swe645:.*|${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}|g' k8s/deployment.yaml
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                """
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
