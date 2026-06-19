pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ECR_REGISTRY = "305042756398.dkr.ecr.ap-south-1.amazonaws.com"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION | \
                docker login --username AWS --password-stdin $ECR_REGISTRY
                '''
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                docker build -t streaming-frontend ./frontend
                docker build -t streaming-auth ./backend/authService
                docker build -t streaming-admin -f backend/adminService/Dockerfile ./backend
                docker build -t streaming-chat -f backend/chatService/Dockerfile ./backend
                docker build -t streaming-service -f backend/streamingService/Dockerfile ./backend
                '''
            }
        }

        stage('Tag Images') {
            steps {
                sh '''
                docker tag streaming-frontend $ECR_REGISTRY/streaming-frontend:latest
                docker tag streaming-auth $ECR_REGISTRY/streaming-auth:latest
                docker tag streaming-admin $ECR_REGISTRY/streaming-admin:latest
                docker tag streaming-chat $ECR_REGISTRY/streaming-chat:latest
                docker tag streaming-service $ECR_REGISTRY/streaming-service:latest
                '''
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker push $ECR_REGISTRY/streaming-frontend:latest
                docker push $ECR_REGISTRY/streaming-auth:latest
                docker push $ECR_REGISTRY/streaming-admin:latest
                docker push $ECR_REGISTRY/streaming-chat:latest
                docker push $ECR_REGISTRY/streaming-service:latest
                '''
            }
        }
    }
}