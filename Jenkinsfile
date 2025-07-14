pipeline {
  agent any

  environment {
    IMAGE_NAME = "narenkanugu/html-app"
    VERSION = "v1.0.${BUILD_NUMBER}"
  }

  stages {
    stage('Checkout') {
      steps {
        git 'https://github.com/Narendarkanugu/narendarkanugu.github.io.git'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          echo "Building Docker image: $IMAGE_NAME:$VERSION"
          docker build -t $IMAGE_NAME:$VERSION .
          docker tag $IMAGE_NAME:$VERSION $IMAGE_NAME:latest
        '''
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(
          credentialsId: 'dockerhub',
          usernameVariable: 'DOCKER_USER',
          passwordVariable: 'DOCKER_PASS'
        )]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push $IMAGE_NAME:$VERSION
            docker push $IMAGE_NAME:latest
          '''
        }
      }
    }

    stage('Deploy') {
      steps {
        sh '''
          docker rm -f html-app || true
          docker run -d -p 8085:80 --name html-app $IMAGE_NAME:$VERSION
        '''
      }
    }
    stage('Cleanup Old Images') {
  steps {
    sh '''
      echo "🧹 Cleaning up old Docker images for narenkanugu/html-app..."

      IMAGE_NAME="narenkanugu/html-app"

      # List all image IDs (excluding 'latest'), sort by creation date DESC
      IMAGE_IDS=$(docker images --format '{{.Repository}}:{{.Tag}} {{.ID}} {{.CreatedAt}}' \
        | grep "$IMAGE_NAME" \
        | grep -v ":latest" \
        | sort -rk3 | awk '{print $2}')

      echo "Found image IDs:"
      echo "$IMAGE_IDS"

      # Count how many images exist
      TOTAL_IMAGES=$(echo "$IMAGE_IDS" | wc -l)
      echo "Total images: $TOTAL_IMAGES"

      # How many to delete
      DELETE_COUNT=$((TOTAL_IMAGES - 5))

      if [ "$DELETE_COUNT" -gt 0 ]; then
        echo "Deleting $DELETE_COUNT old image(s)..."
        echo "$IMAGE_IDS" | tail -n $DELETE_COUNT | xargs -r docker rmi -f
      else
        echo "Nothing to delete. Less than or equal to 5 images exist."
      fi

      echo "✅ Cleanup complete."
    '''
  }
}


    
  }
}
