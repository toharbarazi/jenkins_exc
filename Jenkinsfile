pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'                     // AWS region
        AWS_ACCOUNT_ID = '992382545251'              // Your AWS account ID
        ECR_REPO = 'tohar_jenkins_exc'               // Your ECR repository name
        IMAGE_TAG = 'latest'                         // Image tag
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
                    docker.withRegistry("https://${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com", 'aws-ecr-credentials') {
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
                docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:${IMAGE_TAG}
                """
            }
        }
    }
}

