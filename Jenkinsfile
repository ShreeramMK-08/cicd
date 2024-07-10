pipeline {
    agent any
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        CLUSTER_NAME = 'CICD'
        ECR_REPO_URI = '019848826665.dkr.ecr.ap-south-1.amazonaws.com/cicd'
        IMAGE_TAG = 'latest'
    }
    stages {
        stage('Clone repository') {
            steps {
                git 'https://github.com/ShreeramMK-08/cicd.git'
            }
        }
        stage('Build Docker image') {
            steps {
                script {
                    docker.build("${ECR_REPO_URI}:${IMAGE_TAG}")
                }
            }
        }
        stage('Login to ECR') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region $AWS_DEFAULT_REGION | docker login --username AWS --password-stdin $ECR_REPO_URI'
                }
            }
        }
        stage('Push Docker image to ECR') {
            steps {
                script {
                    docker.image("${ECR_REPO_URI}:${IMAGE_TAG}").push()
                }
            }
        }
        stage('Configure kubectl') {
            steps {
                sh 'aws eks update-kubeconfig --name $CLUSTER_NAME'
            }
        }
        stage('Deploy to EKS') {
            steps {
                script {
                    sh 'sed -i "s|<IMAGE>|${ECR_REPO_URI}:${IMAGE_TAG}|g" deployment.yaml'
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}
