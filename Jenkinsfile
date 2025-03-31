pipeline {
  agent none
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
  stages {
        stage('Checkout'){
           steps {
                git credentialsId: 'ea082ca1-b396-4c3b-aa02-420e550914a1', 
                url: 'https://github.com/TapanPatel11/Medinteract',
                branch: 'main'
           }
        }
    stage('Build Frontend Docker') {
      agent {
        any
      }
      steps {
                    sh '''
                    echo 'Buid Frontend Docker Image'
                    docker build -t frontend:${BUILD_NUMBER} ./Dockerfile-frontend
                    '''      }
    }
    stage('Build Backend Docker Image') {
      agent {
        any
      }
      steps {
                    sh '''
                    echo 'Buid Backend Docker Image'
                    docker build -t backend:${BUILD_NUMBER} ./Dockerfile-backend
                    '''      }
    }
  }
}