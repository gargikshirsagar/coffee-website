pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'gargimk/coffee-website'
        GIT_REPO = 'https://github.com/gargikshirsagar/coffee-website.git'
        IMAGE_NAME = 'coffee-website'
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_HUB_REPO}:${IMAGE_TAG} ."
                }
            }
        }

        stage('Login & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                        sh "docker push ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                    }
                }
            }
        }

        stage('Release Port 8082') {
            steps {
                script {
                    // Check and kill the process using port 8082
                    sh 'fuser -k 8082/tcp || true'
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    // Stop and remove any existing container using the same image
                    sh "docker stop \$(docker ps -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"
                    sh "docker rm \$(docker ps -a -q --filter ancestor=${DOCKER_HUB_REPO}:${IMAGE_TAG}) || true"
                    // Run the new container on port 8083 to avoid conflicts
                    sh "docker run -d -p 8083:80 ${DOCKER_HUB_REPO}:${IMAGE_TAG}"
                }
            }
        }

        stage('Test Website Accessibility') {
            steps {
                script {
                    sleep(10)  // Wait for 10 seconds to ensure the container is fully up
                    sh 'curl -I http://localhost:8083'  // Test if the site is accessible on port 8083
                }
            }
        }

        stage('Check for Broken Links') {
            steps {
                sh 'npm install -g linkinator'  // Install linkinator globally
                sh 'linkinator http://localhost:8083'  // Check for broken links on the new port
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
