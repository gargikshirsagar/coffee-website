pipeline {
    agent any

    environment {
        IMAGE_NAME = 'coffee-website'
        CONTAINER_NAME = 'coffee-website-container'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                script {
                    // Install htmlhint globally and run it to lint the HTML files
                    sh 'npm install -g htmlhint'
                    sh 'htmlhint index.html'
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    // Ensure Docker is installed and running
                    sh 'docker --version'
                    
                    // Build Docker image
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    // Stop and remove the existing container (if any)
                    sh "docker ps -q -f name=$CONTAINER_NAME | xargs -r docker stop | xargs -r docker rm"
                    
                    // Run the container with ports mapped
                    sh "docker run -d --name $CONTAINER_NAME -p 8080:80 $IMAGE_NAME"
                }
            }
        }

        stage('Post Build Cleanup') {
            steps {
                script {
                    // Optionally, clean up by stopping and removing the container after the build
                    sh "docker stop $CONTAINER_NAME"
                    sh "docker rm $CONTAINER_NAME"
                }
            }
        }
    }
}
