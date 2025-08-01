pipeline {
  agent any

  environment {
    IMAGE_NAME = "narencloudev/static-site"
    CONTAINER_NAME = "static-html"
    HOST_PORT = "9091"
  }

  stages {
    stage('Clone') {
      steps {
        git branch: 'main', url: 'https://github.com/narencloudev/portfolio.git'
      }
    }

    stage('Build Image') {
      steps {
        sh "docker build -t ${IMAGE_NAME}:latest ."
      }
    }

    stage('Stop Previous Container') {
      steps {
        sh """
          docker stop ${CONTAINER_NAME} || true
          docker rm ${CONTAINER_NAME} || true
        """
      }
    }

    stage('Run New Container') {
      steps {
        sh """
          docker run -d \
            --name ${CONTAINER_NAME} \
            -p ${HOST_PORT}:80 \
            ${IMAGE_NAME}:latest
        """
      }
    }
  }

  post {
    success {
      echo "Deployed at: http://localhost:${env.HOST_PORT}"
    }
    failure {
      echo "Deployment failed!"
    }
  }
}
