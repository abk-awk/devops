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
            }
        }

        stage('Build') {
            steps {
                echo "Building the project..."
                sh './gradlew build'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "Running SonarQube analysis..."
                withSonarQubeEnv('Sonarqube') { // Ce nom doit correspondre à ta config Jenkins
                    sh './gradlew sonarqube'
                }
            }
        }

        stage('Static Code Analysis') {
            steps {
                echo "Placeholder: Static analysis step (optional if Sonar used)"
            }
        }

        stage('Build and Push Image') {
            steps {
                echo "Building and pushing Docker image with version: ${params.VERSION}"

                withCredentials([usernamePassword(
                    credentialsId: 'DokcerHub Token',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker build -t $DOCKER_USER/devops:$VERSION .
                        docker push $DOCKER_USER/devops:$VERSION
                    '''
                }
            }
        }

        stage('Update Deployment') {
            steps {
                echo "Updating deployment to use image version: ${params.VERSION}"
                // Exemple : sh "kubectl set image deployment/my-app my-container=$DOCKER_USER/devops:$VERSION"
            }
        }
    }
}
 
