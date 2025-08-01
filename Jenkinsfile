pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Building Static Site...'
        // If you have an actual build step like npm or hugo, add it here
      }
    }
    stage('Deploy') {
      steps {
        sh 'cp -r * /usr/share/nginx/html/' // example for NGINX container
      }
    }
  }
}