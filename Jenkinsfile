pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'coffee-website'
    }
    
    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/gargikshirsagar/coffee-website.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Run Tests') {
            steps {
                // You can add any tests here, for now, it's skipped
                echo 'Running tests (if any)...'
            }
        }

        stage('Deploy to Docker') {
            steps {
                script {
                    // Run the Docker container
                    sh 'docker run -d -p 8082:80 $DOCKER_IMAGE'
                }
            }
        }

        stage('Notify') {
            steps {
                echo 'Deployment completed successfully.'
            }
        }
    }
}
