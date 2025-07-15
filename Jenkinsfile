pipeline {
  agent any
  parameters {
    string(name: 'SONAR_HOST', defaultValue: 'http://13.233.1.171:9000', description: 'SonarQube server URL')
    string(name: 'PROJECT_KEY', defaultValue: 'my-simple-project', description: 'SonarQube project key')
  }
  environment {
    JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
    PATH = "${JAVA_HOME}/bin:${env.PATH}"
  }
  stages {
    stage('Checkout') {
      steps {
        git branch: "${params.BRANCH ?: 'main'}", url: 'https://github.com/Nikesh0123/Maven-repo.git'
      }
    }
    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh """
            mvn clean verify sonar:sonar \
              -Dsonar.projectKey=${params.PROJECT_KEY} \
              -Dsonar.host.url=${params.SONAR_HOST}
          """
        }
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
}
