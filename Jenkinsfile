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
            agent {
                docker {
                    image 'docker:20.10.7'  // Use Docker image for Docker build steps
                    args '--privileged'
                }
            }
            steps {
                script {
        withCredentials([usernamePassword(credentialsId: 'ba185353-bbf6-4676-a6fb-f9c41d83d124', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                        // Login to Docker Registry using safer method
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"


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
