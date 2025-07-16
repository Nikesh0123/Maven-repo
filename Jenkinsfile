pipeline {
    agent any

    // 🤖 Add user-defined input parameters
    parameters {
        string(name: 'BRANCH', defaultValue: 'main', description: 'Git branch to build')
        string(name: 'PROJECT_KEY', defaultValue: 'my-simple-project', description: 'SonarQube project key')
        booleanParam(name: 'RUN_QUALITY_GATE', defaultValue: true, description: 'Fail build if Quality Gate fails?')
    }

    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "🔀 Checking out branch: ${params.BRANCH}"
                git branch: "${params.BRANCH}", url: 'https://github.com/Nikesh0123/Maven-repo.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=${params.PROJECT_KEY}"
                }
            }
        }

        stage('Quality Gate') {
            when {
                expression { return params.RUN_QUALITY_GATE }
            }
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate(abortPipeline: true)
                }
            }
        }
    }
