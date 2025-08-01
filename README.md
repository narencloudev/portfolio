Your Jenkinsfile is **almost perfect** for building and deploying a static HTML site in Docker. However, you're experiencing issues due to a **branch mismatch**, and there are a few best practices and improvements worth making.

---

### ✅ Let’s Fix the Problems

#### 🔧 1. **Branch Mismatch:**

Right now your `Clone` stage says:

```groovy
git 'https://github.com/narencloudev/portfolio.git'
```

By default, Jenkins assumes the **`master`** branch. But your repo uses `main`.

✅ **Fix it like this:**

```groovy
git branch: 'main', url: 'https://github.com/narencloudev/portfolio.git'
```

---

#### ✅ Updated Jenkinsfile with Improvements:

```groovy
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
        echo "🔨 Building Docker image..."
        sh "docker build -t ${IMAGE_NAME}:latest ."
      }
    }

    stage('Stop Previous Container') {
      steps {
        echo "🛑 Stopping any previous container..."
        sh """
          docker stop ${CONTAINER_NAME} || true
          docker rm ${CONTAINER_NAME} || true
        """
      }
    }

    stage('Run New Container') {
      steps {
        echo "🚀 Running new container on port ${HOST_PORT}..."
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
      echo "✅ Deployed successfully: http://localhost:${env.HOST_PORT}"
    }
    failure {
      echo "❌ Deployment failed. Check logs."
    }
  }
}
```

---

### 💡 Optional Improvements

#### 1. **Parameterize your Jenkins job**:

Make branch, port, and image name configurable from Jenkins UI.

#### 2. **Webhooks**:

Use GitHub webhook or polling (`H/1 * * * *`) to auto-trigger builds on commit.

#### 3. **Check Docker is available**:

Ensure Jenkins has Docker access (`docker.sock` is mounted, Jenkins user is in docker group, etc.)

---

### ✅ Next Steps

1. **Update your Jenkinsfile with the above changes**
2. **Re-run the build**
3. Visit your site at [http://localhost:9091](http://localhost:9091)

Would you like me to help you set up webhooks or a multi-branch pipeline next?
