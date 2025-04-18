pipeline {
    agent any
    
    environment {
        GIT_DIR = 'coffee-website' // Explicitly mention the folder
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Ensure git commands are executed inside the workspace
                    dir("${GIT_DIR}") {
                        checkout scm
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo "Running tests"
                    sh 'npm install -g htmlhint'
                    sh 'htmlhint index.html'
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    echo "Building Docker Image"
                    sh 'docker build -t coffee-website .'
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    echo "Running Docker container"
                    sh "docker run -d -p 8080:80 --name coffee-website-container coffee-website"
                }
            }
        }
    }
}
