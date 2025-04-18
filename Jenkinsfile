pipeline {
    agent any  

    environment {
        DOCKER_REGISTRY = "swapsphere.azurecr.io"
        FRONTEND_IMAGE = "frontend"
        BACKEND_IMAGE = "backend"
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_USER = "swapsphere"
    }

    stages {
        stage('Checkout Code') {
            agent {
                docker {
                    image 'maven:3.8.4-openjdk-17-slim'
                    args '-v /root/.m2'
                }
            }
            steps {
                git credentialsId: 'ea082ca1-b396-4c3b-aa02-420e550914a1',
                    url: 'https://github.com/TapanPatel11/Medinteract',
                    branch: 'main'
            }
        }

        stage('Run Tests') {
            agent {
                docker {
                    image 'maven:3.8.4-openjdk-17-slim'
                    args '-v /root/.m2'
                }
            }
            steps {
                sh 'mvn test'
            }
        }

        stage('Build and Push Docker Images') {
            steps {
                withDockerRegistry(credentialsId: 'ba185353-bbf6-4676-a6fb-f9c41d83d124', url: 'https://swapsphere.azurecr.io') {
                    // Build and Push Frontend Docker Image
                    sh """
                    docker build -t $DOCKER_REGISTRY/$FRONTEND_IMAGE:$IMAGE_TAG -f Dockerfile-frontend .
                    docker push $DOCKER_REGISTRY/$FRONTEND_IMAGE:$IMAGE_TAG
                    """

                    // Build and Push Backend Docker Image
                    sh """
                    docker build -t $DOCKER_REGISTRY/$BACKEND_IMAGE:$IMAGE_TAG -f Dockerfile-backend .
                    docker push $DOCKER_REGISTRY/$BACKEND_IMAGE:$IMAGE_TAG
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Successfully built and pushed Docker images!"
        }
        failure {
            echo "❌ Build or push failed. Check logs."
        }
    }
}
