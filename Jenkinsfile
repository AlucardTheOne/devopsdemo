pipeline {
  agent any

  environment {
    IMAGE_NAME = 'alucardtheone/my-nginx-image'
    IMAGE_TAG = 'latest'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Validate Config') {
      steps {
        sh 'docker run --rm -v $PWD/nginx.conf:/etc/nginx/nginx.conf nginx:alpine nginx -t'
      }
    }

    stage('Build Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
      }
    }

    stage('Push to Docker Hub') {
      when {
        branch 'main'
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push $IMAGE_NAME:$IMAGE_TAG
          '''
        }
      }
    }

    stage('Deploy (optional)') {
      when {
        branch 'main'
      }
      steps {
        echo "Deploying $IMAGE_NAME:$IMAGE_TAG to server..."
        // Add real deploy logic here
      }
    }
  }

  post {
    always {
      cleanWs()
    }
  }
}