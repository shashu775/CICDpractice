pipeline {
    agent { label 'docker'}

stages {
        stage('Build Docker Image') {
            steps {
                bat 'cd three-tier-architecture-aws/frontend && docker build -t mynginx .'
            }
        }

        stage('Verify Image') {
            steps {
                bat 'docker images'
            }
        }
        stage('AWS login') {
            steps {
                bat 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 398934907029.dkr.ecr.us-east-1.amazonaws.com'
            }
        }
    
        stage('Tag Docker image'){
            steps{
                bat 'docker tag mynginx:latest 398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:latest'
            }
        }
        stage ('Push Docker image'){
            steps{
                bat ' docker push mynginx:latest 398934907029.dkr.ecr.us-east-1.amazonaws.com/my-nginx-app:latest'
            }
        }

  } 
    
}
