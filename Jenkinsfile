pipeline {
    agent any

    environment {
        SONARQUBE_ENV = 'SonarQubeServer'
        DOCKER_IMAGE = 'abk-awk/devops'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/abk-awk/devops.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Analyse SonarQube') {
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Vérification qualité SonarQube') {
            steps {
                timeout(time: 1, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline terminé avec succès.'
        }
        failure {
            echo '❌ Pipeline échoué.'
        }
    }
}
