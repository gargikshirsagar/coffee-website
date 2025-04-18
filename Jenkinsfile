pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // This checks out the code from the repository configured in Jenkins
            }
        }

        stage('Test') {
            steps {
                sh 'npm install -g htmlhint || true'
                sh 'htmlhint index.html || true'
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t coffee-website .'
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 8080:80 coffee-website'
            }
        }
    }
}
