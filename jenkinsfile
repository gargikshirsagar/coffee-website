pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/gargikshirsagar/coffee-website.git'
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
