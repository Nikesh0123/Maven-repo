pipeline {
  agent any

  tools {
    maven 'maven3' // if using Java/Maven project
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: "${env.BRANCH_NAME}", url: 'https://github.com/Nikesh0123/Maven-repo.git'
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('Your-SonarQube-Instance-Name') {
          sh 'mvn sonar:sonar'
        }
      }
    }

    stage('Build Artifact') {
      when {
        expression {
          return currentBuild.currentResult == 'SUCCESS'
        }
      }
      steps {
        sh 'mvn clean package'
        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
      }
    }
  }
}
