pipeline {
  agent any

  tools {
    maven 'Maven 3'
  }

  environment {
    GIT_BRANCH = 'nikesh.developer'
    SONARQUBE_ENV = 'SonarQube'  // <-- This should match exactly with Jenkins config
  }

  stages {
    stage('Checkout Code') {
      steps {
        git branch: "${env.GIT_BRANCH}", url: 'https://github.com/Nikesh0123/Maven-repo.git'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv("${env.SONARQUBE_ENV}") {
          sh 'mvn clean verify sonar:sonar'
        }
      }
    }

    stage('Build & Archive Artifact') {
      steps {
        sh 'mvn clean package'
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }
  }

  post {
    success {
      echo 'Build and analysis completed successfully.'
    }
    failure {
      echo 'Build failed. Please check the logs.'
    }
  }
}
