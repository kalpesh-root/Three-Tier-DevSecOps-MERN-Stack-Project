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
        git branch: 'main', url: 'https://github.com/kalpesh-root/Three-Tier-DevSecOps-MERN-Stack-Project.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('SonarQube Scan') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh '''
          sonar-scanner \
          -Dsonar.projectKey=mern-app \
          -Dsonar.sources=. \
          '''
        }
      }
    }
  }
}
