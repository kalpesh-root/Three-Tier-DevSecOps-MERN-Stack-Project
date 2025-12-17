pipeline {
  agent any

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

    stage('SonarQube Scan - Backend') {
      steps {
        withSonarQubeEnv('sonarqube') {
          dir('backend') {
            sh '''
              sonar-scanner \
                -Dsonar.projectKey=mern-backend \
                -Dsonar.sources=.
            '''
          }
        }
      }
    }

    stage('SonarQube Scan - Frontend') {
      steps {
        withSonarQubeEnv('sonarqube') {
          dir('frontend') {
            sh '''
              sonar-scanner \
                -Dsonar.projectKey=mern-frontend \
                -Dsonar.sources=.
            '''
          }
        }
      }
    }

  }   // stages

}     // pipeline
