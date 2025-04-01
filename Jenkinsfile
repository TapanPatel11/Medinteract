pipeline {
  agent any  // Ensures a global agent is available

  environment {
      IMAGE_TAG = BUILD_NUMBER
  }

  stages {
    stage('Checkout') {
      steps {
        git credentialsId: 'ea082ca1-b396-4c3b-aa02-420e550914a1', 
            url: 'https://github.com/TapanPatel11/Medinteract',
            branch: 'main'
      }
    }

    stage('Build Frontend Docker') {
      steps {
        sh '''
          echo 'Building Frontend Docker Image'
          docker build -t frontend:${BUILD_NUMBER} -f Dockerfile-frontend .
        '''
      }
    }

    stage('Build Backend Docker') {
      steps {
        sh '''
          echo 'Building Backend Docker Image'
          docker build -t backend:${BUILD_NUMBER} -f Dockerfile-backend .
        '''
      }
    }
  }
}
