pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = 'YOUR_AWS_ACCOUNT_ID'
        ECR_REPOSITORY = 'image-regi'
        IMAGE_NAME = 'cloud-image'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
        ECR_IMAGE = "${ECR_REGISTRY}/${ECR_REPOSITORY}:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build and Test') {
            steps {
                bat 'python -m py_compile app.py'
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('ECR Login') {
            steps {
                bat 'aws ecr get-login-password --region %AWS_REGION% | docker login --username AWS --password-stdin %ECR_REGISTRY%'
            }
        }

        stage('Docker Tag') {
            steps {
                bat 'docker tag %IMAGE_NAME%:latest %ECR_IMAGE%'
            }
        }

        stage('Push to ECR') {
            steps {
                bat 'docker push %ECR_IMAGE%'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}