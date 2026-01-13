pipeline {
  agent any

  tools {
    nodejs 'nodejs'
  }

  environment {
    AWS_REGION    = 'ap-south-1'
    ECR_REGISTRY  = '475834860148.dkr.ecr.ap-south-1.amazonaws.com'
    IMAGE_NAME    = 'mern-backend'
    SONARQUBE_ENV = 'sonarqube'
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main',
            url: 'https://github.com/kalpesh-root/Three-Tier-DevSecOps-MERN-Stack-Project.git'
      }
    }

    stage('Install Backend Dependencies') {
      steps {
        dir('backend') {
          sh '''
            node -v
            npm -v
            npm install
          '''
        }
      }
    }

    stage('SonarQube Scan') {
      steps {
        script {
          def scannerHome = tool 'sonar-scanner'
          withSonarQubeEnv("${SONARQUBE_ENV}") {
            sh """
              ${scannerHome}/bin/sonar-scanner \
              -Dsonar.projectKey=mern-backend \
              -Dsonar.sources=backend
            """
          }
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 2, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }

    stage('Docker Build') {
      steps {
        sh '''
          docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} backend/
        '''
      }
    }

    stage('Trivy Scan (Image Gate)') {
      steps {
        sh '''
          docker run --rm \
            -v /var/run/docker.sock:/var/run/docker.sock \
            aquasec/trivy:latest image \
            --severity HIGH,CRITICAL \
            --exit-code 1 \
            ${IMAGE_NAME}:${BUILD_NUMBER}
        '''
      }
    }

    stage('Build & Push Backend Image') {
      steps {
        sh '''
          aws ecr get-login-password --region ${AWS_REGION} \
          | docker login --username AWS --password-stdin ${ECR_REGISTRY}

          docker tag ${IMAGE_NAME}:${BUILD_NUMBER} \
          ${ECR_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}

          docker push ${ECR_REGISTRY}/${IMAGE_NAME}:${BUILD_NUMBER}
        '''
      }
    }

    stage('Update GitOps Repo') {
      steps {
        withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
          sh '''
            rm -rf mern-gitops-argocd
            git clone https://github.com/kalpesh-root/mern-gitops-argocd.git
            cd mern-gitops-argocd

            sed -i "s|image: .*mern-backend:.*|image: ${ECR_REGISTRY}/mern-backend:${BUILD_NUMBER}|" backend/deployment.yaml

            git config user.email "jenkins@local"
            git config user.name "jenkins"

            git add backend/deployment.yaml
            git commit -m "Update backend image to ${BUILD_NUMBER}"
            git push https://kalpesh-root:${GITHUB_TOKEN}@github.com/kalpesh-root/mern-gitops-argocd.git main
          '''
        }
      }
    }
  }

  post {
    always {
      cleanWs()
    }
    failure {
      echo "Pipeline failed (Sonar or Trivy gate)."
    }
    success {
      echo "Pipeline completed successfully."
    }
  }
}
