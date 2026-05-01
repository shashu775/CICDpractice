pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/shashu775/CICDpractice.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-app .'
            }
        }

        stage('Verify Image') {
            steps {
                sh 'docker images'
            }
        }
    }
}
