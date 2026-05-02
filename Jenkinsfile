pipeline {
    agent { label 'docker' }

    environment {
        IMAGE_TAG    = "${BUILD_NUMBER}"
        ECR_REGISTRY = "398934907029.dkr.ecr.us-east-1.amazonaws.com"
        ECR_REPO     = "my-nginx-app"
        AWS_REGION   = "us-east-1"
        ECS_CLUSTER  = "frontend"
        ECS_SERVICE  = "shashwat-web-service-3g5o86u0"
    }

    stages {

        stage('Build Docker image') {
            steps {
                bat 'cd three-tier-architecture-aws/frontend && docker build -t mynginx:latest .'
            }
        }

        stage('Verify image') {
            steps {
                bat 'docker images'
            }
        }

        stage('AWS login') {
            steps {
                bat 'aws ecr get-login-password --region %AWS_REGION% | docker login --username AWS --password-stdin %ECR_REGISTRY%'
            }
        }

        stage('Tag Docker image') {
            steps {
                // Tag with build number  ← this is what was missing
                bat 'docker tag mynginx:latest %ECR_REGISTRY%/%ECR_REPO%:%IMAGE_TAG%'

                // Also tag as latest
                bat 'docker tag mynginx:latest %ECR_REGISTRY%/%ECR_REPO%:latest'
            }
        }

        stage('Push Docker image') {
            steps {
                // Push build number tag
                bat 'docker push %ECR_REGISTRY%/%ECR_REPO%:%IMAGE_TAG%'

                // Push latest tag
                bat 'docker push %ECR_REGISTRY%/%ECR_REPO%:latest'
            }
        }

        stage('Update ECS task definition') {
            steps {
                // Register new task def revision pointing to exact build tag
                bat """
                    FOR /F "tokens=*" %%i IN ('aws ecs describe-task-definition --task-definition shashwat-web --region %AWS_REGION% --query taskDefinition --output json') DO SET TASK_DEF=%%i
                """
                bat """
                    aws ecs register-task-definition ^
                        --region %AWS_REGION% ^
                        --family shashwat-web ^
                        --container-definitions "[{\\"name\\":\\"shashwat-app\\",\\"image\\":\\"%ECR_REGISTRY%/%ECR_REPO%:%IMAGE_TAG%\\"}]"
                """
            }
        }

        stage('Deploy to ECS') {
            steps {
                bat """
                    aws ecs update-service ^
                        --cluster %ECS_CLUSTER% ^
                        --service %ECS_SERVICE% ^
                        --force-new-deployment ^
                        --region %AWS_REGION%
                """
            }
        }
    }

    post {
        success {
            echo "Build ${BUILD_NUMBER} deployed successfully to ECS"
        }
        failure {
            echo "Build ${BUILD_NUMBER} failed"
        }
    }
}
