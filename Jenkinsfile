pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REGISTRY = '836684609232.dkr.ecr.ap-south-1.amazonaws.com'
        ECR_REPO = 'vote-app'
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh '''
                    echo "🔧 Building Docker image..."
                    docker build -t $ECR_REPO:$IMAGE_TAG .
                    '''
                }
            }
        }

        stage('Login to ECR') {
            steps {
                script {
                    sh '''
                    echo "🔐 Logging into Amazon ECR..."
                    aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY
                    '''
                }
            }
        }

        stage('Tag and Push Docker Image') {
            steps {
                script {
                    sh '''
                    echo "🏷️ Tagging image..."
                    docker tag $ECR_REPO:$IMAGE_TAG $ECR_REGISTRY/$ECR_REPO:$IMAGE_TAG

                    echo "🚀 Pushing image to ECR..."
                    docker push $ECR_REGISTRY/$ECR_REPO:$IMAGE_TAG
                    '''
                }
            }
        }
    }
}
