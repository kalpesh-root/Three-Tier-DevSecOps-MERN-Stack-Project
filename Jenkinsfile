pipeline {
  agent any

  tools {
    nodejs 'nodejs'
  }

  environment {
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
          sh 'node -v'
          sh 'npm -v'
          sh 'npm install'
        }
      }
    }

    stage('Install Frontend Dependencies') {
      steps {
        dir('frontend') {
          sh 'npm install'
        }
      }
    }
    stage('SonarQube Scan') {
      steps {
        script {
          def scannerHome = tool 'sonar-scanner'
          withSonarQubeEnv('sonarqube') {
          sh """
            ${scannerHome}/bin/sonar-scanner \
            -Dsonar.projectKey=mern-app \
            -Dsonar.sources=.
          """
      }
    }
  }
}
  stage('Docker Build') {
    steps {
      sh '''
        docker build -t mern-backend:latest backend/
      '''
  }
}

  stage('Trivy Scan') {
    steps {
      sh '''
        docker run --rm \
          -v /var/run/docker.sock:/var/run/docker.sock \
          aquasec/trivy:latest image mern-backend:latest
      '''
    }
  }

  }
}
