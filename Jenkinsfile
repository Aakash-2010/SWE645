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
                    url: 'https://github.com/Aakash-2010/SWE645_EC2.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                    docker build -t ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG} .
                    """
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'dockerhub-creds', url: '') {
                        sh """
                        docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Update Kubernetes Deployment') {
 	   steps {
		script {
         		sh '''
           		# Replace placeholder Docker image in deployment.yaml
            		sed -i 's|skye20/swe645:latest|'skye20/swe645:lastest|g' k8s/deployment.yaml

	            	# Apply Deployment
        		kubectl apply -f k8s/deployment.yaml

            		# Apply Service
            		kubectl apply -f k8s/service.yaml
            		'''
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
