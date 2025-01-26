'pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'  // e.g., 'us-east-1'
        AWS_ACCOUNT_ID = '992382545251'
        ECR_REPO = 'tohar_jenkins_exc'     // Replace with your ECR repository name
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://992382545251.dkr.ecr.us-east-1.amazonaws.com", 'aws-ecr-credentials') {
                        sh """
                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                        docker build -t ${ECR_REPO}:${IMAGE_TAG} .
                        docker tag ${ECR_REPO}:${IMAGE_TAG} ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh """
                docker push <aws_account_id>.dkr.ecr.$AWS_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG
                """
            }
        }
    }
}
