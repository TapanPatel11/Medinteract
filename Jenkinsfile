pipeline {
    agent none  // No default agent, use specific agents for each stage

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
                    image 'maven:3.8.4-openjdk-17-slim'  // Use Maven image for this stage
                    args '-v /root/.m2'  // Persist maven cache to avoid re-downloading dependencies
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
            agent {
                docker {
                    image 'docker:20.10.7'  // Use Docker image for Docker build steps
                    args '--privileged'  // Allow Docker to build Docker images in the container
                }
            }
            steps {
                script {

                    withCredentials([string(credentialsId: '57879b0a-5739-40b0-9598-a9fe0300d795', variable: 'docker-pwd')]) {
                    // Build and Push Frontend Docker Image
                    sh """
                    docker login -u $DOCKER_USER -p ${docker-pwd}
                    """
                    
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
