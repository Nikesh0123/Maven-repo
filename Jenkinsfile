pipeline {
  agent any

  tools {
    maven 'Maven 3'  // must match Jenkins global installation
  }

  environment {
    GIT_BRANCH = 'nikesh.developer'
    SONARQUBE_ENV = 'SonarQube'  // must match the SonarQube instance name in Jenkins System Configuration
  }

  stages {
    stage('Checkout Code') {
      steps {
        git branch: "${env.GIT_BRANCH}",
            url: 'https://github.com/Nikesh0123/Maven-repo.git'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv("${env.SONARQUBE_ENV}") {
          sh 'mvn clean verify sonar:sonar'  // environment variables SONAR_HOST_URL and SONAR_AUTH_TOKEN injected
        }
      }
    }

    stage('Build & Archive Artifact') {
      steps {
        sh 'mvn clean package'
        archiveArtifacts artifacts: 'target/*.war', fingerprint: true
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
  }

  post {
    success {
      echo '✅ Build, SonarQube analysis, and quality gate passed.'
    }
    failure {
      echo '❌ Build failed or quality gate did not pass.'
    }
  }
}
