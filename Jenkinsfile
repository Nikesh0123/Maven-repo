pipeline {
  agent any

  tools {
    maven 'Maven 3'  // This must match the Maven installation name in Jenkins
  }

  environment {
    GIT_BRANCH = 'nikesh.developer'
    SONARQUBE_ENV = 'SonarQube'  // This must match the Name in SonarQube Jenkins config
  }

  stages {
    stage('Checkout Code') {
      steps {
        // This sets env.BRANCH_NAME automatically
        checkout([$class: 'GitSCM',
          branches: [[name: "${env.GIT_BRANCH}"]],
          userRemoteConfigs: [[url: 'https://github.com/Nikesh0123/Maven-repo.git']]
        ])
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv("${env.SONARQUBE_ENV}") {
          sh """
            mvn clean verify sonar:sonar \
            -Dsonar.projectKey=hello-world \
            -Dsonar.branch.name=${env.GIT_BRANCH}
          """
        }
      }
    }

    stage('Build & Archive Artifact') {
      steps {
        sh 'mvn clean package'
        archiveArtifacts artifacts: 'target/*.war', fingerprint: true
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
