pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'sonarqube'              // Must match Jenkins config
        PROJECT_KEY   = 'my-simple-project'      // SonarQube project key
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/Nikesh0123/Maven-repo.git', branch: 'main'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                dir('my-app') { // change this to the folder where pom.xml is located
                    withSonarQubeEnv("${SONARQUBE_ENV}") {
                        sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=${PROJECT_KEY}'
                    }
                }
            }
        }
    }
}
