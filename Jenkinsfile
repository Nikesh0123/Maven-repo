pipeline {
    agent any

    environment {
        SONARQUBE = credentials('sonar') // Optional if token-based auth
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/your-user/hello-world.git', branch: 'nikesh.developer'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=hello-world'
                }
            }
        }

        stage('Build & Archive Artifact') {
            steps {
                sh 'mvn clean package'
                archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
            }
        }
    }

    post {
        failure {
            echo '❌ Build failed. Check logs for errors.'
        }
        success {
            echo '✅ Build succeeded.'
        }
    }
}
