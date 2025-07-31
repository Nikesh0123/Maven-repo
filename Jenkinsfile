pipeline {
  agent any

  tools {
    // This should match the Maven version name configured in Jenkins > Global Tool Configuration
    maven 'Maven 3'
  }

  environment {
    // Replace with your correct branch and configured SonarQube instance name
    GIT_BRANCH = 'nikesh.developer'
    SONARQUBE_ENV = 'Your-SonarQube-Instance-Name'
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

    stage('Build JAR') {
      steps {
        sh 'mvn clean package'
      }
    }
  }

  post {
    success {
      echo '✅ Build and SonarQube analysis completed successfully.'
    }
    failure {
      echo '❌ Build failed. Please check the console output for errors.'
    }
  }
}
