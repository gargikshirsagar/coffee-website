pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'gargimk/coffee-website'  // Docker Hub repository
        GIT_REPO = 'https://github.com/gargikshirsagar/coffee-website.git'  // GitHub repository
        IMAGE_NAME = 'coffee-website'  // Docker image name
        IMAGE_TAG = 'latest'  // Docker image tag
    }

    stages {
        // Clone GitHub repository
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${GIT_REPO}"  // Clone the repository
            }
        }

        // Build Docker image
        stage('Build Docker Image') {
            steps {
                script {
                    // Building the Docker image
                    sh 'docker build -t ${DOCKER_HUB_REPO}:${IMAGE_TAG} .'  // Replace with your Docker Hub repo
                }
            }
        }

        // Push Docker image to Docker Hub
        stage('Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub
                    sh 'docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}'  // Replace with your Docker Hub credentials

                    // Push Docker image to Docker Hub
                    sh 'docker push ${DOCKER_HUB_REPO}:${IMAGE_TAG}'
                }
            }
        }

        // Deploy Docker container
        stage('Deploy Docker Container') {
            steps {
                script {
                    // Stop any running containers of the same image
                    sh 'docker stop $(docker ps -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG})'

                    // Remove any old containers
                    sh 'docker rm $(docker ps -a -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG})'

                    // Run new container
                    sh 'docker run -d -p 8082:80 ${DOCKER_HUB_REPO}:${IMAGE_TAG}'  // Adjust port as per your needs
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after build
        }
    }
}
