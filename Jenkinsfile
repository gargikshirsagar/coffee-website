pipeline {
    agent any

    environment {
        IMAGE_NAME = 'coffee-website'
        CONTAINER_NAME = 'coffee-website-container'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the repository code
                checkout scm
            }
        }

        stage('Test') {
            steps {
                script {
                    // Install htmlhint globally and run it to lint the HTML files
                    echo "Installing htmlhint and running lint on index.html"
                    sh 'npm install -g htmlhint'
                    sh 'htmlhint index.html'
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    // Ensure Docker is installed and running
                    echo "Checking Docker version"
                    sh 'docker --version'
                    
                    // Build Docker image
                    echo "Building Docker image: $IMAGE_NAME"
                    sh "docker build -t $IMAGE_NAME ."
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    echo "Stopping and removing any existing container named $CONTAINER_NAME"
                    // Stop and remove the existing container (if any)
                    sh "docker ps -q -f name=$CONTAINER_NAME | xargs -r docker stop | xargs -r docker rm"
                    
                    echo "Running container $CONTAINER_NAME from image $IMAGE_NAME"
                    // Run the container with ports mapped
                    sh "docker run -d --name $CONTAINER_NAME -p 8080:80 $IMAGE_NAME"
                }
            }
        }

        stage('Post Build Cleanup') {
            steps {
                script {
                    echo "Cleaning up: stopping and removing the container $CONTAINER_NAME"
                    // Optionally, clean up by stopping and removing the container after the build
                    sh "docker stop $CONTAINER_NAME"
                    sh "docker rm $CONTAINER_NAME"
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace after build, regardless of success or failure
            echo "Cleaning up the workspace..."
            cleanWs()
        }
    }
}
