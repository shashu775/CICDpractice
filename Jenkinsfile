pipeline {
    agent { label 'docker'}

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
