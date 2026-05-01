pipeline {
    agent { label 'docker'}

stages {
        stage('Build Docker Image') {
            steps {
                bat 'cd three-tier-architecture-aws/frontend && docker build -t my-app .'
            }
        }

        stage('Verify Image') {
            steps {
                bat 'docker images'
            }
        }
  } 
    
}
