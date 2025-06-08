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
                withSonarQubeEnv('Sonarqube') { // Nom de l'installation SonarQube dans Jenkins
                    sh './gradlew sonar'
                }
            }
        }

        stage('Static Code Analysis') {
            steps {
                echo "Running optional static code analysis step"
                // Intégré à Sonar, ce stage peut être enrichi si nécessaire
            }
        }

        stage('Build and Push Image') {
            steps {
                echo "Building and pushing Docker image with version: ${params.VERSION}"

                withCredentials([usernamePassword(
                    credentialsId: 'DockerHub Token', // Attention au nom exact
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

        stage('Update values.yaml') {
            steps {
                echo "Updating Helm values.yaml with new Docker tag"
                sh """
                    sed -i 's/tag: .*/tag: ${params.VERSION}/' helm/app/values.yaml
                    git config user.email "jenkins@example.com"
                    git config user.name "Jenkins CI"
                    git commit -am "Update Docker tag to ${params.VERSION}"
                    git push origin main
                """
            }
        }
    }
}
 
