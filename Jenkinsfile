pipeline {
    agent any

    tools {
        maven 'Maven 3' // Jenkins Maven installation name
    }

    environment {
        GIT_BRANCH       = 'main'
        SONARQUBE_ENV    = 'SonarQube'     // Jenkins SonarQube config name
        NEXUS_URL        = 'http://13.201.126.35:30001/repository/maven-releases/'
        NEXUS_CREDENTIALS = 'nexus-cred' // Jenkins credentials ID for Nexus
        DOCKER_REGISTRY  = 'nexus-server:8083' // Nexus Docker registry URL
        DOCKER_IMAGE     = 'myapp'
        DOCKER_TAG       = '1.0'
    }

    stages {

        // Stage 1: Checkout Code
        stage('Checkout Code') {
            steps {
                git branch: "${env.GIT_BRANCH}", url: 'https://github.com/Nikesh0123/Maven-repo.git'
            }
        }

        // Stage 2: SonarQube Analysis
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${env.SONARQUBE_ENV}") {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        // Stage 3: Build & Archive Artifact
        stage('Build & Archive Artifact') {
            steps {
                sh 'mvn clean package'
                archiveArtifacts artifacts: 'target/*.war', fingerprint: true
            }
        }

        // Stage 4: Upload Artifact to Nexus
        stage('Upload to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${env.NEXUS_CREDENTIALS}", usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh """
                        mvn deploy:deploy-file \
                        -Durl=${env.NEXUS_URL} \
                        -DrepositoryId=nexus \
                        -Dfile=target/*.war \
                        -DgroupId=com.example \
                        -DartifactId=${env.DOCKER_IMAGE} \
                        -Dversion=${env.DOCKER_TAG} \
                        -Dpackaging=war \
                        -Dusername=$NEXUS_USER \
                        -Dpassword=$NEXUS_PASS
                    """
                }
            }
        }

        // Stage 5: Build Docker Image from Nexus Artifact
        stage('Build Docker Image') {
            steps {
                sh """
                    wget --user=${env.NEXUS_USER} --password=${env.NEXUS_PASS} ${env.NEXUS_URL}com/example/${env.DOCKER_IMAGE}/${env.DOCKER_TAG}/${env.DOCKER_IMAGE}-${env.DOCKER_TAG}.war -O app.war
                    docker build -t ${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}:${env.DOCKER_TAG} .
                """
            }
        }

        // Stage 6: Push Docker Image to Nexus Docker Registry
        stage('Push Docker Image') {
            steps {
                sh """
                    echo $NEXUS_PASS | docker login ${env.DOCKER_REGISTRY} -u $NEXUS_USER --password-stdin
                    docker push ${env.DOCKER_REGISTRY}/${env.DOCKER_IMAGE}:${env.DOCKER_TAG}
                """
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline executed successfully. Artifact stored in Nexus and Docker image pushed.'
        }
        failure {
            echo '❌ Pipeline failed. Please check logs for details.'
        }
    }
}
