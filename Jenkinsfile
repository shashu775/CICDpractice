pipeline {
    agent { label 'docker'}

        stages('Build Docker Image') {
            steps {
                sh 'docker build -t my-app .'
            }
        }

        stages('Verify Image') {
            steps {
                sh 'docker images'
            }
        }
    
}
