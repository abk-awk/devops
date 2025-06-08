pipeline {
    agent any

    parameters {
        string(name: 'VERSION', defaultValue: 'v1.0', description: 'Version to build and deploy')
    }

    environment {
        DOCKER_IMAGE = 'abel025/devops'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
                sh 'git checkout -B main'
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
                withSonarQubeEnv('Sonarqube') {
                    sh './gradlew sonar'
                }
            }
        }

        stage('Static Code Analysis') {
            steps {
                echo "Running optional static code analysis step"
            }
        }

        stage('Build and Push Image') {
            steps {
                echo "Building and pushing Docker image with version: ${params.VERSION}"
                withCredentials([usernamePassword(
                    credentialsId: 'DockerHub Token',
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
                // Exemple de commande :
                // sh "kubectl set image deployment/my-app my-container=$DOCKER_IMAGE:$VERSION"
            }
        }

        stage('Update values.yaml') {
            steps {
                echo "Updating Helm values.yaml with new Docker tag"
                withCredentials([string(credentialsId: 'github-push-token', variable: 'GIT_TOKEN')]) {
                    sh """
                        git checkout -B main
                        git pull origin main

                        sed -i 's|name:.*|name: ${DOCKER_IMAGE}|' helm/app/values.yaml
                        sed -i 's|tag:.*|tag: ${params.VERSION}|' helm/app/values.yaml

                        git config user.email "abel.kabangu@2025.icam.fr"
                        git config user.name "Jenkins CI"

                        git commit -am "Update Docker imag
 
