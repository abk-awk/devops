pipeline {
    agent any

    parameters {
        string(name: 'VERSION', defaultValue: 'v1.0', description: 'Version to build and deploy')
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Checkout') {
            steps {
                echo "Performing additional checkout operations if needed"
                // Exemple : checkout depuis un autre repo
                // git url: 'https://github.com/example/another-repo.git'
            }
        }

        stage('Build and Test') {
            steps {
                echo "Building the project and running tests..."
                sh './gradlew build test' // ou 'mvn test', etc.
            }
        }

        stage('Static Code Analysis') {
            steps {
                echo "Running static code analysis tools"
                // Exemples : SonarQube, Checkstyle, etc.
                // sh './gradlew sonarqube'
            }
        }

        stage('Build and Push Image') {
            steps {
                echo "Building and pushing Docker image with version: ${params.VERSION}"
                sh """
                docker build -t my-image:${params.VERSION} .
                docker tag my-image:${params.VERSION} my-registry/my-image:${params.VERSION}
                docker push my-registry/my-image:${params.VERSION}
                """
            }
        }

        stage('Update Deployment') {
            steps {
                echo "Updating deployment to use image version: ${params.VERSION}"
                // Exemple avec kubectl
                sh """
                kubectl set image deployment/my-app my-container=my-registry/my-image:${params.VERSION}
                """
            }
        }
    }
}
 
