pipeline {
    agent { label 'docker'}

stages {
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t my-app .'
            }
        }

        stage('Verify Image') {
            steps {
                bat 'docker images'
            }
        }
    
}
