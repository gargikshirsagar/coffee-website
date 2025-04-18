pipeline {
    agent any

    environment {
        IMAGE_NAME = 'coffee-website'
        CONTAINER_NAME = 'coffee-website-container'
        GIT_REPO_URL = 'https://github.com/gargikshirsagar/coffee-website.git' // Your Git repository URL
        GIT_BRANCH = 'main' // Your desired branch
    }

    stages {
        // Step 1: Clone the repository manually
        stage('Manual Git Checkout') {
            steps {
                script {
                    echo "Cloning repository manually"
                    // Clone the repository
                    sh 'git clone $GIT_REPO_URL'
                    
                    // Move to the repository folder (if necessary)
                    dir('coffee-website') {
                        // Check Git status to see if it's correct
                        sh 'git status'
                    }
                }
            }
        }

        // Step 2: Test HTML code
        stage('Test') {
            steps {
                script {
                    echo "Running HTMLLint test"
                    sh 'npm install -g htmlhint'
                    sh 'htmlhint index.html'
                }
            }
        }

        // Step 3: Build Docker image
        stage('Build Image') {
            steps {
                script {
                    echo "Building Docker image"
                    sh 'docker build -t $IMAGE_NAME .'
                }
            }
        }

        // Step 4: Run Docker container
        stage('Run Container') {
            steps {
                script {
                    echo "Running Docker container"
                    sh "docker run -d -p 8080:80 --name $CONTAINER_NAME $IMAGE_NAME"
                }
            }
        }

        // Step 5: Clean up the Docker container after the build
        stage('Post Build Cleanup') {
            steps {
                script {
                    echo "Cleaning up by stopping and removing the container"
                    sh "docker stop $CONTAINER_NAME"
                    sh "docker rm $CONTAINER_NAME"
                }
            }
        }
    }

    // Step 6: Clean up the workspace after the pipeline finishes
    post {
        always {
            echo "Cleaning up the workspace..."
            cleanWs()
        }
    }
}
