
pipeline {
    agent any

    environment {
        REPO_NAME = 'vote-app'
        AWS_ACCOUNT_ID = '836684609232'
        AWS_REGION = 'ap-south-1'
        IMAGE_TAG = "v1.${BUILD_NUMBER}"
        ECR_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${REPO_NAME}"
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${REPO_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin ${ECR_URI}
                """
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                    docker tag ${REPO_NAME}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}
                    docker push ${ECR_URI}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh """
                    aws ecs update-service \
                      --cluster your-cluster-name \
                      --service your-service-name \
                      --force-new-deployment
                """
            }
        }
    }
}
