pipeline {
  agent any

  tools {
    maven 'Maven 3'  // This must match the Maven installation name in Jenkins
  }

  environment {
    SONARQUBE_ENV = 'SonarQube'  // Match this to the configured SonarQube server name in Jenkins
  }

  stages {
    stage('Checkout Code') {
      steps {
        // In multibranch, Jenkins already checks out the branch — no need for manual checkout
        echo "🚀 Building branch: ${env.BRANCH_NAME}"
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv("${env.SONARQUBE_ENV}") {
          sh """
            mvn clean verify sonar:sonar \
              -Dsonar.projectKey=hello-world \
              -Dsonar.branch.name=${env.BRANCH_NAME}
          """
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
      echo '✅ Build and SonarQube analysis completed successfully.'
    }
    failure {
      echo '❌ Build failed. Check logs for errors.'
    }
  }
}
