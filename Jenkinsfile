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

  }
}
