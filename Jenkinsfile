pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'himani708/ingress:v1'
        
    }

    stages {

        stage('User Confirmation') {
            steps {
                script {
                    def userInput = input(
                        id: 'userConfirm',
                        message: 'Do you want to build this project?',
                        parameters: [choice(name: 'CONFIRM', choices: ['Yes', 'No'], description: 'Select Yes to proceed or No to abort')]
                    )
                    if (userInput == 'No') {
                        echo "🚫 Build aborted by user."
                        currentBuild.result = 'ABORTED'
                        error("User chose not to proceed.")
                    }
                }
            }
        }

        stage('Select Branch') {
            steps {
                script {
                    def branchInput = input(
                        id: 'branchSelect',
                        message: 'Select the branch to build:',
                        parameters: [string(name: 'BRANCH', defaultValue: 'main', description: 'Enter the branch name to build')]
                    )
                    env.BRANCH_NAME = branchInput
                    echo "✅ Selected Branch: ${env.BRANCH_NAME}"
                }
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/him-b/web-app-ingress.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                echo "🔧 Building Docker image..."
                docker build -t $DOCKER_IMAGE .
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh '''
                echo "📦 Pushing image to Docker Hub..."
                docker push $DOCKER_IMAGE
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                echo "🚀 Deploying to Kubernetes..."
                microk8s.kubectl apply -f deploy.yaml
                microk8s.kubectl apply -f service.yml
                microk8s.kubectl apply -f ingress.yml
                echo "Waiting for pods to stabilize..."
                sleep 20
                microk8s.kubectl get pods
                '''
            }
        }

        
    }

    post {
        success {
            echo '✅ CI/CD pipeline executed successfully. App deployed and accessible via Ingress.'
        }
        failure {
            echo '❌ Build or deploy failed. Please review Jenkins logs.'
        }
        aborted {
            echo '⚠️ Pipeline aborted by user.'
        }
    }
}
